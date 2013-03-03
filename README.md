ircbridged
==========

Daemon that bridges IRC to JSON-RPC over UDP.

Usefull for tools or daemons that need to interact with IRC but don't want a
persistent connection or the overhead of handling IRC stuff themselves.

Installing & Running
--------------------

```
% npm install ircbridged
% ircbridged 
Options:
  --server      IRC server address     [required]
  --port        IRC server port        [default: 6667]
  --nick        IRC nick               [default: "ircbridge"]
  --realname    IRC realname           [default: "IRC Bridge"]
  --ssl         Use SSL                [default: false]
  --listenport  UDP port for JSON-RPC  [default: 41234]
```

Testing
-------

You can talk to the bridge locally using nc. For example:

```
% nc -u 127.0.0.1 41234
{"method": "join", "params": ["#channel"]}
{"method": "message", "params": ["#channel", "Hello World!"]}
```

Client
------

There's a client library for Node.js in https://github.com/calmh/node-ircbridge.

Commands
--------

### Join

Join an IRC channel.

Params: `channel`

```
{"method": "join", "params": ["#channel"]}
{"method": "join", "params": ["#channel aChannelPassword"]}
```

### Part

Leave an IRC channel.

Params: `channel`

```
{"method": "part", "params": ["#channel"]}
```

### Message

Send a privmsg to a channel or individual.

Params: `target`, `message`

```
{"method": "message", "params": ["#channel", "Hello all!"]}
{"method": "message", "params": ["someNick", "Hey there!"]}
```

### Notice

Send a notice to a channel.

Params: `target`, `message`

```
{"method": "notice", "params": ["#channel", "This is a channel notice!"]}
```

### Subscribe

Subscribe to messages on a channel. For the duration of the
subscription, any messages on the channel will be sent to the subscriber
as "message" events.

Params: `target`

```
{"method": "subscribe", "params": ["#channel"]}
```

A `subscribed` event is returned on successfull subscription. The event
contains the channel name and the subscription duration in milliseconds.
To continue receiving messages from the channel, the subscription needs
to be renewed by sending a new `subscribe` command before the expiration
time.

```
{"method": "subscribed", "params": ["#channel", 1800000]}
```

The message events are in the form of `message` notifications with
`params` being `from`, `to` and `message`.

```
{"method":"message","params":["someNick","#channel","hi everyone"]}
```

