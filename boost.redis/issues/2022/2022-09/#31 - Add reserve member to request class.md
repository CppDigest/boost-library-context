# #31 - Add reserve member to request class [Closed]

> Username: mzimbres  
> Created at: 2022-09-24 10:09:17 UTC  
> Updated at: 2022-09-24 13:26:31 UTC  
> Closed at: 2022-09-24 13:26:31 UTC  
> Url: https://github.com/boostorg/redis/issues/31  

> Klemens: the aedis::request object goes into the const_buffer as is?  
> Occase: Yes, it is the payload written to the socket.  
> Klemens: ok, then it's probably correct as long as request has a .reserve() member  
> Occase: I will add one, it is missing now.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-09-24 13:26:31 UTC  
> Url: https://github.com/boostorg/redis/issues/31#issuecomment-1256968585  

Implemented in https://github.com/mzimbres/aedis/commit/4e30a9d53d3d71f87dae553da62138d08a6e0cad.
