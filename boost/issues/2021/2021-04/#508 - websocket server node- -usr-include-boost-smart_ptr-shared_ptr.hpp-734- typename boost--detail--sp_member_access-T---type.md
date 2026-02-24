# #508 - websocket server node: /usr/include/boost/smart_ptr/shared_ptr.hpp:734: typename boost::detail::sp_member_access<T>::type [Closed]

> Username: newcanopies  
> Created at: 2021-04-23 23:23:53 UTC  
> Updated at: 2021-05-27 16:13:31 UTC  
> Closed at: 2021-05-27 16:13:31 UTC  
> Url: https://github.com/boostorg/boost/issues/508  

Hi everyone,   
  
I have two ROS2 packages, each need to run their own websocket server on the same PORT.  
  
Is that's what's throwing this boost:: error **gazebo::transport::Publication***]: **Assertion px != 0' failed.** ?   
   
```  
node: /usr/include/boost/smart_ptr/shared_ptr.hpp:734: typename boost::detail::sp_member_access<T>::type boost::shared_ptr<T>::operator->() const [with T = gazebo::transport::Publication; typename boost::detail::sp_member_access<T>::type = gazebo::transport::Publication*]: Assertion `px != 0' failed.  
```  
  
What does the `Assertion px fail` mean?  
 is there a websocket server configuration that can allow two different ROS nodes to listen on the same PORT?  
  
Thank you

---

## Comment 1

> Username: newcanopies  
> Created at: 2021-04-24 10:22:08 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-826070893  

is it a threading issue?

---

## Comment 2

> Username: newcanopies  
> Created at: 2021-04-24 10:42:14 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-826073075  

this is the boost line throwing the **Assertion px** error  
[smart_ptr/include/boost/smart_ptr/shared_ptr.hpp](https://github.com/boostorg/smart_ptr/blob/f3424e74e83821ebd616dc3e54c9c1f426af3886/include/boost/smart_ptr/shared_ptr.hpp#L732-L735)  
  
```  
    typename boost::detail::sp_array_access< T >::type operator[] ( std::ptrdiff_t i ) const BOOST_SP_NOEXCEPT_WITH_ASSERT  
    {  
        BOOST_ASSERT( px != 0 );  
        BOOST_ASSERT( i >= 0 && ( i < boost::detail::sp_extent< T >::value || boost::detail::sp_extent< T >::value == 0 ) );  
```

---

## Comment 3

> Username: DMaroo  
> Created at: 2021-05-27 11:40:11 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-849561800  

Boost is checking whether the pointer is null or not before de-referencing it in the definition of the [ ] (indexing) operator. I therefore think that either your code is passing null pointers to boost, or any of the ROS2 packages is doing so. In either case, there is no change needed in the boost's source code, rather, check your code and ROS2 packages' source code (I'm guessing Gazebo's causing this) once.

---

## Comment 4

> Username: newcanopies  
> Created at: 2021-05-27 12:51:15 UTC  
> Updated at: 2021-05-27 12:51:43 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-849607537  

> Boost is checking whether the pointer is null or not before de-referencing it in the definition of the [ ] (indexing) operator.  
  
Thanks for this tip!   
How to debug the pointer that is being passed?   
  
these are the two ROS packages attempting to create a websocket connection on the same port:  
  
package1 <details>  
```  
const rosbridge = require('../index.js');  
const app = require('commander');  
const pkg = require('../package.json');  
  
const rclnodejs = require('rclnodejs');  
const WebSocket = require('ws');  
const Bridge = require('./lib/bridge.js');  
const debug = require('debug')('ros2-web-bridge:index');  
  
// rclnodejs node  
let node;  
  
// Websocket server (or client if client mode set via --address)  
let server;  
let connectionAttempts = 0;  
  
// Map of bridge IDs to Bridge objects  
let bridgeMap = new Map();  
  
function closeAllBridges() {  
  bridgeMap.forEach((bridge, bridgeId) => {  
    bridge.close();  
  });  
}  
  
function shutDown(error) {  
  // Closing the server triggers the individual connections to be closed.  
  if (server) {  
    server.close();  
  }  
  if (!rclnodejs.isShutdown()) {  
    rclnodejs.shutdown();  
  }  
  if (error) {  
    throw error;  
  }  
}  
  
function createServer(options) {  
  options = options || {};  
  options.address = options.address || null;  
  process.on('exit', () => {  
    debug('Application will exit.');  
    shutDown();  
  });  
  return rclnodejs.init()  
    .then(() => {  
      node = rclnodejs.createNode('ros2_web_bridge');  
      rclnodejs.spin(node);  
      debug('ROS2 node started');  
      createConnection(options);  
    })  
    .catch(error => shutDown(error));  
}  
  
function createConnection(options) {  
  if (options.address != null) {  
    debug('Starting in client mode; connecting to ' + options.address);  
    server = new WebSocket(options.address);  
  } else {  
    options.port = options.port || 8080;  
    //options.port = options.port || 9090;  
    debug('Starting server on port ' + options.port);  
    server = new WebSocket.Server({port: options.port});  
  }  
  
  const makeBridge = (ws) => {  
    let bridge = new Bridge(node, ws, options.status_level);  
    bridgeMap.set(bridge.bridgeId, bridge);  
  
    bridge.on('error', (error) => {  
      debug(`Bridge ${bridge.bridgeId} closing with error: ${error}`);  
      bridge.close();  
      bridgeMap.delete(bridge.bridgeId);  
    });  
  
    bridge.on('close', (bridgeId) => {  
      bridgeMap.delete(bridgeId);  
    });  
  };  
  
  server.on('open', () => {  
    debug('Connected as client');  
    connectionAttempts = 0;  
  });  
    
  if (options.address) {  
    makeBridge(server);  
  } else {  
    server.on('connection', makeBridge);  
  }  
  
  server.on('error', (error) => {  
    closeAllBridges();  
    debug(`WebSocket error: ${error}`);  
  });  
  
  server.on('close', (event) => {  
    debug(`Websocket closed: ${event}`);  
    if (options.address) {  
      closeAllBridges();  
      connectionAttempts++;  
      // Gradually increase reconnection interval to prevent  
      // overwhelming the server, up to a maximum delay of ~1 minute  
      // https://en.wikipedia.org/wiki/Exponential_backoff  
      const delay = Math.pow(1.5, Math.min(10, Math.floor(Math.random() * connectionAttempts)));  
      debug(`Reconnecting to ${options.address} in ${delay.toFixed(2)} seconds`);  
      setTimeout(() => createConnection(options), delay*1000);  
    }  
  });  
  
  let wsAddr = options.address || `ws://localhost:${options.port}`;  
  console.log(`Websocket started on ${wsAddr}`);  
}  
  
module.exports = {  
  createServer: createServer,  
};  
  
```  
</details>  
  
package2 <details>  
```  
const WebSocketServer = require('./node_modules/websocket').server;  
const http = require('http');  
const fs = require('fs');  
const path = require('path');  
const gzbridge = require('./build/Debug/gzbridge');  
  
  
// static serve path  
const staticBasePath = './bin/';  
  
//Port to serve from, defaults to 8080  
const port = process.argv[2] || 9090;  
  
  
// Array of websocket connections currently active, if it is empty, there are no  
 * clients connected.  
let connections = [];  
  
  
  
// Whether currently connected to a gzserver  
let isConnected = false;  
  
/**  
 * Callback to serve static files  
 * @param req Request  
 * @param res Response  
 */  
let staticServe = function(req, res) {  
  
  // CORS  
  res.setHeader('Access-Control-Allow-Origin', '*');  
  res.setHeader('Access-Control-Request-Method', '*');  
  res.setHeader('Access-Control-Allow-Methods', 'OPTIONS, GET');  
  res.setHeader('Access-Control-Allow-Headers', '*');  
  
  let fileLoc = path.resolve(staticBasePath);  
  
  if (req.url === '/')  
    req.url = 'index.html';  
  
  fileLoc = unescape(path.join(fileLoc, req.url));  
  
  fs.readFile(fileLoc, function(err, data) {  
    if (err) {  
        console.error('File not found [', fileLoc, ']');  
        res.writeHead(404, 'Not Found');  
        res.write('404: File Not Found!');  
        return res.end();  
    }  
  
    res.statusCode = 200;  
  
    res.write(data);  
    return res.end();  
  });  
};  
  
// HTTP server  
let httpServer = http.createServer(staticServe);  
httpServer.listen(port);  
  
console.log(new Date() + " Static server listening on port: " + port);  
  
// Websocket  
let gzNode = new gzbridge.GZNode();  
  
// Start websocket server  
let wsServer = new WebSocketServer({  
  httpServer: httpServer,  
  // You should not use autoAcceptConnections for production  
  // applications, as it defeats all standard cross-origin protection  
  // facilities built into the protocol and the browser.  You should  
  // *always* verify the connection's origin and decide whether or not  
  // to accept it.  
  autoAcceptConnections: false  
});  
  
wsServer.on('request', function(request) {  
  
  // Accept request  
  let connection = request.accept(null, request.origin);  
  
  // If gzserver is not connected just send material scripts and status  
  if (!gzNode.getIsGzServerConnected())  
  {  
    // create error status and send it  
    let statusMessage =  
        '{"op":"publish","topic":"~/status","msg":{"status":"error"}}';  
    connection.sendUTF(statusMessage);  
    // send material scripts message  
    connection.sendUTF(materialScriptsMessage);  
    return;  
  }  
  
  connections.push(connection);  
  
  if (!isConnected)  
  {  
    isConnected = true;  
    gzNode.setConnected(isConnected);  
  }  
  
  console.log(new Date() + ' New connection accepted from: ' + request.origin +  
      ' ' + connection.remoteAddress);  
  
  // Handle messages received from client  
  connection.on('message', function(message) {  
    if (message.type === 'utf8') {  
      console.log(new Date() + ' Received Message: ' + message.utf8Data +  
          ' from ' + request.origin + ' ' + connection.remoteAddress);  
      gzNode.request(message.utf8Data);  
    }  
    else if (message.type === 'binary') {  
      console.log(new Date() + ' Received Binary Message of ' +  
          message.binaryData.length + ' bytes from ' + request.origin + ' ' +  
          connection.remoteAddress);  
      connection.sendBytes(message.binaryData);  
    }  
  });  
  
  // Handle client disconnection  
  connection.on('close', function(reasonCode, description) {  
    console.log(new Date() + ' Peer ' + request.origin + ' ' +  
        connection.remoteAddress + ' disconnected.');  
  
    // remove connection from array  
    let conIndex = connections.indexOf(connection);  
    connections.splice(conIndex, 1);  
  
    // if there is no connection notify server that there is no connected client  
    if (connections.length === 0) {  
      isConnected = false;  
      gzNode.setConnected(isConnected);  
    }  
  });  
});  
  
// If not connected, periodically send messages  
if (gzNode.getIsGzServerConnected())  
{  
  setInterval(update, 10);  
  
  function update()  
  {  
    if (connections.length > 0)  
    {  
      let msgs = gzNode.getMessages();  
      for (let i = 0; i < connections.length; ++i)  
      {  
        for (let j = 0; j < msgs.length; ++j)  
        {  
          connections[i].sendUTF(msgs[j]);  
        }  
      }  
    }  
  }  
}  
```

---

## Comment 5

> Username: DMaroo  
> Created at: 2021-05-27 14:26:02 UTC  
> Updated at: 2021-05-27 14:32:43 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-849680297  

It seems more likely that the error arises from somewhere within the included packages of ROS2. You can reproduce the exact error by compiling and running the following C++ code:  
  
```c++  
#include <boost/smart_ptr/shared_ptr.hpp>  
  
struct two_ints  
{  
    int a;  
    int b;  
};  
  
int main(int argc, char* argv[])  
{  
    boost::shared_ptr<two_ints> new_pointer;  
  
    boost::shared_ptr<two_ints> my_pointer = new_pointer;  
  
    delete new_pointer.get();  
  
    int a_1 = my_pointer->a;  
}  
```  
  
It will give the following error:  
  
``a.out: /usr/include/boost/smart_ptr/shared_ptr.hpp:728: typename boost::detail::sp_member_access<T>::type boost::shared_ptr<T>::operator->() const [with T = two_ints; typename boost::detail::sp_member_access<T>::type = two_ints*]: Assertion `px != 0' failed.``  
  
So, I'm guessing that somewhere in one of ROS2's packages' code, somebody did deleted the raw pointer of the shared_pointer and then de-referenced that shared_pointer. To be honest, I don't think I am qualified enough to look into ROS2's code. However, you can take a look at it and maybe do a PR to fix it :)

---

## Comment 6

> Username: newcanopies  
> Created at: 2021-05-27 16:13:31 UTC  
> Url: https://github.com/boostorg/boost/issues/508#issuecomment-849762226  

Thank you very much @DhruvMaroo for the great example !
