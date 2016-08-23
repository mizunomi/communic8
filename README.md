The PICO-8 GPIO pins allow us to communicate in 128-bit chunks.

The first 8 bits are reserved as a header, the remaining 120 are the message.

## Creating a connection

```javascript
let bridge = connect();

// this starts a polling loop which will eat up CPU cyclces, so if you no longer need it, stop it:
bridge.stop();
```

## Executing an RPC

We create an RPC object as follows. These must be registered on both ends.

```javascript
let add = new RPC({
  id: 0, // a unique byte to identify the RPC
  args: [
    ArgTypes.Number,
    ArgTypes.Number
  ],
  ret: [
    ArgTypes.Number
  ]
});
```

We can then create an *invocation* as follows:

```javascript
let addInvocation = add(2, 3);
```

And send it to the bridge:

```javascript
let response = bridge.send(addInvocation);
```

`response` is now a promise which will evaluate to the return value of the RPC.


## headers
          0 if written by JS-land, 1 if written by pico-land
          |
          v
   00000000
         ^
         |
         1 if should be read, 0 if not (set to 0 by the reader)



## Packet-based format

9-byte packets,
1-byte header, then 14 9-byte packets.

each packet having a 1-byte header indicating its id

we operate under the assumption that every message will arrive in the order it is sent (which is a reasonable assumption)