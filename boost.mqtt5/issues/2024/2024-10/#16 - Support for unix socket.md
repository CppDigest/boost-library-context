# #16 - Support for unix socket [Open]

> Username: Project579  
> Created at: 2024-10-17 20:06:06 UTC  
> Updated at: 2024-10-18 15:34:44 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/16  

It would be nice if UNIX Sockets were supported as a transport medium, this allows creating safer, easier to manage and more performant IPC client/server communication via MQTT on *nix systems.  
  
I have not looked at the code yet but the fact that the library is largely based on Asio should allow for a simple implementation, correct me if I'm wrong.

---

## Comment 1

> Username: siladic  
> Created at: 2024-10-18 15:34:43 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/16#issuecomment-2422744645  

That’s definitely a good idea. The implementation would require some caution, as Asio’s UNIX socket stream interface differs slightly from the TCP stream interface. However, it’s certainly feasible. We’ll add it to our roadmap.
