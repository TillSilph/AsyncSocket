# AsyncSocket
AsyncSocket is a simple implementation of WS communication using asynchronous methods.\
It consists of a Node.js, Web and WebMin modules. For Node.js is implemented by AsyncSocketServer, which is the main difference from a regular WebSocket.\
The server is what the 'connection' event returns AsyncSocket. \
AsyncSocket, in turn, is the same WebSocket client, but implements its own 'message' event and methods: .send .sendNoReply and .sendEmit
```js
//Server side
const wss = new AsyncSocketServer({port: 8080});

wss.on('connection', wsc=> {
    console.log("New connect");

    wsc.on('message', mess=>{
        if(mess.testReply) return mess.reply({message: "Great"});
        else console.log(mess);
    });
});
```
```js
//Client side
const wsc = await AsyncSocketClient(new WebSocket('ws://localhost:8080'));
//or
const ws = new WebSocket('ws://localhost:8080');
const wsc = await new Promise((resolve, reject) => {
    ws.on('open', async () => {
        const wsc = new AsyncSocket(ws);
        resolve(wsc);
    });
});

wsc.on('message', console.log);
const data = await wsc.send({testReply: true});
console.log(data.message); //=>"Great"
```
It is also possible to transfer events using the sendEmit method.
```js
//Server side
wss.on('connection', wsc=> {
    console.log("New connect");

    setInterval(()=>{
        wsc.sendEmit('interval', {message: "Great"})
    },5000);
});
```
```js
//Client side
wsc.on('interval', data=>console.log(data.message)); //=>"Great" Every 5 seconds 
```