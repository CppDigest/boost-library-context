# #915 - SSL Web Socket Error: stream truncated [Closed]

> Username: svmakarov  
> Created at: 2017-11-30 17:16:08 UTC  
> Updated at: 2017-12-01 15:14:15 UTC  
> Closed at: 2017-12-01 14:08:13 UTC  
> Url: https://github.com/boostorg/beast/issues/915  

Hello.  
  
I investigate boost.beast library to host web socket services in my c++ app.  
I try with two examples from beast library: websocket_server_sync.cpp and websocket_server_sync_ssl.cpp.  
  
Without SSL web socket works correctly.  
But if I run SSL web socket I get following error:  
Error: stream truncated  
  
It occurs during ws.accept().  
  
Could you please explain what the possible reason of such behavior?  
  
  
BOOST_BEAST_VERSION 124  
  
As client I use simple JavaScript:  
  
<script>  
var socket = new WebSocket("wss://127.0.0.1:8080");  
socket.onopen = function() {  
   socket.send("HelloWS");  
};  
socket.onclose = function(event) {};  
socket.onmessage = function(event) {};  
socket.onerror = function(error) {};  
</script>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-30 17:51:18 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348267184  

Try using different browsers. I have noticed that some browsers work and others don't. I believe it has something to do with the self-signed certificates that the beast examples use.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-30 17:55:25 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348268391  

Anyway "stream truncated" means that the other end closed the connection abruptly. Chrome does this often, it usually never performs the SSL closing handshake. Google says that behavior leads to better performance.

---

## Comment 3

> Username: svmakarov  
> Created at: 2017-12-01 09:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348448912  

I try to open in three different browsers, results is following:  
  
Firefox: Error: tlsv1 alert unknown ca  
Chrome: Error: stream truncated  
Opera: Error: stream truncated  
  
All errors occurs during ws.accept().  
  
Which browser do you use for this examples, or this problem specific for different machine and OS?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-12-01 12:41:23 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348485167  

Microsoft Edge browser worked for me. Chrome and Firefox did not.

---

## Comment 5

> Username: svmakarov  
> Created at: 2017-12-01 14:07:10 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348503145  

The problem was connected with self signed certificate.  
  
All works fine if in Firefox I add my server (localhost:8080) to Server Exception in Certificate Manager.  
  
And also I have tried to run ssl server with my certificate which is not self signed and which has correct Common Name (CN) = localhost and has correct Subj Alt Name = localhost. With my certificate SSL Web Sockets server works correctly in all browsers.  
  
Many thanks for your quick replies.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-12-01 15:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/915#issuecomment-348520264  

Glad to hear that you got it sorted! SSL certificates are a hassle...
