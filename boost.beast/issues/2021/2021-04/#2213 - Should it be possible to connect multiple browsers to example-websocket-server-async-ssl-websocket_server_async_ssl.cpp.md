# #2213 - Should it be possible to connect multiple browsers to example/websocket/server/async-ssl/websocket_server_async_ssl.cpp? [Closed]

> Username: SAlsum  
> Created at: 2021-04-13 23:02:39 UTC  
> Updated at: 2022-01-09 05:17:24 UTC  
> Closed at: 2022-01-09 05:17:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2213  

I copied the example mentioned in the title (https://www.boost.org/doc/libs/master/libs/beast/example/websocket/server/async-ssl/websocket_server_async_ssl.cpp) exactly, except for changing the   
> load_server_certificate(ctx);  
  
line to load in my own server information, and also adding a std::cout to the beginning of most of the functions so I could track the flow of the program to verify I understood how things were working.  
  
I also created a simple webpage to test it out, the page just has 3 buttons, 1 to open a websocket, one to send a pre-written message ("Sending another message"), and one to close the websocket (code at the bottom if interested).  
  
Opening the WebSocket and sending the messages works exactly as it should, I receive back the echoed response, however, if I try to open up a second browser and connect a second time while the first one is still open, it just times out instead of connecting.  
  
My understanding is that because the "on_accept()" handler in the listener class calls another instance of "do_accept()", which calls another instance of "acceptor_.async_accept()", that the server should be waiting for another connection attempt and should be able to handle it.  Indeed, this is what is _seems_ to be happening based on the print statements I added, except that the second listener "on_accept()" handler is never being called the second time, indicating the server never hears the second connection attempt:  
  
> ~/networking/asioPractice$ ./echo_server.exe 0.0.0.0 6164 1  
> Running listener async_accept (do_accept).  
> Calling listener on_accept handler.  
> Calling session run.  
> Calling session on_run handler.  
> Running listener async_accept (do_accept).  
> Calling session on_handshake handler.  
> Calling session on_accept handler.  
> starting session do_read  
> Calling session on_read handler.  
> Recieved message: socket open  
> Calling session on_write handler.  
> starting session do_read  
> Calling session on_read handler.  
> Recieved message: sending another message  
> Calling session on_write handler.  
> starting session do_read  
  
The above is the result of starting the server, clicking the "create socket" button on my html page, then clicking the "send message" button on my html page, then attempting to click the "create socket" button on a second instance of that page open on another computer (no print statements occur due to this final act, indicating it is not heard).   
  
The listener's "do_accept()" function was used once at the beginnig, and the "on_accept()" handler was called, indicating the connection went forward, after which the listener's "do_accept()" function was called again, indicating a second "async_accept()" should have been run (which queues the actual action to be run by the io_context? I'm a little fuzzy on the terminology as I'm very new to this), but this time, it does not seem to notice when I attempt to make a connection.  
  
What am I missing?  
  
----------------------------  
test webpage  
```  
<!DOCTYPE HTML>  
  
<html>  
  <head>  
      
    <script>  
      function openConnection(){  
	  if(window.WebSocket){  
	      // Open a web socket  
	      ws = new WebSocket("wss://shaun.ralsum.com:6164");  
	      ws.onopen = function(){  
		  ws.send("socket open");  
		  document.getElementById('socketStatus').innerHTML =  
		      "socket connected";  
	      };  
	      ws.onmessage = function(evt){  
		  var recievedMessage = evt.data;  
		  document.getElementById('serverResponse').innerHTML =  
		      recievedMessage;  
	      };  
	  } else{  
	      document.getElementById('socketStatus').innerHTML =  
		  "WebSockets not supported";  
	  }  
      }  
  
      function sendMessage(){  
	  // use global web socket object to send a message  
	  ws.send("sending another message");  
      }  
        
      function closeConnection(){  
	  // close the web socket connection  
	  ws.close();  
	  document.getElementById('socketStatus').innerHTML =  
	      "socket disconnected";  
      }  
    </script>  
      
  </head>  
    
  <body>  
    <p id="socketStatus">socket disconnected</p>  
    <button onclick="openConnection()">create socket</button>  
    <button onclick="sendMessage()">send message</button>  
    <button onclick="closeConnection()">close socket</button>  
    <p id="serverResponse">response from server</p>  
  </body>  
    
</html>  
```

---

## Comment 1

> Username: SAlsum  
> Created at: 2021-04-17 03:30:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-821758657  

**UPDATE:**  
  
It seems that I can indeed connect multiple browsers, however, I can only have one browser on my local (home) network connected at once.  I can try multiple different computers as well as my phone, and they all interfere, however if I turn my phone's wifi off so it is not connecting via my network, I can simultaneously connect it and another computer.  
Why is this happening?  
  
Note: when I check netstat, the listener displays  
  
> tcp        0      0 0.0.0.0:6164            0.0.0.0:*               LISTEN      26866/./serv...  
  
and when any compuer (or my phone) on my network is connected it displays  
  
> tcp        0      0 10.0.2.15:6164          10.0.2.2:6164           ESTABLISHED 26866/./serv...  
  
but when I disconnect my phone from my network it reads  
  
> tcp        0      0 10.0.2.15:6164          10.0.2.2:51616          ESTABLISHED 26866/./serv...  
  
(the 51616 number changes every time I try to repeat).  
  
I assume this has something to do with it but I am unsure how...

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-04-17 10:07:24 UTC  
> Updated at: 2021-04-17 10:07:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-821799985  

> tcp 0 0 0.0.0.0:6164 0.0.0.0:* LISTEN 26866/./serv...  
  
This means your server is listening on all interfaces on port 6164  
  
> tcp 0 0 10.0.2.15:6164 10.0.2.2:6164 ESTABLISHED 26866/./serv...  
  
The remote computer picked the port 6164 to connect from (that's odd). the remote computer is at address 10.0.2.2 and connected to your host's 10.x.x.x network (your home LAN?)  
  
> tcp 0 0 10.0.2.15:6164 10.0.2.2:51616 ESTABLISHED 26866/./serv...  
  
Another connection from the same host, this time originating on ephemeral port 51616 (expected)   
  
Is your phone's wifi doing something strange like creating a hotspot?

---

## Comment 3

> Username: SAlsum  
> Created at: 2021-04-18 10:47:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-821971655  

> The remote computer picked the port 6164 to connect from (that's odd).  
  
This is almost certainly the crux of the issue.  Every time I connect with any computer on my LAN this happens.  
  
Not using a hotspot, but my configuration may be somewhat unorthodox.  
  
My server is running on a virtual machine (Oracle VM) on a desktop on my home network, I forward port 6164 (along with some others like 80, 443) from my router to my desktop, which in turn forwards them to my VM (the 10.0.2.15 machine).  The 10.0.2.2 address seems to be the VM's gateway to my desktop.  See the diagram below for clarification.  
  
![image](https://user-images.githubusercontent.com/16842373/115142476-c45afa80-9ff6-11eb-98d7-55076852cffd.png)  
  
Whenever I connect the websocket with any device on my network (my computer running the VM, my phone connected to wifi, my laptop, my VMs themselves) the result of netstat on the VM shows the incoming connection as 10.0.2.2:6164.  However, when connecting with any device outside of my LAN (a friend's computer, or my phone on data instead of wifi) a random "ephemeral" port is used (10.0.2.2:#####), which you noted was the expected behavior.  
  
![image](https://user-images.githubusercontent.com/16842373/115142570-5bc04d80-9ff7-11eb-832d-085b65c30062.png)  
  
It seems that whatever process causes a browser to use these ephemeral ports either only occurs for connections originating outside of my router, or else is overridden for connections on my side.  I attempted changing the listening and forwarded ports to 6165, and 6166 with the same result (the remote connections on my side of the router used 6165, then 6166).  
  
I am at a loss as to why this would be the case.  Any ideas?  
  
  
Also, if I'm setting up my connection in a really stupid way I'm open to changing, first time I've tried to do anything with networking.  Would like to solve this mystery though...

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-04-18 11:14:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-821974968  

This is definitely a networking/docker question rather than a beast/c++ question.  
I don't know much about docker's port-forwarding.  
Is it worth asking a stack overflow question on this?  
The relevant question is about the network behaviour, not the server application, which is clearly working fine.  
  
Re ephemeral ports, the originating host chooses the port number from the ephemeral range whenever you bind the client's outgoing socket to port 0 (which you should almost always do).  
  
I would expect the port forwarding in docket to choose a new ephemeral port for the onward connection to the service in the container. But it may be that you have specified bridged networking rather than NAT when you built the docker network?  
  
As I say, I'm no domain expert here but I suspect your issue is somewhere around there.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-850857844  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2213#issuecomment-1008232416  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
