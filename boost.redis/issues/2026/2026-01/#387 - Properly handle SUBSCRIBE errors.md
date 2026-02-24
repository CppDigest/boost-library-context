# #387 - Properly handle SUBSCRIBE errors [Open]

> Username: anarthal  
> Created at: 2026-01-26 08:56:19 UTC  
> Updated at: 2026-01-26 08:56:19 UTC  
> Url: https://github.com/boostorg/redis/issues/387  

I've recently learned that SUBSCRIBE may error under circumstances other than a syntax error. For instance, when using ACL, the server returns an error if the user doesn't have access to the requested channels.  
  
I think we need to improve how we handle these errors. Currently, in the best scenario, they're interpreted as pushes as put into the receive response. But if other commands are on the flight, they will be interpreted as errors to these commands, completely messing up the connection. I can see attackers somehow crafting channel names to exploit this.  
  
I've seen that SUBSCRIBE immediately returns a push with a certain response. My proposal is attempting to identify this push (or the corresponding error), and fail the request if an error is seen.
