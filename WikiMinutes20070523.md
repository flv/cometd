# Introduction #

2007-05-23 Meeting.  No set agenda.


# Details #
```
<markn> hey slightlyoff
<markn> anyone else with us today?
<markn> gregw?
<slightlyoff> heya markn!
<markn> I think xantus mentined previously he was busy
<markn> through this week
<slightlyoff> markn: I got some interesting feedback yesterday from a potential Bayeux user
<markn> good I hope :)
<slightlyoff> markn: they were worried about the kinds of administrative commands which we'd tentatively spec'd out at one point then dropped
<slightlyoff> markn: yeah, they were hoping we'd be able to perhaps flesh them out if they were able to send us use-csaes
<gregw> hey all
<slightlyoff> hey gregw 
<slightlyoff> gregw: this can't be a sane time for you
<gregw> 7am.  but I have been up since 5.  nasty bug I am chasing
<slightlyoff> gregw: ouch, that's brutal
<slightlyoff> (both)
<gregw> anyway.... I can only visit briefly..
<gregw> markn was saying....
<gregw> or was it Alex interupted?
<markn> gregw: alex
<markn> gregw,  waiting for alex,  you mentioned in google group about not seeing the need for status/ping in the protocol doc
<markn> while we are wating for slightlyoff I should say
<markn> I made an attempt at fillign in status,  but left ping out
<markn> any more thoughts?
<slightlyoff> sorry, back
<gregw> I can see a need for status kind of... to get advice and subscriptions
<gregw> but reconnect is already a kind of ping
<gregw> we are pinging all the time
<slightlyoff> markn: so they were wondering about queries for number of client subscribed to topics and some stuff we'd explicitly punted (like access control)
* CB1 has joined #cometd
<slightlyoff> and clear advice about extensions for things like auth
<gregw> slightlyoff: yes I think we should either define auth or punt it to ext
<gregw> half defined just raises questions.
<gregw> same for status
<gregw> either it is defined
<slightlyoff> gregw: I'm starting to be in the "punt" camp for auth
<gregw> or we can do it in ext
<slightlyoff> gregw: status queries seem more like something we should handle
<slightlyoff> but I don't have a basis for that assertion = )
<slightlyoff> other than it's data we already have
<gregw> well auth will need a lot of time and it is something that a) we need to get correct and b) webapps already do
<gregw> So long as we do not break existing auth and we allow ext
<gregw> I think we are OK
<slightlyoff> gregw: I tend to agree
<gregw> our use of a client ID still protects against hijack
<slightlyoff> oh, hey janb 
<slightlyoff> gregw: yeah, that's handy
<slightlyoff> gregw: might not fix replay, but that's just an SSL issue
<janb> slightlyoff: hi there
<gregw> So unless others disagree.... shall we remove auth field and expand the security 
section to discuss how ext can be used or existing auth used 
<slightlyoff> gregw: I think that's best
<gregw> I will do (once my nasty bug is fixed)
<gregw> So then status?
<gregw> If somebody wants to define what it contains and a format....
<gregw> then we can have it
<slightlyoff> gregw: so I'll make that case to the folks I was talking to yesterday
<slightlyoff> gregw: hopefully we can get a use case out of them to work from
<gregw> ok
<markn> The update I put in for status says to use the ext field to specify type of status info to request, and ext feild for response
<markn> wasn't sure how well that would work out
<gregw> markn: that will work out fine for container specific status.  but if there is a usecase for standard status
<slightlyoff> markn: in lieu of a real use case, that seems right
<gregw> ie if somebody can make a case for why they have forgotten their own subscriptions....
<gregw> then we can have a standard status
<slightlyoff> gregw: heh
<gregw> yes?
<gregw> so you will ask the users for there use case... and if it is good, then we can do standard status
<gregw> if not, I think markn ext stuff is all that is needed
<slightlyoff> gregw: fair enough
<slightlyoff> gregw: so we had talked a bit in person about handling cases where we have one connection servicing two clients (peering windows, etc.)
<gregw> tabs etc.  yes.
<slightlyoff> gregw: has that had any time to roll around in your cranium?
<gregw> I still don't think I understand the solution you are advocating
<gregw> do you understand mine:
<gregw> ie that the advice to a second tab/window is to wait for a cookie to be set before 
econnecting
<gregw> then the other window/tab can be asked to set that cookie to notify other windows that there are messages to be got
<gregw> slightlyoff:^
<slightlyoff> gregw: what do those other windows do in response to getting the message?
<gregw> All windows work as they do now... getting their own messages via a reconnect.  the only difference is
<gregw> that when we have 2 or more reconnects at once
<gregw> the extra windows are told to wait before doing another reconnect
<gregw> and they just wait until they see and event "you have data to get"
<gregw> which is passed via a cookie
<slightlyoff> so they essentially wind up being request/response, but w/ notification?
<gregw> and the reconnect and get their messages
<slightlyoff> gregw: is the thought that this prevents the first window from handling the data itself?
<gregw> yes- they fall back to smart polling
<gregw> yes the first window just passes events
<gregw> also if the first window closes
<gregw> the other windows will still be polling after a timeout
<gregw> and will thus recover and get new advice
<slightlyoff> so what's the threat model here? XSS in the same domain?
<gregw> no voting for who is boss
<gregw> Limited bandwidth of cookies
<gregw> currently we can send a 16k message if we want
* slightlyoff likes avoiding voting
<gregw> but we could not put that through a cookie
<slightlyoff> no, but we can batch
<slightlyoff> that's actually really quite easy
<gregw> but then we are doing TCP/IP over cookies
<slightlyoff> you can get great bandwidth over cookies = )
slightlyoff> gregw: yes
<slightlyoff> gregw: and I agree, that's ugly and brittle
<slightlyoff> gregw: ok, so I kinda like your solution
<gregw> I think mine works even if the cookies don't
<gregw> it is just traditional polling
<gregw> with the cookies being extra value in that they allow the polling to be less frequent
<gregw> and to be short-cutted
<gregw> this is really Ted's idea that he was advocating in OAA
<gregw> Once my nasty bug is fixed.... I will write this up as a proposal for the spec.
<gregw> then we can all play devils advocate
<slightlyoff> heh
<markn> this sounds quite nice
<slightlyoff> well, I will note that it's going to perform like ass, but we all know that
<gregw> throughput will be OK, but latency will be double
<gregw> two round trips
<slightlyoff> but we can hash that out later
<gregw> but many apps can handle 400ms of latency, just not 4s
<gregw> yes.
<gregw> I mean if we do get data over cookies
<gregw> there is nothing stopping the data being sent with the notification
<slightlyoff> gregw: true
<slightlyoff> we might be able to also augment it w/ some sort of policy to prevent floods
<gregw> true
<slightlyoff> gregw: so don't request data from notification more than once a a second or something like that
<slightlyoff> we'll still be hogging both channels should the volume be high
<slightlyoff> but at least we let people interleave
<gregw> yep.
<gregw> my fear is that if the "open" channel is busy
<gregw> then stupid browsers
gregw> will queue other requests behind the one long poll
<gregw> but then, they should not be doing anything but bayeux anyway :-)
<slightlyoff> gregw: you're a dreamer
<slightlyoff> gregw: I think we'll wind up w/ something exotic SRTL
<gregw> not sure how we get the browser to fetch images over bayeux
<slightlyoff> gregw: even back-filling this scheme for corner cases only really drops leader election
<gregw> yes
<gregw> anyway.... got to run
<gregw> anything else quick before I go
<gregw> ?
<markn> quick qustion about protcol doc
<markn> iframe envelope description
<gregw> "blah blah blah"
<markn> original doc describes in detail the envelope info
<markn> we want to include that in new protocol doc?
<gregw> I kind of thing that is implementation specific
<gregw> which is the nature of iframe
<markn> maybe we can provide a document outside of the protocol doc to help implementers
<markn> or reference
<gregw> but then I guess for interop we need to
<slightlyoff> markn: I think we're going to need that anyway
<slightlyoff> markn: I'm getting lots of architecture questions, esp from J2EE folks
<slightlyoff> markn: a lot of them are on Tomcat which they have to proxy w/ Apache 'cause it's so goddamned slow
<markn> yeah,   same issues
<markn> for us
<markn> lot of doubters
<slightlyoff> so they can't serve static resources from it and their apache falls over when they put cometd insisde their servlet container
* stefan has joined #cometd
* stefan is now known as st3fan
<slightlyoff> markn: so yeah, we probably need to address that head-on w/ some architectural "best practices" crap
<gregw> haproxy + jetty is the solution
<slightlyoff> gregw: yep, and we should outline that in a whiltepaper = )
<slightlyoff> gregw: or x-domain cometd, or, or, or.
<gregw> actually we should reach out to the apache people
<gregw> I think they are working on comet support as well
<slightlyoff> gregw: yeah, we need to find out about apache 2.2 and event_mpm for this proxy workload
<gregw> and we should make sure that proxying comet requests is a high priority for them
<markn> wonder fi they have ssl working over event mpm yet
<gregw> 2.2 and event_mpm is still not good enough
<slightlyoff> gregw: howso?
<gregw> it still allocates threads per requests
<gregw> so it handles lots of idle connections
<gregw> but not lots of outstanding requests
<st3fan> lighttpd 1.5 with cometd support will probably be more interesting than apache ;)
<gregw> my testing of mod_proxy in 2.2 with mpm had it chocking at 250 requests
<slightlyoff> st3fan: yeah, pretty much anything that can proxy zillions of zombies will have an opportunity to "play" here
<gregw> st3fan: yes - but there are organizational issues that mean it would be nice to have an apache proxy solution
* slightlyoff makes a glance at perlbal
<gregw> so a whitepaper of solutions would be good
<gregw> with an incentive for other proxies to get themselves listed
<gregw> as supporting comet
<gregw> we can have the good comet list and the bad comet list
<slightlyoff> yep, it seems like one of the best things we can do from a marketing/bloodpressure-lowering perspective
<st3fan> we deliberately chose for doing it on a much lower level with a foundry switch instead .. i'm really afraid of putting software solutions iin front of something like cometd to deal with 10 of 1000s of connections
<st3fan> s/level/layer/
* Looking up st3fan user info...
<slightlyoff> st3fan: yep, that seems also very sane
<slightlyoff> st3fan: if you can afford 2 for HA, it's a good answer
<st3fan> slightlyoff: ebay ;)
<slightlyoff> st3fan: we should probably document that too = )
<gregw> so do we have a volunteer to start this whitepaper?
<slightlyoff> st3fan? ;-)
<gregw> "comet loadbalancers and proxies"
<st3fan> in a few weeks when we go live i'll have real data for a foundry/jetty setup
<slightlyoff> st3fan: you seem versed in the alternatives
<st3fan> slightlyoff: i mean, just buy them 2nd hand from ebay, we use foundries from 2002, which easily deal with 250K connections
<st3fan> $1500 USD
<gregw> volunteer issue aside.... is there an issue tracker we can use?
<gregw> we should capture tasks discussed here
<markn> code.google.com/p/cometd
<markn> has an issue traxcker
<markn> a few defects there now
<markn> I'll make sure I update the meeting info on the wiki with this new information also
<gregw> OK - I will look to put "whitepaper" as a task there.
<gregw> I really have to run guys
<markn> see you gregw
<gregw> later....
<st3fan> bye bye
<slightlyoff> later gregw 
<st3fan> is there a tool to generate cometd load on a server? i find it hard to push a server to the limits for testing
<markn> someone posted a grinder script to the cometd google group
<st3fan> yeah but can grinder deal with say 25K concurrent connections?
<slightlyoff> st3fan: we need to write one in erlang or something
<markn> I do not know
<st3fan> tricky stuff :)
<markn> I know our performance team will have to deal with this issue at some point..... but I do not think their tools are up for the job
<slightlyoff> st3fan: I think that also comes to the overall point of conformance testing
<slightlyoff> st3fan: I know both my client and server fail miserably right now
<markn> slightlyoff:  I think that was discussed in a previous meeting also.  I think catalyst was mentioend as a possibility
<markn> or jmeter
<markn> slightlyoff: one mroe quick question,  in the protocol doc mime-message-block is mentioned as a transport type.  I think I read a blog post of yours describing the difficulties.  Should that transport be removed?
<slightlyoff> markn: I'm dropping it from the 0.9 Dojo client
<slightlyoff> markn: I think it's a dead line of questioning for now = \
<st3fan> slightlyoff: unit testing our cometd stuff is something we're working on now actually
<markn> ok,  
```