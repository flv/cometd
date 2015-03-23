# Introduction #

IRC meeting minutes 2006-06-21


# Agenda #
  * The Bayeux Spec ( http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html  )
  * Comet Implementation white paper
  * The state of each implementation
  * Unified unit tests
  * Dojo Board nominations
  * Infrastructure

# Spec #

  * servers SHOULD optionally accept publish requests without clientID and should apply server specific authentication mechanisms to verify the requests
  * remove status
  * remove ping
  * defined errors - markn to produce  a list


# white paper #

  1. a problem description
  1. some fundamentals of how Cometd reduces deployment complexity
  1. a discussion of how to integrate auth
  1. cross-domain and integration w/ other infrastructure (JMS, etc.)

# implementation status #

  * python in trouble
  * perl/sprocket on cpan - alpha state
  * java pretty much complete.  working on "standard" server side API.
  * dojo version is still lacking globbing and full advice support.
  * xantus is working on a firefox extension

# unified tests #

  * Xantus will write in perl.
  * start test case descriptions on wiki

# board nominations #

  * xantus and gregw nominated

# infrastructure #

  * asking dojo project for svn, drupal, trac, www

# IRC log (missing some minutes at end) #

```

<xantus> hi mark
<markn> hi xantus
<slightlyoff> heya xantus 
<gregw> hi all
<xantus> hi everyone
<xantus> Topics for today
<xantus> * The Bayeux Spec ( http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html  )
<xantus> * Comet Implementation white paper
<xantus> * The state of each implementation
<xantus> * Unified unit tests
<xantus> * Dojo Board nominations
<xantus> * Infrastructure
* kentam has joined #cometd
* LordVorp has joined #cometd
<gregw> So what do we need to do, to get to a 1.0 of the spec?
<xantus> I think there's some todo's and unfinished areas
<xantus> I want to suggest transfering a copy the spec to the wiki and have people change it there
<xantus> s/the/of the/
* buu has joined #cometd
<buu> STALK STALK.
<xantus> if everyone on the google code project?
* xantus stabs buu
<slightlyoff> xantus: I think I'm not
<slightlyoff> oof, I suck
<gregw> xantus - I'm cautious about moving it to the wiki
<gregw> I don't think our limit is ease of edits
<xantus> remember all edits are sent to the list
<gregw> I think our limit is deciding the last few issues
<xantus> yeah
<slightlyoff> agreed
* avinash240 has joined #cometd
<gregw> eg. do we need a status request/response?
<gregw> and my worry about the wiki is more formatting than erroneous content :-)
<xantus> haha yeah
<markn> the wiki editor is a pain,  at least for me
<markn> or code.google's is
<gregw> scanning through the spec, I see the following issues:
* dorian_ has joined #cometd
<gregw> 1. official support for commented json
<gregw> 2. status or not to status. and if we do what is it
<gregw> 3. supported connection types
<gregw> might just be missing a blurb for iframe etc.
<gregw> or do we want to specify iframe
<gregw> or is that the implementations whitepaper?
<gregw> 4. some blah blah blahs for blurbage
<avinash240> what exactly does cometd do? and where does it sit in the client/server/middleware tier?
<slightlyoff> avinash240: 3 tier is so 1998 = )
<xantus> lets answer that after we get everything else taken care of
<avinash240> xantus: you told me to ask damnit.. >: [ but fine.
<gregw> avinash240: we are having an IM meeting
<xantus> stick around
<xantus> :)
<gregw> so we'll answer you in due course
* avinash240 sticking
<gregw> Do we want to deal with those spec issues now?
<kentam> what about unconnected operations?
<gregw> kentam: explain? 
<xantus> gregw: I think we should try
<slightlyoff> kentam: you mean things that haven't done handshake?
<kentam> (i sent mail about unconnected publish events to the dev list)
<xantus> as long as we have the audience
<kentam> yes, e.g. publish w/o handshake
<slightlyoff> kentam: we've said for a while that we need to support pre-auth
<gregw> slightlyoff: but have since removed all auth!
<slightlyoff> kentam: so that if you pass a valid auth token, you don't need to explicitly handshake ahead of time
<slightlyoff> gregw: oof, good point
<xantus> we didn't remove auth keys did we?
<gregw> yes - just said do it in ext section
<xantus> s/keys/token/
<gregw> why specify fields if we leave them undefined
<xantus> nah, the token is in
<gregw> clientId is in
<gregw> but no authToken
<xantus> just optional
<gregw> and in ext
<kentam> the current spec doesn't appear to have any auth related fields that aren't deprecated
<slightlyoff> so a pre-shared clientId?
<gregw> So perhaps it is the same... unconnected sends can happen so long as whatever auth is implemented is satisfied
<gregw> or no clientId?
<kentam> (advance apologies for lack of background) so is clientId needed or not for publish?  right now spec says yes
<gregw> well it is needed... at the moment
<gregw> but maybe we can allow a publish with out it (optionally)
<gregw> and say that the server must authenticate the request by what ever means it wants
<slightlyoff> well, we could also just say that servers should pre-negotiate long-lived client IDs for this kind of hting
<gregw> but then we have to say that servers preserve those over restarts
<gregw> also clientId are more session tokens than long lived secrets
<gregw> kentam: what's the use case to avoid handshake again?
<slightlyoff> gregw: big server farm, lots of events, one event router
<xantus> the clientId should be preshared with the token
<slightlyoff> xantus: that seems right
<xantus> usually they are generated together
<slightlyoff> xantus: so in your config file for your server you'd have a set of mappings
<slightlyoff> xantus: w/, potentially, a list of topics that id is allowed to post to
<xantus> in my case, I use the client id, and a secret key to generate the token
<xantus> depends on use case, so that'll be covered in the whitepaper
<gregw> So at the very least, we need something like connected=false in a handshake. So we can create clientIds that the server does not timeout
<gregw> if reconnects are not received
<gregw> but then we are in DOS issues
<gregw> if somebody creates a million clientIds
<slightlyoff> gregw: well, I'd like to avoid that so we can drop things that don't match
<kentam> i didn't have a specific use case in mind that _needed_ it, but do have situations where trusted server-side publishers come and go and just needed to know whether handshake was needed for each
<xantus> well, I wouldn't store the clientIds
<xantus> just unless they have mappings
<gregw> kentam: jetty server side at a least does not need a client
<gregw> not sure if that is legal
<xantus> so if they do have a key, then i guess they could dos
<kentam> gregw: i think it does now?
<gregw> I think it doesn't 
<gregw> but I could be wrong
<gregw> could we just leave it open.  and say something like:
<gregw> servers SHOULD optionally accept publish requests without clientID and should apply server specifica authentication mechanisms to verify the requests
<xantus> Oh, one more thing on the topic list
<xantus> * Sprocket FF plugin
<kentam> gregw: there is an "if (client==null) { unknownClient(..); return; }" block in PublishHandler
<gregw> but not via the server API
<gregw> that is for over the wire publishes
<kentam> ah, right
<xantus> well, that sort of thing isn't on the public end
<gregw> and I may make that optional, depending on what we decide here
<gregw> So is there a problem with the text I proposed above?
<kentam> grewg: i see what you meant now by "jetty server side" -- I thought you meant the jetty java implementation as a whole
<xantus> ok, where are we now
<slightlyoff> gregw: I don't see a problem with that
<gregw> well it is the dojox.cometd API.... which we should also talk about
<slightlyoff> gregw: the python server will probably just look for something in ext
<gregw> exactly
<gregw> and if we don't specify it - we have security through obscurity!  works for M$
<gregw> :-)
<slightlyoff> oh please, they stole that from Apple in like 1994
<slightlyoff> = )
<markn> gregw:   text as you have it above looks good to me
<gregw> If there are no objections.. I'll update the publish section and ad a section call unconnected  operations
<kentam> +1
<slightlyoff> +1
<markn> +1
<xantus> +1
<gregw> gees we are going all apache!
<gregw> consider it done
<gregw> So what about status then?
<xantus> um, refresh me
<gregw> as it is in the spec... there is a request and response, but little content
<gregw> [
<gregw>   {
<gregw>      "channel": "/meta/status",
<gregw>      "clientId": "Un1q31d3nt1f13rï¿½,
<gregw>      "ext":  {"org.dojo.bayeux.status": {"serverTopics"} }
<gregw>    }
<gregw> ]
<gregw> response:
<slightlyoff> gregw: no fillibuster -1 = )
<gregw> [
<gregw>   {
<gregw>      "channel": "/meta/status",
<gregw>      "clientId": "Un1q31d3nt1f13rï¿½,
<gregw>      "ext":  {"org.dojo.bayeux.status": {"serverTopics": {"topic1", "topic2", "topic3"} } }
<gregw>    }
<gregw> ]
<gregw> -0
<gregw> +0
<gregw> So it is really just a way to exchange ext fields
<xantus> hm
<xantus> Do we need it
<gregw> if it is just ext fields - then it would be better off being called ping
<gregw> somebody might need it
<gregw> it all depends on what is put in the ext
<gregw> but they could always invent their own channel to exchange messages on
<gregw> I'm not sure it is needed from a protocol point of view
<xantus> yeah, thats what I'm leaning to
<xantus> If we're going to add status it should give useful stats on the connection
<gregw> which is?
<gregw> list of subscriptions?
<xantus> what channels subscribed, um, possibly more
<gregw> in case the client forgot !!!!
<xantus> heh, perhaps
<gregw> you could do uptime, and numbers of channels, but that really management stuff
<xantus> avg trip time
<xantus> um, yeah, management
<xantus> ok, lets drop it then?
<gregw> +1
<xantus> +1
* agc has joined #cometd
<xantus> tick tock, we have other topics
<gregw> OK to finish spec
<gregw> I will have a new shiny version today
<markn> +1, don't see much of a difference between in the protocol doc or server specific impl
<gregw> with no red bits
<xantus> supported connection types
<markn> grammer/spelling/minor clarifications is the only other thing
<markn> I can think of
<markn> I do wonder if we might need to return an error code in addition to error message 
<markn> I can post my thoughts on cometd-dev once I think it through
<xantus> I'd prefer an constant error set
<xantus> a list of error #'s a suggested constant name like EBADAUTHTOKEN and a human readable error message
<slightlyoff> seems sane
<gregw> Maybe the error field can be something lilke
<xantus> and a note that the constants won't be used in the protocol spec, just the numbers and human readable
<gregw> EBADAUTHTOKEN: Bad Auth Token xyz
<xantus> EBADAUTHTOKEN(400): Bad Auth Token
<xantus> I like it
<gregw> Should we adopt the XXX style of http
<gregw> 5xx is fatal server error
<xantus> yeah
<gregw> 4xx is bad request error
<gregw> 3xx is advice like stuff
<xantus> 2xx status
<xantus> sure
<gregw> OK - I'll add the section to the spec, but I'll need help making the definitive list of errors
<xantus> who's taking the task of starting the list of errors
<gregw> (I don't like lists) pick the movie reference
<markn> I can start it....  looking through this now
<markn> maybe wiki is good place to start this information
<markn> and let people add
<xantus> Ok, cool. post the list of errors to the group
<xantus> markn: yeah, sounds good
<xantus> slightlyoff: you are on the google project as: slightlyoff
<slightlyoff> xantus: yeah, cool
<xantus> looks like mark, greg, you and I
<xantus> as project owners
<xantus> ok, next up
<xantus> * Comet Implementation white paper
<xantus> someone mentioned starting one
<xantus> are they here?
<xantus> If not then we'll start our own
<slightlyoff> I didn't realize anyone had started on it
<xantus> Ok, maybe they just asked about it
<gregw> what should it contain?
<xantus> I'm on vacation next week, so I'll start one
<markn> did we answer the iframe implementation question at beginning?  I think iframe implementation should be in whitepaper
<slightlyoff> gregw: I think the short list is probably:
<slightlyoff> 1.) a problem description
<slightlyoff> 2.) some fundamentals of how Cometd reduces deployment complexity
<slightlyoff> 3.) a discussion of how to integrate auth
<xantus> markn: we could reference the iframe section of the whitepaper in the protocol spec
* kentam has quit IRC (Ping timeout: 360 seconds)
<slightlyoff> 4.) cross-domain and integration w/ other infrastructure (JMS, etc.)
* ybart2 has quit IRC (irc.sloth.org irc.pobox.com)
* slightlyoff has quit IRC (irc.sloth.org irc.pobox.com)
* ybart2 has joined #cometd
* slightlyoff has joined #cometd
<slightlyoff> whoa
<xantus> nice
<xantus> quick split
<xantus>  * The state of each implementation
<slightlyoff> python one is in trouble = \
<xantus> the perl one is still alpha :(
<xantus> the base library is solid though
<xantus> Sprocket is on cpan now
<slightlyoff> oh, congrats
<xantus> its super fast
<gregw> are these in line with the latest version of the spec?
<gregw> ie no connectionID field
<gregw> connect returns without blocking
<gregw> java version is pretty much complete.  Working on "standard" server API
<gregw> would appreciate if other languages at least looked at the dojox.cometd interfaces 
<slightlyoff> gregw: so that's another interesting topic. It seems that you're mirroring the client APis
<slightlyoff> gregw: agreed
<gregw> it is a superset of the client API.  you can do things like filtering on the server side
<xantus> nice
<gregw> dojo version is still lacking globbing and full advice support.
<slightlyoff> yep
<xantus> Oh
<xantus> I'm writing a firefox extension
<gregw> xantus: I know of a commercial effort there
<xantus> I'll make a socket api available to any site
<gregw> I'll hook you up if you want
<xantus> sure
<gregw> st3fan:xantus: hook
<xantus> oh, we have
<gregw> cool
<xantus> I'm having lunch tomorrow or friday with someone
<xantus> but they want something for use IN an extension
<xantus> they can use the code I'm writing
<gregw> Note I think the dojo client can also be used in an extension
<xantus> they'll just need sprocketSocket.js and a cometd wrapper around it
<xantus> gregw: yep, but it uses xmlhttp
<xantus> with a direct socket you'll get true push
<gregw> not through my firewall :-)
<xantus> hehe
* kentam has joined #cometd
<xantus> it will if you go over port 80
<xantus> my http server will suport bidirectional http
<xantus> well, its not true http
<xantus> well, anyway
<gregw> Anyway... I'm running out of time....  next in agenda?
<xantus> the firefox plugin will give any JS same domain socket connections
<xantus> * Unified tests
<xantus> I think we agreed to use perl for the tests
<xantus> I can do that
<gregw> I think the hard part is writing the test-case descriptions
<gregw> shall we start that on the wiki?
<xantus> we'll need a script for each implmentation that'll fire up the server
<xantus> and we'll use sprocket to do the connect and fake http requests
<xantus> so it'll just test cometd protocol stuff
<xantus> as a start
<xantus> * Dojo Board nominations
<xantus> where are we with that
<xantus> * Infrastructure
<gregw> I thought there was a delay in the process?  Alex?
<slightlyoff> gregw: I suck = \
<xantus> mst nominated me
<xantus> I nominate gregw
<gregw> thanks
<xantus> there we go, 2 so far
<gregw> how are nominations going in the rest of Dojo?  long list?
<slightlyoff> gregw: pretty long
<slightlyoff> gregw: more than the 4 open seats = )
<xantus> haha
<xantus> how many dojo projects are there
<xantus> it makes sense to have at least 1 of each project
<xantus> but thats just a suggestion
<gregw> I'd see it as unlikely that the board would have 2 cometd members
<gregw> xantus - are you much involved with the rest of dojo?
<xantus> no :(
<gregw> ditto here
<xantus> too many projects already
<gregw> ditto here:  but I am trying to get dojo commit 
<gregw> so I can work on cometd.js directly
<xantus> bribe alex
<xantus> :)
<slightlyoff> heh
<slightlyoff> he is
<slightlyoff> with patches
<xantus> of cash!
<xantus> :P
<slightlyoff> he knows my weak spot = )
<gregw> Well I think the key thing is to get one of us onto the board
<xantus> I have a cometd.js library for archetype started
<gregw> for inclusion in dojo?
<xantus> nah, its based on sixapart's js
<slightlyoff> gregw: there's going to be one person elected by the project leads
<xantus> which is open source
<slightlyoff> gregw: separate from the main elections
<xantus> although smaller than dojo :)
<gregw> slightlyoff: I don't understand?
<slightlyoff> gregw: 4 of the 5 seats are "open election" seats
<slightlyoff> gregw: the 5th is elected by the project leads
<xantus> ah
<gregw> and how many projects are there?  is dijit etc considered different projects now?
<slightlyoff> gregw: no, it's not
<slightlyoff> gregw: stil 3
<slightlyoff> so that group is xantus, myself, and Brian Skinner (open record)
<gregw> And the open voters are the project committers?
<slightlyoff> gregw: yes, all committers across all projects
<xantus> does a lead get 2 votes
<gregw> and when is the vote?
<slightlyoff> gregw: probably after I recover from Foo Camp = \
<slightlyoff> gregw: unless you want to help organize
<gregw> OK - well I'm happy to do it, and happy of David does it, so guess we let the great unwashed decide :-)
<gregw> not organize that is
<gregw> be on the board
<gregw> then I'll help organize things :-)
<gregw> or not
<gregw> * Infrastructure ?
<slightlyoff> (sorry, now in the Dojo meeting)
<gregw> Ah - ask them if we can have infrastructure?
<slightlyoff> yes, yes we can
<slightlyoff> we want:
<gregw> svn
```
< slightlyoff> SVN, drupal, trac?
< gregw> www
< slightlyoff> and root so we can experiment
< gregw> NB. I'm minuting this in the wiki http://code.google.com/p/cometd/wiki/WikiMinutes20070621
< slightlyoff> so we want a system image w/ that stuff, right?
< gregw> +1
< slightlyoff> will we do our own web? or do we want drupal?
< gregw> I think the best is a combination
< gregw> custom home page - pointing into drupal
< gregw> with some live demos as part of the custom bit
<@xantus> +1
<@xantus> I'll be available to lock svn its copied over
< slightlyoff> ok
<@xantus> s/its/when its/
<@xantus> bleh
< slightlyoff> I'm gonna ask sitepen for some resources
< slightlyoff> and failing that, i"m just going to pay for a Rimu image out of my own pocket
<@xantus> well, its for the project shouldn't it come out of the coffers
<@xantus> sprinkle a comma somewhere in there
<@xantus> I'm a bit out of it today
< slightlyoff> xantus: yeah, but the coffers are tiny = )
<@xantus> I have a xen instance
<@xantus> @ cometd.net
<@xantus> I use it for coding at testing, we can use that
<@xantus> i'm renting it from mark smith
<@xantus> I have root so I can give everyone accounts
<@xantus> its in a colo too, not a someone's house
< slightlyoff> what's the rent on it?
< slightlyoff> 'cause I can probably just get the foundation to pay for that
<@xantus> $15-20 a month
< slightlyoff> oh, that's nothing
<@xantus> hehe, it can go up if we request more memory
<@xantus> but its cheap
< slightlyoff> and it's on backups?
<@xantus> I probably owe mark like 6 months of back pay
<@xantus> on ups?
<@xantus> yeah, at a colo
< slightlyoff> no, data backups (disk snapshots or whatever)
<@xantus> disk backup, no, I backup to dreamhost
<@xantus> the cometd svn is at dreamhost which has backups
< slightlyoff> ...who is only rooted **sometimes" ;-)**

....later...

< gregw> xantus: slighltyoff: markn: whoever: updated version of the spec has been checked in
<@xantus> woot

