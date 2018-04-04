#Introduction
Each ark <a href="https://github.com/ArkEcosystem/ark-node">relay node</a> 
has a number of api endpoints opened up. Depending on the network, -mainnet,
testnet or a bridgenet-, different ports listen to incoming requests.

These endpoints can be used to find blocks, transactions, delegates, or post
transactions of different types. As creating transactions is relatively
complicated, it is best to use an api wrapper such as Arky to handle 
the creation of the transaction for you.

##Javascript
Ark-JS is a JavaScript library for sending ARK transactions. It does not wrap 
the different api endpoints used for querying the blockchain. We recommend you use 
ark-ts to leverage all the advantages of typescript.


##Python
Our verbose python api client of choice is Arky. It can handle all endpoints, generate transactions and
has some lower level operations available through the crypto package. 
Don't try to create transactions yourself using these unless you know what 
you are doing. 

If you are looking for something more lightweight, check out pythark (a wrapper on top of Arky)
or Python-Ark-Client, which uses the ark-js library for building transactions.

##Go
Ark-GO is the ARK Ecosystem library client implemented in GOLANG programming language. 
It implements all most relevant ARK functionalities to help you develop efficient, 
fast and scalable GOLANG applications built upon ARK platform. It provides also 
low level access to ARK so you can easily build your application on top of it.

##.NET
Ark.NET is the ARK Ecosystem library for the .NET platform. It implements all most 
relevant ARK functionalities to help you develop efficient .NET applications built 
upon the ARK platform. 
The package supports Windows Desktop applications, Mono Desktop applications, 
and platform supported at .NET Standard Library. 
Should work will all .NET solutions with framework > 4.5.2.

##Java
There are two libraries you can use when building on the JVM, Ark-kotlin and ark-java.
The first is written in Kotlin and fully interoperable with Java & Android as well.