# #2293 - help trying to design a solution [Closed]

> Username: journeytosilius  
> Created at: 2021-07-30 00:01:56 UTC  
> Updated at: 2021-07-30 00:45:07 UTC  
> Closed at: 2021-07-30 00:45:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2293  

So, I'm new to websocket programming and I would like to ask a design question:  
  
The program I'm coding receives messages through a ZMQ socket, process them and then my goal is to serve websocket endpoints that will later be accessed by a React app that makes visualizations with the data being streamed.  
  
So, my question is: Is it possible to add multiple endpoints on the same port ? Since this app is pulling a lot of data from multiple ZMQ sockets, each on its thread and there are going to be a lot of endpoints served, separating the processed data for different visualization instances.  
  
Also, looking at the examples I'm a bit confused, which is the example I should start with to get a rough POC given the requirements ?   
  
Thank you very much !

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-30 00:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2293#issuecomment-889544146  

If you mean, "is it possible to lissten for connections on one port and accept multiple connections?", then yes. That is normal behaviour for a listening TCP socket.  
  
Any of the server examples will do that.
