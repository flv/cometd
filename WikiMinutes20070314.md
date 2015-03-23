# 2007-03-14 Meeting Minutes #

```
<slightlyoff> hey yo
<davidascher> hey yo
<markn> hey
<slightlyoff> howdy markn
<CB1> hey guys
<slightlyoff> I know gregw's around 'cause was just on a conference call w/ him = )
<slightlyoff> do we have a xantus?
<davidascher> guess not =(
<davidascher> this irc meeting thing isn't working out so well =)
<slightlyoff> oh effing hell 
slightlyoff calls xantus
<slightlyoff> he's not answering his phone
<davidascher> let's go on w/o him and see if we can agree on anything.
<slightlyoff> gregw: are you at least here?
<davidascher> agenda items from the past: RPC style interactions, advice, wildcards.
<davidascher> on all of these it feels as though i don't have some of the background.
<slightlyoff> I can add some context to them
<slightlyoff> so one of the things we've found with our brief use of Bayeux is that you very often need to tie a request/response service to a Bayeux bus for reasons of backward compatibility and simplicity
<slightlyoff> essentially request/response, only w/ a Bayeux topic name as the signifier for where to go and a publish message noting the "what to get" bits
<slightlyoff> I think we've agreed that the protocol needs this and that the clients have been mostly (all?) updated to support it
<davidascher> "what to get" being a reference to the response itself?
<davidascher> they have? how do you send a response?
<slightlyoff> davidascher: no, a set of variables to send along
<slightlyoff> davidascher: that's the bit that we didn't have implemented everywhere
<slightlyoff> davidascher: we now require that clients handle messages on any server response
<slightlyoff> davidascher: so the server can hold a request/response connection until the response is available and deliver just that response in the return packet
<gregw> hi all
<slightlyoff> hey gregw
<slightlyoff> gregw: we were just going over the details of the RPC style stuff that we needed to discuss
<slightlyoff> gregw: particularly, what needs to be discussed now that the RPC-style response patch has been applied to Dojo's client?
<slightlyoff> davidascher: I think we should add authentication to today's agenda, Bob Ippolito had some questions for me regarding that and I don't think the current protocol lays out the options clearly enough
<gregw> slightlyoff: do you mind if I restate the RPC description a little differently
<slightlyoff> gregw: please!
<gregw> The key protocol change to support RCP was one of efficiency
<gregw> previously we would have had a forever response or a long poll waiting
<gregw> a message to a channel would be posted on a new request
<gregw> if the semantics of that message was to produce a reply
<gregw> it was not able to be included in the response to the message
<gregw> as it could only say that the message was delivered
<gregw> and the long poll or forever response had to be woken up to deliver any response messages that resulted from the orginal message
<gregw> now with this change
<gregw> the response that acks the sent message can contain messages
<gregw> so it can contain a response to the message... multiple responses to the message or even unrelated messages
<gregw> but the key thing is that if you map RPC over channels
<gregw> you get a single HTTP request and HTTP response used
<gregw> instead of 2
<gregw> how we map RPC to channels is still not defined
<gregw> but I don't think we really need to do that
<gregw> at least not yet
<slightlyoff> yeah, at some point I think we'll have a Great Config File Reckoning
<slightlyoff> but it's a bit premature for that
<slightlyoff> gregw: ok, so what work do we need to do around it?
<slightlyoff> gregw: (lots of other stuff to talk about today  )
<davidascher> so I may be missing a point, but a single request to the bus can end up going to N subscribers.
<davidascher> each one can send different responses, no?
<gregw> davidascher. sure
<gregw> and if those responses
<gregw> are available in time, then they can be in the HTTP response
<gregw> to the original message
<gregw> only likely for server side clients
<gregw> so it does not change the semantics of the channels
<gregw> only efficiency of transport.
<gregw> to answer Alex
<gregw> now we have the patch
<gregw> I don't think we need more
<gregw> now
<gregw> we can do efficient RPC
<gregw> with various mappings to channels
<gregw> (normally with client ID in channel ID)
<gregw> and from my point of view... I don't think we need to discuss RPC more here....
<gregw> unless others have issues
<davidascher> fine w/ me, thanks for the bg.
<slightlyoff> ok, globbing?
<markn> yep, thanks,
<slightlyoff> it's the topic that won't die
<slightlyoff> and I was totally OK w/ letting it go, but TIBCO raised it with me
<davidascher> why not do globbing?
<markn> I missed some of the previous discussions, any background on why it was taken out?
<slightlyoff> markn: there were lots of competing ideas about if/how to make it work and the semantics for specifiying it
<slightlyoff> markn: it's like any other regexp-like discussion
<slightlyoff> markn: despite folks agreeing on 99% of stuff, you still get awk regexp vs. gnu regexp vs. pcre 
<gregw> and I whinged about it being hard to implement efficiently in the java server (but I think that actually is an arguement of why we should do it rather than leave it to users)
<slightlyoff> gregw: so I was willing to let it die because I didn't have enough real-world experience to suggest that it was a "can't live without" feature
<slightlyoff> gregw: but the TIBCO guys do 
<gregw> can you describe TIBCOs user case?
<slightlyoff> gregw: they want to be able to do a * subscription to everything "under" a particular topic for administrative and logging purposes
<davidascher> right, so the use case can be summarized as "*" being the only needed syntax =)
<slightlyoff> yep
<slightlyoff> and that's the only syntax the Python server supported when it did do globbing
<gregw> does /foo/* match /foo/bha/bah
<slightlyoff> gregw: yes
<davidascher> which of course is neither awk vs. gnu vs. pcre forwards compatible =)
<davidascher> sounds like a winner to me =)
<slightlyoff> gregw: in the Python server we made topics hierarchial
<slightlyoff> gregw: w/ "/" as the hierarchy delimiter
<gregw> perhaps we should just use the JMS topic globbing spec... whatever that is
<markn> it was mentioned to me by some coworkers that some work was done in WS-topics to deal with things like this
<davidascher> (so we move to battling RFCs instead of battling re libraries. typical 
<slightlyoff> davidascher: heh
<markn> only difference seems to be using // to match further down the tree instead of just /, so /foo//* would match /foo/bha/bah
<davidascher> am i not right that w/o the * style globbing, it's basically impossible for users to do that efficiently?
<slightlyoff> well, I actually don't care what the syntax is, so long as we can't match partial topic names
<slightlyoff> davidascher: I think we can do it quite efficiently
<davidascher> because they'd need to subscribe to every subtopic that is?
<gregw> so /foo* does not match /foobar
<slightlyoff> davidascher: see the unused code at the bottom of cometd.py
<davidascher> slightlyoff: (i mean if the server doesn't do the globbing)
<slightlyoff> gregw: correct
<slightlyoff> gregw: in fact, foo* is just another token
<slightlyoff> gregw: the only special meaning would be for full "this or below" globbing
<gregw> so /foo/* matches /foo????
<gregw> so /foo/* matches /foo ????
<slightlyoff> gregw: no
<gregw> good
<slightlyoff> gregw: but it does match /foo/thinger and /foo/thinger/blah
<gregw> gotcha
<gregw> who is going to write it up for the spec 
<slightlyoff> gregw: it was in the spec before, but I can do it
<slightlyoff> gregw: but if there's an alternate (aka: better) syntax for it, I'm totally down w/ that too
<gregw> Depends... do you intend to support /foo/*/bah ?
<gregw> that will need a more rigourous spec
<gregw> but to support just trailing * can be done as a special topic name rather than as a pattern match
<slightlyoff> gregw: yep, and I want it to be implemented as a special topic name
<slightlyoff> gregw: or at least a subscribe-time only operation
<slightlyoff> gregw: whatever we do shouldn't adversely impact dispatch
<gregw> So it only subscribes to channels that exist at the time of the * subscription?
<slightlyoff> gregw: no, but dispatch only needs to send down to the special "*" channels at each level
<slightlyoff> gregw: but like I said, I don't know if this is a great syntax for it
<slightlyoff> markn's xpath-like thing seems OK
<davidascher> we could just try it and see. it feels like something that 'practice will tell'.
<davidascher> call it an experimental feature.
<gregw> the problem with more complex syntax's is they encourage more complex matchings
<davidascher> (speaking of which, do the servers currently advertise what features they implement?)
<slightlyoff> davidascher: nope, but I think we need to do that or provide an "advanced" or "extensions" namespace on every protocol message
<slightlyoff> davidascher: I'd like for folks like KnowNow and Lightstreamer to be able to implement exotic features on top of the protocol w/o breaking protocol compatibility
<gregw> or just in the handshake and allow arbitrary extra fields in all messages
<slightlyoff> gregw: that seems slightly dirty, but I guess it's impossible that you'll really get conflicts since you'll only connected to one server at a time w/ a given client
<gregw> we could have naming conventions for extra fields?
<davidascher> yagni for now i'd say
<gregw> or insist they are in sub objects
<slightlyoff> gregw: my thinking is that we allow an "ext" field on every message and let vendors duke it out in there
<slightlyoff> gregw: it's JSON...they can pack it w/ whatever they want
<gregw> cool
<slightlyoff> davidascher: does that make your YAGNI instincts cringe?
<davidascher> nah, ext is fine.
<davidascher> it localizes the trouble spot and makes vendors feel like we care about them =)
<davidascher> this part is all about convention.
<slightlyoff> cool
<davidascher> and the "feature discovery" can be done by looking at the ext field in the handshake interaction?
<davidascher> (both client and server, i guess)
<slightlyoff> davidascher: yep
<davidascher> is the ext field required?
<slightlyoff> davidascher: I don't think so
<davidascher> ok/
<slightlyoff> davidascher: clietns and servers should both be able to ignore it w/o error
<davidascher> speaking of which,someone was going to fix up the spec?
<gregw> I'll just code that ignore now..... finished!!!
<gregw> I have been working on making it look like and RFC
<gregw> skeleton is in SVN
<gregw> updated a few days ago
<davidascher> I'm happily using comet and bayeux w/ precious little understanding of the loow level protocol, because the current spec (and python server code) is too cryptic =)
<gregw> but a long way to go
<slightlyoff> gregw: saw that, it's looking a lot better than my hackish attempts
<davidascher> oh, cool, will look at it
<gregw> offers of help finishing are welcome!
<davidascher> (man xantus writes a lot of code)
<gregw> may convert it to HTML or Docbook so we can merge multiple changes
<slightlyoff> gregw: as an aside, you caused me to get Open Office... /me shudders
<slightlyoff> gregw: would be happy w/ HTML, DocBook, or ReST

gregw hates open office only a little less than word
<slightlyoff> gregw: heh
<davidascher> (is there a mailing lsit for the svn checkins?)
<slightlyoff> not that I know of...I'd ask xantus...but
<slightlyoff> 
<davidascher> yup
<gregw> I'll go for HTML I think.... lingua franca
<markn> was planning on mostly helping review, but if help is needed I might be able to fill in a few sections of the doc
<slightlyoff> "you must know this much HTML to work on a Comet protoocl"
<slightlyoff> 
<gregw> I'll convert to HTML today... so merging works
<gregw> and then we can coordinate on this IRC
<gregw> to make sure we don't bumb into each other
<gregw> lots of empty sections to fill
<davidascher> am i right we have 14 minutes to cover auth? 
<gregw> lets move on then
<davidascher> gregw: spec looks like it's going in the right direction!
<slightlyoff> gregw: great, thank you!
<gregw> so about this auth stuff then????
<slightlyoff> gregw: so Bob Ippolito was asking about auth as he was implementing a Bayeux server
<slightlyoff> (in erlang, no less!)
<slightlyoff> gregw: and I know that we've thought about it, but I've slept since then and I don't think we've got a coherent auth scenario laid out in the protocol spec
<slightlyoff> his primary concern, IIRC, was that there's no chance for the server to present a challenge
<slightlyoff> I'm pretty sure that's not exactly true, but it would seem prudent to lay out the most important auth scenarios in the protocol doc
<slightlyoff> namely:
<slightlyoff> 1.) pre-authenticated "server blat" events
<slightlyoff> 2.) challenge-based auth
<slightlyoff> 3.) cometd w/ SSL
<xantus> shit, i'm late
<slightlyoff> haha
<slightlyoff> gregw: what am I missing there?
<gregw> I think we should start off with a blanket allowance for any HTML auth scheme to be used without direct bayeux participation
<gregw> so 403 responses can be allowed
<gregw> for example for BASIC or DIGEST auth
<gregw> I also think we need to look at little more at ongoing token exchange
<slightlyoff> yep
<gregw> if we want changing tokens.... then we have to be aware of multiple connections and messages that pass in the night
<gregw> so we can't have a simple token sequence
<gregw> and have to allow slightly old tokens to be accepted
<gregw> at least once
<gregw> but maybe more as the client may pipeline multiple messages before a response is received with a new token
<xantus> i'm using hash based tokens
<gregw> or we could just say - use SSL if you are worried.... and then not have any per message tokens or auth 
<slightlyoff> gregw: we should probably talk that over on-list then...I don't have a strong feeling as to whether or not it's palletable to punt it entirely
<slightlyoff> gregw: SSL has other problems
<gregw> such as?
<slightlyoff> gregw: it forces you to put your bayeux server back in front of your HTTP infrastructure
<slightlyoff> no off-site or x-domain usage w/o nasty security warnings in IE
<gregw> or server proxy
<slightlyoff> yep
<CB1> maybe x-domain hosters should also provide ssl?
<slightlyoff> ok, gotta jump over to the dojo meeting
<slightlyoff> 'till next week = )
<gregw> I gotta run to... bye all.... I'll post when the spec doco is more accessible
<xantus> I think tokens are fine
<xantus> the cometd server shouldn't generate them though
<xantus> the server serving the html generates a token based on the id and secret key
<xantus> passes it to cometd server that knows the saem key
<xantus> hash based..
<davidascher> xantus: is there a mailing list for svn changelogs?
```