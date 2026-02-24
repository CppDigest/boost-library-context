# #1314 - How could I read the HTTP requests header of an async WS server? [Closed]

> Username: naszta  
> Created at: 2018-11-21 20:31:41 UTC  
> Updated at: 2018-11-28 01:52:35 UTC  
> Closed at: 2018-11-28 01:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1314  

I'm using the latest boost version (1.68) and I would like to use the HTTP GET path for choosing the (my) internal message protocol. I've found some examples, but it's not clear for me how HTTP GET could be extracted. I don't want to implement a full featured HTTP server (examples are based on that one) the WS servers otherwise would be a really good fit.  
  
Many thanks!  
  
  
PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-21 20:38:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1314#issuecomment-440801520  

You don't need a "full" HTTP server, but you do need to read the HTTP request yourself. The documentation provides instructions on how to do this:  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
In order to inspect the HTTP message, you have to read it yourself. This shouldn't be terribly difficult, the advanced server examples demonstrate this. You can just leave out the call to `handle_request` since you aren't implementing a full HTTP server.
