# 2007-04-04 Meeting 2:00 PST, 5:00 EST #

**IRC location: #cometd on MAGnet  (irc.perl.org)**

## Outcome ##
  * Globbing issue resolved and now in protocol doc.  Copied doc info here:
```
 A set of channels may be specified with a channel globbing pattern:

channel_pattern  = *( "/" channel_segment ) "/" wild_card
wild_card = "*" | "**"

The channel patterns support only trailing wildcards of either "*" to match a single 
segment or "**" to match multiple segments. Example channel patterns are:

/foo/*
    Matches /foo/bar and /foo/boo. Does not match /foo, /foobar or /foo/bar/boo.
/foo/**
    Matches /foo/bar, /foo/boo and /foo/bar/boo. Does not match /foo, /foobar or /foobar/boo 
```

  * Extension mechanism.   Agreed to add section to protocol doc.  Info copied from doc for reference
```
An ext field MAY be included in any bayeux message. It's value SHOULD be a JSON map with top level names distinguished by 
implementation names (eg. "org.dojo.bayeux.field"). The contents of ext may be arbitrary values that allow extensions to
 be negotiated and implemented between implementations.
```

  * Security - Agreed on a few items
    1. Outline how auth works at a higher level than Bayeux
    1. Outline how negotation can take place with authSuccessful flag, advice mechanism (go away, reconnect, reauth,etc) and using the ext field for any auth information.

  * Version number.  Definition has changed and doc updated.  Info copied from doc to here:
```
A protocol version is a integer followed by an optional "." separated sequence of alphanumeric elements:

version         = integer *( "." version_element )
version_element = alphanum *( alphanum | "-" | "_" )

versions are compared element by element, applying normal alphanumeric comparison to each element.
```

  * ConnectionID:  Agreed to remove connectionID and keep use just clientID.

  * Outline recommended way to deal with multi window/multi tab situations.  See Multi frame operation section of document for information.


## Action Items ##
  * Help fill in [Protocol doc](http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html) sections.  Anybody/everybody that can help is welcome.
  * Much thanks to Greg for current state of doc, including many recent updates after today's meeting
  * Security section outcome from today's needs added to protocol doc.

## Agenda ##

_**Status from previous meetings**_
  * [Protocol doc progress](http://svn.xantus.org/shortbus/trunk/bayeux/bayeux.html)
  * Test Suite Update  (2-28 meeting mst)


_**Possible agenda items**_


_Globbing_
> Channels are currently based on URI format without parameters.
```
channel_name  = "/"  path_segments
path_segments = segment *( "/" segment )
segment       = *pchar
pchar         = alphanum | mark
```

> Globbing was left out of spec but there is renewed interest.  Listed below are some general goals for globbing
    1. No matching of partial topic names `/foo*` does not match `/foobar`
    1. Need at least "this or below" matching   `/foo/*` would match /foo/bar and `/foo/bar/asdf` and possibly "this and just one level below" matching
    1. More complex syntax equals more complex matching,  `/foo/*/bar` will be more difficult


> Possible Solutions
    1. No wildcards...
    1. Do not allow wildcards and use extension mechansim to indicate wildcards as advanced feature.  Server implementors could then specify type of wildcards the server supports (simple wildcards,   wildcards based on WS-Topics spec, etc).  Possibly allow wildcards in future version of Bayeux spec.
    1. Implement wildcard system based on above goals and include in specfication.  Possibly allow more advanced wildcard systems in extension/advanced feature mechanism.
    1. Investigate and use more advanced wildcarding - JMS globbing (pick a vendor specific one), WS-topics, etc.  Or come up with own specification.




_Extension mechanism_ -
> There has been some recent discussion. Following was discussed:
    1. ext field allowed(not required) in every message.
    1. Feature discovery done by looking at handshake ext fields in client and server.
> Any concerns/questions?  Will need to add section to protocol doc for this.


_Security_
> Although there is references to security in original document, further investigation/protocol doc inclusion  is needed on following points:
    1. Specify allowance for any HTML auth scheme to be used without bayeux participation  (Basic/Digest)
    1. Challenge base auth, including looking at ongoing token exchange (no simple token sequence, need for allowing old tokens). [recent discussion](http://groups.google.com/group/cometd-dev/browse_thread/thread/b38637fb3900b677?hl=en)
    1. Provision for setup where html generates token and passes it to comet server.
    1. Pre-authenticated server blat events (anything need done for this??)


## Meeting Minutes ##

```
<xantus> yo
<markn> hey xantus,  everyone
<xantus> hi
<markn> anyone else with us yet?
<slightlyoff> howdy
<slightlyoff> where's the agenda?
<markn> I put an early agenda out here - http://code.google.com/p/cometd/wiki/WikiMinutes20070404
<xantus> I was just about to paste that
<slightlyoff> rad, thanks
<markn> do we happen to have gregw with us?
<gregw> markn: happenstance would have it
<gregw> didn't know there was a meeting
<markn> ah,  well you made it so good.   
<xantus> it was announced on the user list
<gregw> 800 emails behind at the moment
<xantus> :(
<markn> Was going to bring up protocol doc progress,  it seems you made some updates recently,  so that looks good
<markn> I'm sure you could use some more help udpating :)
<gregw> Yes definitely.  even if it is the intro blurb
<gregw> I also have been raising a few issues in red in the document
<gregw> which should be put on an agenda for a meeting soon(ish)
<slightlyoff> can we get it on for today's agenda?
<gregw> sure - let's put it on the end
<markn> I would think so,  I just threw some possible items for agenda for this one, we can pick and choose
<xantus> lets just have markn edit the wiki ATM
<xantus> I'm not sure google code handles conflicts
<xantus> Lets see, first up is globbing
<markn> tried to summarize in agenda what has been discussed,  not sure if we want go go any further at this point
<gregw> Can somebody reiterate the use-cases for globbing
<xantus> although we should come up with a solution
<gregw> is it just logging?
<slightlyoff> gregw: logging, sending messages to all subscribers "below" something (in-game events, etc.)
<gregw> But the second use case can be done with a broadcast channel
<slightlyoff> yes
<slightlyoff> and the first can be handled w/ a hacked server
<slightlyoff> both of which strike me as a missed opportunity
<xantus> I don't think we should support sending to a glob channel
<gregw> +1
<xantus> but yes to subscribing to a glob channel
<gregw> with subscriptions....
<gregw> should /foo/* subscription
<xantus> yes
<gregw> subscribe to channel /foo/bar 
<gregw> THAT IS
<slightlyoff> so the syntax we're using in the OpenAjax hub is:
<gregw> created after the subscription?
<slightlyoff>  /foo/* matches one level down
<slightlyoff>  /foo/** matches all the way down
<xantus> I like that
<markn> just at the end?
<xantus> yes
<xantus> -1 to /foo/*/bar
<slightlyoff> markn: I don't care, but the semantics for /foo/**/bar are weird not matter what
* Tendrid has joined #cometd
<slightlyoff> and more expensive
<markn> yep,   I am fine with just at the end
<gregw> Well - there appears to be general agreement that it is needed.   so let's do it
<gregw> who wants to add it to the spec?
<slightlyoff> I shouldn't volunteer...too slammed on Dojo stuff
<slightlyoff> markn? xantus?
<gregw> simple trailing matches of /* or /**
<markn> I'll give it a shot
<slightlyoff> I think the thought was also to support /foo/*/baz
<slightlyoff> but perhaps not /foo/**/baz
<slightlyoff> dunno
<slightlyoff> and frankly, I don't much care = )
<gregw> -1 on both 
<xantus> -1
<gregw> unless there really is a use-case
<slightlyoff> ok
<xantus> yeah, I don't see a use for it
<slightlyoff> great
<slightlyoff> so trailing * and ** then?
<xantus> yes
<markn> advanced feature/extension if needed by someone
<slightlyoff> both are essentially the same code
<xantus> gregw, markn?
<markn> * ** is ok with me
<gregw> +1
<xantus> cool
<xantus> extension mech
<xantus> I don't recall much of that
<markn> discussed briefly last meeting
<gregw> it says basically: "put what you like in an ext field"
<gregw> and don't pollute top level of messages
<xantus> ah
<xantus> is it in the spec yet
<gregw> No, but I'm happy to add
<xantus> ok cool
<gregw> if there are no objections, I'll add it shortly
<slightlyoff> awesome
<xantus> security
<slightlyoff> so bob was making the case that the current system can't be used for challenge-response auth
* Looking up Tendrid user info...
<xantus> because we thought token based was good enough
<xantus> the html that loads the js could have the auth done by then, and a token setup
<slightlyoff> well, we agreed (I think) that you might do auth out of band
<gregw> One train of thought is that auth needs to be done at a higher level than bayeux
<xantus> correct
<slightlyoff> so it sounds like we should, at a minimum, outline that scenario
<xantus> +1
<gregw> There is an empty section on security in the spec :-)
<gregw> looking for a happy typer :-)
<gregw> But if we want to add the ability for challenge response....
<gregw> it could be as simple as a successful=false in the handshake response
<gregw> with advice to retry
<xantus> true..
<gregw> no harm adding that... then using it for challenge response could be an extension for now
<xantus> which it should be since there are so many types
<gregw> OK - I will add the ability for a failed handshake - unless there is a -1 ????
<slightlyoff> works for me
<xantus> +1
<slightlyoff> is that a requirement for conformant clients/servers?
<slightlyoff> or optional?
<gregw> Make it an optional field - that if missing true is assumed
<xantus> a failed handshake with advice
<gregw> make successful and optional field that is
<xantus> go away, reconnect, reauth, etc
<gregw> and as much exchange as you like can take place in the ext field
<gregw> secret handshakes and all
<slightlyoff> yep
<xantus> I wanted to do a bind socket type demo
<gregw> actually - we already have this
<gregw> we have authSuccessful field
<gregw> so no need to change... just explain how the negotiation can be done with this mechanism
<gregw> and the ext field
<gregw> so authSuccessful=false advice=retry ext=nonce
<gregw> etc.
<slightlyoff> yep, that works
<gregw> So little issues I have highlighted in the spec so far are:
<gregw> version is defined as a float.
<gregw> but that gives us trouble with 1.9 to 1.10
<gregw> which hopefully will not be a problem...  but...
<gregw> I still think it would be best to have the version as a string
<gregw> of . separated elements
<xantus> maj.min
<xantus> yep
<gregw> well as many dots as you like
<slightlyoff> maj.min.patch
<slightlyoff> etc
<gregw> yep
<slightlyoff> maj.min.patch.whateverfirefoxdoes
<gregw> will just bring in line with other version definitions in RFC land
<gregw> I'll make a note that implementations SHOULD accept a float, but MUST generate a string version
<gregw> so we don't break anything
<mst> I'd have the canonical version being zero-padded
<mst> so 1.0009 then 1.0010 or whatever
<mst> it's how perl does it, and it's about the only way that plays nice with all distro packaging systems
<mst> and they're probably a saner selection of version calculation code than average
<xantus> thats a good point
<gregw> so . separated elements.   first is an int, second is 4 digit zero padded decimal and rest is free form?
<gregw> or maybe 2 digit zero padded will be enough?
<slightlyoff> 100 versions is a lot = )
<mst> I'd tend to 3 digits
<mst> it's nice sometimes to be able to do 1.001, 1.005, 1.010 etc.
<mst> Catalyst used 2 digits and is about to face the problem of running out of them.
<gregw> well if the spec just says . separated strings, we can decide on a release by release basis the strings to use
<gregw> with the first hard choice being 1.0 or 1.000
<mst> seems reasonable
<xantus> We can just use arbirary versions like the AOL MSN wars
<gregw> or 1.0GS  (gold standard)
<xantus> :P
<gregw> and then 1.5 can become enterprise5
<mst> and then we rename 2 to 7
<mst> for the software but not the protocol
<gregw> I think starting at 7 is the only intelligent thing to do
<gregw> so . separated string in spec
* slightlyoff thinks we should ask Sun marketing
<slightlyoff> ;-)
<gregw> the other thing I noticed was that in a normal event send
<gregw> we have both clientID
<gregw> and connectionID
<slightlyoff> yes
<slightlyoff> that's intentional
<gregw> which are really the same thing?  or no?
<xantus> why
<slightlyoff> a client session can last over many logical "connections" (client retries, etc.)
<slightlyoff> I'm ammenable to the idea that we don't need it
<gregw> Oh - I thought the connection was always /meta/connections/clientId
<slightlyoff> but it would make life a bit harder on server authors
<xantus> hm
<gregw> If connections are different to clients.... are subscriptions per client or per connection?
<xantus> should we rename it
<slightlyoff> gregw: I think I was making them per-client
<xantus> sessionId
<gregw> I don't see the need for a different ID.    clientID really is a session ID
<gregw> and the connectionID can be derived from clientID
<gregw> unless there is a use-case for multiple connections?
<gregw> in which case subscriptions should be connection related and not client related
<slightlyoff> no, probably not...I just wanted a simple way to handle the problem of needing to differentiate the logical subscription set from the physical connections to the daemon
<xantus> subscriptions should then be by logical connection
<gregw> can you explain that some more?
<slightlyoff> gregw: so assume the connection drops, right?
<gregw> yes
<slightlyoff> you don't want to cause the client to need to re-subscribe to all of it's topics
<slightlyoff> so in the server, you need to somehow disambiguate the two
<gregw> so just reconnect
<gregw> and no need for a new connectionID
<slightlyoff> you can use some session concept if you already have one
<gregw> disambiguate the two what?
<slightlyoff> gregw: the logical group of subscriptions for an agent from the handler for the physical connection
<gregw> so are you saying that connectionId is for a physical connection?
<slightlyoff> well, that's how I was using it = )
<xantus> hm
<gregw> I actually think we could drop the concept of connectionID
<gregw> and just have clientId
<xantus> as do i
<gregw> and then we just have connected and unconnected clients
<slightlyoff> ok = )
<xantus> subscriptions are per client id then
<gregw> Great!  I can add a legacy section to the spec then :-)
<xantus> ok, so the use case of multiple tabs
<xantus> anyone remember the demo I had?
<xantus> inter-window communication
<gregw> xantus - is this the same topic or a new topic?
<xantus> same
<gregw> ok go on....
<xantus> So I open multiple windows to a cometd app
<xantus> it could then detect previous winodws and talk to the window thats the controlling window for topic data
<xantus> how do you tell which window gets the data?
<xantus> .... with a connectionId
<gregw> is it possible to do semephores in client side cookies?
<gregw> without busy loops?
<gregw> semaphores 
<xantus> hm
<gregw> We have two choices with this....
<xantus> I didn't use cookies for the inter-window thing
<gregw> the different windows/tabs chat to each other first
<gregw> and agree to share a client ID
<gregw> or they have separate client IDs
<gregw> and agree to share a physical connection
<slightlyoff> yeah, xantus has a very elegant soltuion
<gregw> do tell...
<slightlyoff> gregw: I really do think Xantus' solution makes a lot of the OAA Communcations stuff obsolete
<gregw> excellent!
<xantus> the persistent storage comms?
<slightlyoff> xantus: yeah
<xantus> I wish IE's storage was larger, but that can be worked around
<xantus> just marshal data in blocks through it
<markn> this sounds quite nice
<xantus> so I think we can do away with connectionId and any sort of wacky things like this can be done OOB
<gregw> So the is something in shared storage per host... that has message queues in it?
<xantus> in data: with a session id or something
<gregw> and the client ID would be shared that way as well
<xantus> yes
<gregw> it sounds good to me.   but I don't think we should mandate it in the spec.
<gregw> but perhaps describe how multi windoes could be supported
<gregw> in fact I think we should have 4 levels of multi window/tab support:
<gregw> 0) No support - block at your own peril
<gregw> 1) server side detection with advice used to fall back to traditional polling
<gregw> 2) client side detection with users choice what to do
<xantus> http://javascript.groups.vox.com/library/post/6a00b8ea0728391bc000d10a7907638bfa.html
<gregw> 3) client side coordination like what xantus is talking about
<xantus> http://voxosphere.com/iwc/
<slightlyoff> gregw: well, the client-side coordination lets us ignore the problem (more-or-less) in the spec and at OAA
<gregw> exactly - but it may be a while before all client impls support it
<xantus> I sorta emulate WhatWG storage in IE
<slightlyoff> gregw: right, but that's a competitive differentiator = )
<slightlyoff> yeah, IE has timers+UserData
<slightlyoff> we have solutions 95+%
<slightlyoff> and we can just yell at them in #webkit to fix Safari ;-)
<xantus> yeah, I throw the event with a timer
<gregw> xantus your demo is not working for me :-(
<xantus> in what?.
<gregw> security error
<gregw> firefox
<slightlyoff> heh
<xantus> odd, works for me
<gregw> 2.0.0.3
<gregw> Security error" code: "1000
<xantus> same here
<gregw> [Break on this error] this.storage = globalStorage[ domain ];
<markn> seems ok for me
<gregw> on linux (ubuntu)
<markn> windows, same version
<xantus> probably some wierd difference between linux/windows builds
<slightlyoff> gotta go, Dojo meeting starting
<markn> see you later
<gregw> OK - bye.  Good meeting today!
<slightlyoff> sounds like we got a lot decided this week
<gregw> lot's decided
<slightlyoff> yeah
<slightlyoff> good meeting = )
<slightlyoff> later all!
<xantus> cya!
<gregw> I will check in some updates in an hour or two
<markn> I will update meeting notes/outcomes/action items.   And start the meeting page for next week.   Anyone feel free to edit wiki and add agenda items
<xantus> awesome
<markn> probably should be meeting next week?
<xantus> yep!
```



















































































































































