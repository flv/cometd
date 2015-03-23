section of the [Bayeux Cometd Documentation](BayeuxCometdDocumentation.md).

# Bayeux Chat Example #

Chat is the "hello world" of web-2.0. This page describes how Bayeux can be used to implement a basic and more advanced chat room.

# Basic Chat #

With a basic chat, all users in the same "room" see all the chat published by all other users.  This has an almost 1:1 correlation with how a Bayeux channel works, so it is very simple to create a basic chat room:

  * Pick a Cometd server (eg Jetty )
  * Pick a cometd client library (eg dojotoolkit)
  * Create a webapplications that includes the cometd servlet and can server the client library.
  * Pick a channel name for your chat room: "/chat/demo"
  * Write client javascript that initializes cometd
```
dojox.cometd.init("http://myhost/mycontext/cometd");
```
  * Write client javascript that listens for chat in "/chat/demo"
```
var room = {
  _username: "myuser",
  _chat: function(message){
    var from=message.data.user;
    var text=message.data.chat;
    // do something with chat
   }
dojox.cometd.subscribe("/chat/demo", room, "_chat");
```
  * Write client javascript to tell the room you have joined:
```
dojox.cometd.publish("/chat/demo", { user: room._username, join:true, chat : room._user
name+" has joined"});
```
  * Write client javascript to publish anything typed by the user
```
chat: function(text){
  dojox.cometd.publish("/chat/demo", { user: room._username, chat: text});
},
```

and that's it.  No server side code is required as messages published by clients on a channel ("room") are by default broadcast to all subscribers the that channel ("room").

This example can be seen as part of the  [cometd demo](http://svn.codehaus.org/jetty-contrib/jetty/tags/jetty-6.1.7/contrib/cometd/demo/src/main/webapp/examples/chat/)'s

# User List #

But a basic chat room is not very functional.  Most reasonable chat rooms will provide a list of users that are currently in the room that will be updated as users join and leave.

## Client side user list ##

It is possible to maintain a list of users without implementing any server side code. Each client simply needs to watch for join messages and publish an empty message in response:
```
  _chat: function(message){
    var from=message.data.user;
    var text=message.data.chat;
    if (message.data.join) {
      dojox.cometd.publish("/chat/demo", { user: room._username});
    }
    updateMember(from);
    if (text) {
      addChat(from,text);
    }
  }
```
Thus soon after joining, every client will receive a message from every other client in the room, and thus will be able to update their list of members.  A similar technique can be done with leave messages to remove users from the members list.

While this mechanism works, it is not optimal as:
  * The emtpy messages are needed only by the newly joining client, but they are published to the channel and thus delivered to every client. This wastes bandwidth and causes unnecessary server activity.
  * The member messages will not arrive at once, so the member list will visibly be updated many times.
  * The mechanism is not reliable for leaving, as some clients my silently be disconnected.

## Server side user list ##
If the chat room member list is maintained on the server side, then the server can send a list of existing members to each new client as they join the list.

Using the proposed java API, this can be done as follows:
```

// Create data structure to hold member lists (should be class field)
final ConcurrentMap<String,Set<String>> members = new ConcurrentHashMap<String,Set<String>>();

// Get the Bayeux API instance
final Bayeux bayeux=(Bayeux)servletContext.getAttribute(Bayeux.DOJOX_COMETD_BAYEUX);

// Create a server side client
final Client client = bayeux.newClient("chat");
        
// Subscribe the client to all chat rooms
bayeux.getChannel("/chat/**",true).subscribe(client);

// Add a message listener
client.addListener(new MessageListener(){
    public void deliver(Client joiner, final Client client, Message msg)
    {
        // extract channel and data
        String channel=msg.getChannel();
        Map<?,?> data = (Map<?,?>)msg.getData();
                
        // if the message is a join message
        if (Boolean.TRUE.equals(data.get("join")))
        {
            // get the set of members (in convoluted concurrent style)
            Set<String> tmp = members.get(channel);
            if (tmp==null)
            {
                Set<String> new_list=new CopyOnWriteArraySet<String>();
                tmp=members.putIfAbsent(channel,new_list);
                if (tmp==null)
                    tmp=new_list;
            }            
            final Set<String> members=tmp;
            
            // add the user to the member set
            final String username=(String)data.get("user");        
            members.add(username);
            
            // send the members just to the new member
            // (other members will see the join message)
            joiner.deliver(client,channel,members,msg.getId());

            // add a remove listener to ensure we know the user has left
            joiner.addListener(new RemoveListener(){
                public void removed(String clientId, boolean timeout)
                {
                    // remove user from members set
                    members.remove(username);

                    // publish new list to all members
                    bayeux.getChannel(channel,false).publish(client,bayeux,null);
                }
            });
                    
        }
     }
});
```

The important things to note with this code are:
  * The server side can have bayeux clients
  * The server side can originate a message published to a channel
  * A message may be published to all subscribers to a channel, or delivered
> > to just a single subscriber to a channel.
  * Listeners can be used to handle removal of clients

The client side code needs to be modified to handle handle receipt of the members array differently to the receipt of a chat message:
```
  _chat: function(message){
    if (message.data instanceof Array) {
      updateMembers(message.data);
    }
    else {
      var from=message.data.user;
      if (message.data.join) {
        updateMember(from);
      }

      var text=message.data.chat;
      if (text) { 
        addChat(from,text);
      }
    }
  }
```

# Private Messages #

# Security and Validation #


---

[prev](BayeuxCometdDocumentation.md) [up](BayeuxCometdDocumentation.md) [next](BayeuxCometdDocumentation.md)

---
