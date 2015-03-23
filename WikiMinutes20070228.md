# 2007-02-28 minutes #

```
Topic is 'http://cometd.com/ | The Scalable Comet Framework | http://cometd.vox.com/ | Try out the cometd cluster at http://www.webtide.com:9000/' 
Set by xantus!~xantus@c-67-161-8-139.hsd1.ca.comcast.net on Wed Jan 24 16:25:32 
GumbyNET sets mode: +o markn 
slightlyoff has joined #cometd
<CB1> hello everyone!
<slightlyoff> howdy
<markn> hello all 
cbreathe has joined #cometd 
bradoaks has joined #cometd
<gregw> hi
<gregw> have we started?
<CB1> greetings
<markn> not yet
<CB1> nope
<bradoaks> i've just arrived, but am merely lurking.
<cbreathe> Likewise.
<slightlyoff> do we have an agenda?
<gregw> there was one on the wiki....
<slightlyoff> and are we logging this?
<mst> I log everything.
<slightlyoff> mst: rad
<CB1> there are some topics on the blog
<mst> you can have the appropriate snippet on request
<gregw> http://cometd.vox.com/library/post/developer-meetings.html
<slightlyoff> ok, shall we get started?
<CB1> yup
<davidascher> hi folks
<slightlyoff> hey davidascher 
<davidascher> xantus not here?
<slightlyoff> ugg
<slightlyoff> xantus: you here? 
slightlyoff phones xantus 
cbreathe is now known as zanes
<slightlyoff> wow, he's REALLY not sounding good
<slightlyoff> out of the office sick
<CB1> yikes
<slightlyoff> yeah
<davidascher> i had the flu a few weeks ago. no fun.
<slightlyoff> reschedule?
<slightlyoff> or should we plod ahead?
<davidascher> are there agenda items we can handle w/o him?
<CB1> it's up to you guys, i'm down for whatever
<slightlyoff> I think we've got everyone but xantus
<slightlyoff> davidascher: yeah, I think so
<slightlyoff> we can discuss a test suite
<davidascher> let's do what we can I think. the organization must move even if individuals are taken out. lesson 4 from the borg.
<slightlyoff> rpc style for bayex
<slightlyoff> and I'd like to add hierarchial things again
<slightlyoff> (after a conversation w/ TIBCO)
<gregw> firstly - what is the target for a 1.0 ?
<davidascher> i'd like to add the python client to the agenda – see if anyone else cares.
<slightlyoff> davidascher: great
<CB1> 1.0 of the protocol, right?
<gregw> yep
<markn> I was of understandign it was around April 15th, but heard that a bit ago
<CB1> what is the protocol missing?
<slightlyoff> gregw: I think we need to to be something that's clear enough for clients to easily implement and servers to support for what we view as the common-case uses
<gregw> A make-over of the documentation mostly (I think)
<slightlyoff> gregw: it would also be good if we had at least 2 conformant servers and clients
<gregw> I might volunteer to rewrite the document more like and RFC and less like a by-example document
<gregw> then we need a test suite to measure conformant
<slightlyoff> gregw: agreed
<CB1> yeah, there's still a few fix me's in there
<gregw> OK - so I have volunteered then
<slightlyoff> gregw: who is going to assist?
<gregw> I will get a draft done by tuesday next week and then circulate for comment
<slightlyoff> gregw: I mean, I'm happy to help, but my time is severely constrained right now
<slightlyoff> gregw: ok, that sounds good
<gregw> yeah - so is mine... but.... 
garbagecat has joined #cometd
<slightlyoff> ok, test suite
<slightlyoff> we should probably define what we want to check for conformance
<slightlyoff> clients? servers? both?
<davidascher> slightlyoff: i get the impression that xantus has more "infrastructure" already built than the other implementations.
<slightlyoff> we've also, at various times, said that we need performance and scalability test sets
<gregw> both - otherwise pointless
<gregw> davidascher: so are you volunteering xantus to write the test suites?
<davidascher> someone's been pushing me to learn Apache::Test
<davidascher> gregw: not really – i was wondering if that topic should wait for his input
<davidascher> like i know he's got some sort of a benchmark running w/ apachebench
<CB1> at the comet developer day, foobarfighter (bob) mentioned he might be able to assist with the test suite
<CB1> i guess the test suite was going to be coded in perl
<slightlyoff> it would seem we should pick the language for that which is going to have the most folks available to hack on it
<CB1> i'm not a perl guy 
<markn> mainly java here, won't be able to help much with perl
<gregw> I'm not a perl guy either.... but it is probably more universal than any othe choice
<slightlyoff> well, other than Java = )
<slightlyoff> we've got a lot of folks here who know Java, right?
<markn> I figured 
<davidascher> not a perl guy or a java guy =)
<CB1> there has to be some software already out there
<slightlyoff> CB1: and we're all using that software in the guts of our various servers = )
<gregw> A good scriptable HTTP tester is what we need to test servers
<gregw> but the "good" bit is hard to find
<mst> WWW::Mechanize is usually pretty good
<slightlyoff> mst: do you have any time to help w/ this?
<slightlyoff> perl or java both seem good choices
<mst> right now, probably not
<mst> I'm currently working on a v. simple long-poll server for $app to make a start towards cometed
<mst> the idea being we get used to the standalone poll server first, then move it to being cometd over time
<CB1> a friend recommended jMeter... http://jakarta.apache.org/jmeter/
<mst> java might be better
<slightlyoff> the other option, as I see it, is to try to get the Python client in shape and use it as a test harness for servers
<mst> simply because you already need it for dojo
<slightlyoff> or we write anothe client
<mst> whereas you don't need perl for dojo
<davidascher> slightlyoff: do you know what the testing story is in twisted land?
<slightlyoff> davidascher: I don't, but let me ask in #twisted = )
<davidascher> I think that gregw's opinion is going to matter a lot – he who writes the RFC will need to like the testing framework,no?
<gregw> Well - I am happy with jmeter, java or perl.
<gregw> so I'm a bit agnostic 
<gregw> my main concern
<gregw> is that the testing approach is not to write another client
<gregw> which might be the temptation in straight java
<CB1> agreed
<gregw> using something like jmeter may drive the tests more towards use-cae based testing
<gregw> use-case
<slightlyoff> gregw: well, we need a Java client = )
<gregw> but I never like the reference impl as test suite approach
<gregw> who/what tests the reference impl
<gregw> something that can simply play scenarios and put the right clientID into them is what we need to test servers
<gregw> more difficult to test clients
<CB1> correct me if i'm wrong, but the test suite isn't performance, rather submitting something and verifying the output? we don't need a cometd client to do that
<slightlyoff> CB1: yes
<slightlyoff> I think you're right
<gregw> If jmeter is good enough to extract clientID and put in subsequent request... then I guess I lean towards that
<slightlyoff> yeah, that seems fine
<gregw> what transports will we test?
<davidascher> I wonder if it's possible to do cross-implementation client testing, or whether each client implementation is going to require its own test harness
<CB1> i assume all transports will need testing
<slightlyoff> gregw: I think if we test long-poll, we're probably going to be in good shape...testing others is going to be much more difficult
<slightlyoff> gregw: but I understand what you're driving at
<gregw> I was thinking long polling and maybe jsonp
<gregw> or whatever it is called
<gregw> as that gives us basic operation and x-domain
<slightlyoff> gregw: yep
<slightlyoff> gregw: I think those will become the default transports in the real world
<gregw> but probably harder to test jsonp (what is it called in bayeux?)
<slightlyoff> gregw: yeah, but being able to see that we get JSON-P enclosures for all the verbs is really what we're driving at there
<gregw> yes- and that can be tested with long-polling and JSON-P is just an encapsulation of the same stuff
<gregw> so I would think a long polling test suite is the first objective.
<slightlyoff> gregw: yeah
<slightlyoff> so it sounds like we should use perl or java for that
<slightlyoff> I don't really care which
<slightlyoff> (the twisted guys don't really seem to have anything helpful)
<gregw> ditto - except that if it could be done in jmeter scripts, that would be OK as well
<slightlyoff> yeah, that's great by me
<gregw> so - any volunteers?
<slightlyoff> I think our biggest criteria needs to be that you can (modulo getting your dep chain setup) say "run tests http://someserver:port/cometd/"
<slightlyoff> or something like that...a simple way to fire off tests at a URL
<mst> Catalyst's test suite does that with $ENV
Unknown macro: {CATALYST_SERVER} 

<mst> (and all app test suites)
<slightlyoff> mst: ok, sure: %> $SERVER="whatever" ./run_tests
<slightlyoff> 
<markn> I wouldn't mind volunteering to look at some of the junit stuff, but just won't be able to get to it in near term
<markn> jmeter
<markn> sorry
<mst> slightlyoff: it really doesn't matter
<mst> my point was more 'the infrastructure is there'
<slightlyoff> mst: would you be willing to set up a harness that folks can write tests in?
<mst> if you can give me an idea of the reqs I can give it a go
<slightlyoff> mst: ok
<slightlyoff> mst: we need to be able to fire off the whole test suite in one command
<slightlyoff> mst: writing tests should mean writing new functions or adding new files in general
<slightlyoff> mst: and we need to test successive HTTP reuests in a single test
<mst> yeah, that's fine
<slightlyoff> mst: basically, we need a place to put tests, and a couple of small examples to show how to make requests and test the result values
<slightlyoff> mst: not big magic, but we need a place to do it
<slightlyoff> mst: oh, and it's gotta report failures = )
<slightlyoff> good enough?
<mst> yep 
mst giggles
<mst> I could do it inline on a POD copy of the spec, if you liked 
<slightlyoff> ok, I've gotta jump over to the Dojo meeting in 10 minutes
<slightlyoff> what's left on the agenda?
<gregw> OK - so at the dojo meeting... are you going to talk about putting RPC support intothe cometd impl?
<gregw> 
<slightlyoff> gregw: no, lets get that sorted = )
<gregw> The key thing that is missing....
<gregw> IMNSHO
<gregw> is the ability to return messges in any response
<gregw> that is now in the spec and I have done a patch for dojo long polling
<slightlyoff> gregw: is the patch in Dojo trac?
<gregw> nope - I sent it to the cometd list.... but I will put it in Dojo trac
<slightlyoff> gregw: I'm filing a bug on it right now
<gregw> excellent - I'll dig up the patch and append it once you have the bug number
<markn> one quick question from me, is there a dojo cometd client that has advice support? Haven't seen it yet
<slightlyoff> markn: no, my patches are rotting
<slightlyoff> gregw: attach it to this: http://trac.dojotoolkit.org/ticket/2519
<markn> ok, np, just wanted to make sure I was not missing anything
<slightlyoff> gregw: I'll make sure it gets merged for 0.4.2
<slightlyoff> markn: /me files dojo bug on that
<markn> thanks
<slightlyoff> #2520
<gregw> slightlyoff: how do I get username/password for trac?
<slightlyoff> gregw: use guest/guest
<gregw> patch attached
<slightlyoff> thanks
<gregw> anything else today?
<slightlyoff> I think we've got a busy week ahead
<slightlyoff> you're gonna work on the spec, and mst will have a test harness in place for next week
<davidascher> slightlyoff: I may explore py.test or pyunit to tackle the python client directly
<davidascher> but i can't commit to it
<slightlyoff> davidascher: that would be outstanding
<slightlyoff> davidascher: we should check w/ xantus to see if he'll give you committer privs
<davidascher> sure
<gregw> OK - bye all... got to run!
<markn> For the next meeting, I might have a few Bayeux protocol questions/concerns to discuss
<markn> but that can wait till then
<markn> or might post them to cometd-dev group
<slightlyoff> see everyone next week
<markn> bye all
<CB1> cya guys 
```



