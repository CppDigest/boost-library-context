# #379 Adds type-safe command wrappers to request [Closed]

> Username: anarthal  
> Created at: 2026-01-07 11:11:53 UTC  
> Updated at: 2026-01-09 19:48:29 UTC  
> Closed at: 2026-01-09 19:48:28 UTC  
> Url: https://github.com/boostorg/redis/pull/379  



---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-07 11:24:20 UTC  
> Updated_at: 2026-01-07 11:26:23 UTC  
> Url: https://github.com/boostorg/redis/pull/379#issuecomment-3718432198  

I've created this PR to discuss on how to proceed with type-safe command wrappers. These are needed for:  
  
1. PubSub state restoration (#367), for the `SUBSCRIBE`, `PSUBSCRIBE`, `UNSUBCRIBE` and `PUNSUBSCRIBE` commands. We need to inspect all the channels the user is manipulating, so we know how to resubscribe on reconnection.  
2. Cluster (#374), for all commands that act on a key. We need to compute the key's hash so we know which node we should send each command to.  
  
I'm concerned about the size that this would add to `request`, specially for point 2, since most commands act on a key. I've generated the current prototype with AI just to get a feel of the size this would have.  
  
I see several options here:  
  
1. Go with this approach. I'd open another PR supporting only the ones required for PubSub, properly implemented and tested.  
2. Go with this approach for PubSub state restoration, but think of something else for cluster. For example:  
  
```cpp  
req.push_get("mykey"); // GET is common, so we have a command for it that computes the key's hash  
req.push_routed(hash_of("mykey"), "GEOADD", "mykey", 1.35, 2.05, "Palermo"); // GEOADD is not common, so the user needs to provide how to hash the command manually  
```  
  
3. Think of something more modular. The best I've been able to come up with is:  
  
```cpp  
req.push_cmd(cmds::get, "mykey");  
req.push_cmd(cmds::subscribe, std::array{"chan1", "chan2"}); // doesn't work without the explicit std::array because push_cmd forwards arguments  
```  
  
or  
  
```cpp  
req.cmd(cmds::get).args("mykey");  
req.cmd(cmds::subscribe).args({"chan1", "chan2"}); // supports initializer lists  
```  
@mzimbres what's your view on this?

---

## Comment 2

> Username: anarthal  
> Created_at: 2026-01-09 19:48:28 UTC  
> Url: https://github.com/boostorg/redis/pull/379#issuecomment-3730351806  

Closing this as it was a prototype for discussion, and the direction has been set to explicit functions like `request::subscribe`.

---
