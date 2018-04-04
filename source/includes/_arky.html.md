#Arky

An ark api wrapper by Moustikitos for pythonistas.

```commandline
sudo apt get python-dev3.X
pip3 install arky
```

Arky 1.X.X is capable of connecting to the ledger nano s, and thus has some
extra package requirements. To install these packages, you will need
the development version of python.

We recommend using python 3.6-dev. Then simply pip3 install arky.

<aside class="notice">
Even though python-dev ought to install all need C headers and dependencies, sometimes errors
occur. Try the following if you get this error: command 'x86_64-linux-gnu-gcc failed with exit status 1`
    
    <ul>
    <li>sudo apt-get install gcc</li>
    <li>sudo apt install libudev-dev libusb-1.0-0-dev</li>
    </ul>
    
</aside>

##Connecting to a peer

```python
import arky.rest
arky.rest.use("ark") 
```

> the contents of the ark.net file which are loaded when arky.rest.use is invoked.

```json
{
  "timeout": 5,
  "begintime": [2017, 3, 21, 13],
  "endpoints": "ark",
  "familly": "ark",
  "maxlimit": 50,
  "maxvotepertx": 1,
  "compressed": true,
  "wif": "aa",
  "marker": "17",
  "slip44": "111",
  "bip32": {
    "private": 76066276,
    "public": 76067358
  },
  "headers" : {
		"Content-Type" : "application/json; charset=utf-8",
    "port": "4001"
	},
  "port": 4001,
  "peers": [
    "5.39.9.240",
    "5.39.9.241",
    "5.39.9.242",
    "5.39.9.243",
    "5.39.9.244",
    "5.39.9.250",
    "5.39.9.251",
    "5.39.9.252",
    "5.39.9.253",
    "5.39.9.254",
    "5.39.9.255"
  ],
  "broadcast": 10,
  
  "explorer" : "https://explorer.ark.io",
  "token" : "ark",
	"symbol" : "Ѧ",
	"delegate": 51,
  "blocktime": 8,
  "blockreward": 2
}
```

>After creating your own .net file, you can use it like you would use the mainnet or devnet.

```python
arky.rest.use("mytestnetwork")
# You can also override the peers.
arky.rest.cfg.PEERS=['mypeer1', 'mypeer2']
```

Arky performs an automatic peer selection for you based on the information
provided by seed peers. 


This will work fine for mainnet (ark) and devnet (dark). However for a custom
network you will have to add a .net file for Arky to use to initialize the client.
.net files are stored in the directory where you installed Arky, inside the net directory.
For example inside `python3/lib/site-packages/arky/net/ark.net`


If you have created a custom network, edit the marker, header.port, header, and peers. 
Marker is the hexadecimal representation of the first letter of the addressing, the rest is optional. 
You can now connect to your private network. After initialization you can override the peer by setting the `cfg.PEERS` to a new IP.

Use this link to find the correct value for the marker value: 
<a href="https://bitcoin.stackexchange.com/questions/36623/what-version-byte-will-produce-a-base58-encoding-starting-with-x">link</a>

##Querying

>Querying is easy! Simply follow the same scheme as the rest endpoints. 

```python
# the general querying scheme looks like this:
# arky.rest.[METHOD].[endpoints with "/" replaced by "."](param=value, ...[returnKey=name])


# http equivalent [PEER ADDRESS]/api/delegates/get?username=arky
arky.rest.GET.api.delegates.get(username="arky")
```
>Our response looks like this.

```json
  {  
   "delegate":{  
      "productivity":99.22
   },
   "producedblocks":42283,
   "approval":1.06,
   "rate":19,
   "publicKey":"030da05984d579395ce276c0dd6ca0a60140a3c3d964423a04e7abe110d60a15e9",
   "username":"arky",
   "vote":"137484978342696",
   "address":"ARfDVWZ7Zwkox3ZXtMQQY1HYSANMB88vWE",
   "missedblocks":334
},
"success":True
}
```
After initialization, Arky will have dynamically created some modules and classes. Don't
worry if your IDE tells you that modules are missing.


##Creating a transaction

>Now comes the cool stuff. Creating transactions is easy with a wrapper.

```python
import arky.rest
arky.rest.use("dark")

USER_SECRET = "one two three four five six seven eight nine ten eleven twelve"
MASTER_SECRET = "matter mother draft wear joke media brain build month second such invite"

user = arky.core.crypto.getKeys(USER_SECRET)
PUB, PRIV, WIF = user["publicKey"], user["privateKey"], user["wif"]

ADR = arky.core.crypto.getAddress(PUB)

transaction = arky.core.sendToken(amount=1000000000, recipientId=ADR,
      secret=MASTER_SECRET)
```

>If you had added some print statements, you'd see this.

```commandline
PUB:     0309d164ec345776d7b902cbe27bf16a3d6fe234618d3963e906e66ee424f9982e
PRIV:    aba2570815169d04bc0b454c3e107cbce15af228ca43707937637f25e738ab2b
WIF:     cTLLSVesJSmrNjAr5h7CPmsxDpULWaPvV9vMXWqQ2k66BMGmx5ev
ADR:     DNzRmkeUYp9LHbiHgn77iNRoabtHFAYRb9
```

```json
{  
   "success":"100.0%",
   "transactions":[  
      "224a270cc4ee1ced57168527487d3bb51ab43021f94ec2a3526e0f3b7974884f"
   ],
   "messages":[]
}
```

<aside class="warning">
Never reveal your private key or secret to anyone. Ownership of coins or data on a 
blockchain is solely determined by whomever has possesion of the private key.
</aside>

Ark has constant transaction fees at the moment, however in V2 you will 
be paying a fee per byte. 

Even though your wallet will display Ark/tokens in complete numbers, 
actually a single token consists of 10⁸ satoshis. The standard network fee
is 10⁷ arktoshi, or 0.1 token. The transaction fee is automatically added to
the amount you wish to send, so sending 1 Ark over the api endpoint means 
that the wallet will lose 1.1 Ark. The desktop client (graphical user interface) 
conveniently displays how much Ark is actually removed, if you require a
sanity check.

Read more on address generation in the following section. 

Notice the success percentage. Arky will try to send the transaction to multiple nodes,
this decreases the odds of a bad peer going down right after receiving a transaction.

The master wallet is sending Devnet Ark (dark) to the user wallet. All we need is the user address
and the master secret. We procedurally generate the address from the user secret by way of public key.

Even if a transactionId is returned, the transaction is only permanently added if it is incorporated in the 
blockchain. This should happen approximately 8 seconds after sending the transaction.

Arky broadcasts the transaction to multiple peers. The success percentage relays the amount of 
peers that successfully received the transaction.

<aside class="warning">
Even though you can be reasonably sure a transaction will be added to the blockchain after receiving
a success from the peers, it is only finalized if it is actually in a block. Read more on double 
spend attacks <a href="https://en.bitcoin.it/wiki/Irreversible_Transactions">here</a>
</aside>

##Generating an address
>Addresses are procedurally generated. No need to register an account somewhere!

```python
import arky.rest
arky.rest.use("dark")

secret = "some random gibberish, not necessarily english words, but whatever you want."

keyRing = arky.core.crypto.getKeys(secret)
```

> Our output:

```json
    {  
   "publicKey":"038fd0a57a752da3c7d57b65546e1a98d66d23af3978047b526febd108f873a19d",
   "privateKey":"5f3ac9757a8beb807f19ddd51830191bc38d7e6d780fc37dc4ed43ebe9fab883",
   "wif":"cQmpFxuRp66iggeRRXF295LQoZyeLZicNiNmQimuTnJhpGZgQFF3"
}
```

```python
address = arky.core.crypto.getAddress(keyRing["publicKey"])
```

>A valid devnet address starts with a D.

```commandline
DFbRTxKpkemtY7wd3RCoNDEpedGdETqx9g
```

>Lets check the account info.

```python
arky.rest.GET.api.accounts(address=address)
```

>No luck, apparently the network doesn't have a copy of the details.

```json
  {
    "success": false, 
    "error": "Account not found"
    }
```

>Lets send the address some tokens and then query the details again.

```python
res = arky.core.sendToken(amount=1000000000, recipientId=address,
      secret="matter mother draft wear joke media brain build month second such invite")
```

>Success!

```json
{
    "success": true, 
    "account": {
            "address": "DFbRTxKpkemtY7wd3RCoNDEpedGdETqx9g", 
            "unconfirmedBalance": "1000000000", 
            "balance": "1000000000", 
            "publicKey": null, 
            "unconfirmedSignature": 0, 
            "secondSignature": 0, 
            "secondPublicKey": null, 
            "multisignatures": [], 
            "u_multisignatures": []
            }
    }
```

>No public key though. Why?

```python
import time
res = arky.core.sendToken(amount=1000000000, recipientId=address,
      secret=secret)
time.sleep(8)
res = arky.rest.GET.api.accounts(address=address)
```

>That's better.

```json
{
    "success": true, 
    "account": {
            "address": "DFbRTxKpkemtY7wd3RCoNDEpedGdETqx9g", 
            "unconfirmedBalance": "1000000000", 
            "balance": "1000000000", 
            "publicKey": "038fd0a57a752da3c7d57b65546e1a98d66d23af3978047b526febd108f873a19d", 
            "unconfirmedSignature": 0, 
            "secondSignature": 0, 
            "secondPublicKey": null, 
            "multisignatures": [], 
            "u_multisignatures": []
            }
    }
```

Addresses are procedurally generated from a secret. You do not need to register an account or address through
the API. Actually any address of the correct format will be accepted as recipient in a transaction, meaning
that it is possible to send tokens to an address of which the secret is unknown. Once an address has received 
tokens, it will be queryable from the api.

The keyRing is a dictionary containing the privateKey, publicKey and wif.
from the publicKey we can then generate an address. This is a valid address to which we can send tokens. Were you to query the api for this address right now, you wouldn't
receive its balance, as the network is not aware the address exists. Only once it has a balance does it show up.

If we send some tokens to the address and then query again, we get a succesful response. However the publickey is not 
returned At the moment we are looking at a cold wallet. The network has never received the public key. It is not possible
to derive a public key from the address. Only once an address has sent a transaction does it reveal its public key to
the network.