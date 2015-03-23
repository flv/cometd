Documentation entry page for Bayeux Cometd

# Bayeux Cometd Introduction #

Cometd is a project by the [Dojo Foundation](http://dojotoolkit.org/foundation) to produce a specification (Bayeux) and a set of implementations of that specification (Cometd).


## Bayeux Overview ##

The primary purpose of Bayeux is to implement responsive user interactions for
web clients using Ajax and the server-push technique called Comet.

Initially the Bayeux specification provides only a [protocol definition](http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html), but eventually it is planned to include standardized APIs for servers and clients as part of the specification.

#### Bayeux Protocol ####

The [Bayeux Specification](http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html) is written in RFC style and is aimed at defining the interoperability aspects of cometd project. The protocol itself is open to extension and encourages multiple transport implementations.

#### Bayeux APIs ####

There is currently a proposed Java server side [dojox.cometd](https://svn.codehaus.org/jetty-contrib/jetty/trunk/contrib/cometd/api) API.  It has been implemented by the Jetty server and is under review/consideration by other java servers.



## Cometd Overview ##

The cometd project includes implementations of Bayeux in several languages, toolkits and servers. Third party implementations of Bayeux also exist, and are not strictly part of the cometd project, however for completeness the third party implementations are linked off this document where appropriate.



# Using Bayeux #

This section contains material that is applicable to all Bayeux implementations.

## Bayeux Features ##
  * BayeuxChannels
  * BayeuxStates
  * BayeuxFilters
  * BayeuxExtensions

## Use Cases ##
  * BayeuxChatExample
  * BayeuxBroadcast
  * BayeuxNarrowcast
  * BayeuxRpc


# Using Cometd (and other implementations) #

This section contains material that is applicable to specific Bayeux implementations,
in most part the implementations are part of the cometd project, but for completeness, non-cometd implementations are included aswell


## Cometd Clients ##

#### Javascript Clients ####
  * CometdDojo
  * CometdJquery
  * CometdExtJs

#### Java Clients ####
  * CometdJetty

#### Flex/Flash Clients ####
  * [flexcomet](http://code.google.com/p/flexcomet/)

## Cometd Servers ##

#### Java Servers ####
  * CometdJetty
  * TomcatBayeux ?
  * BeaBayeux ?
  * [IBM Websphere](http://www-1.ibm.com/support/docview.wss?uid=isg1PK57246)

#### Perl Servers ####
  * CometdPerl

#### Twisted Python Servers ####
  * CometdTwisted
