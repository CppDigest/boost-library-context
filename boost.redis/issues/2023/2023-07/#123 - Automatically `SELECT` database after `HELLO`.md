# #123 - Automatically `SELECT` database after `HELLO`? [Closed]

> Username: ysc3839  
> Created at: 2023-07-24 09:13:34 UTC  
> Updated at: 2023-08-06 09:00:35 UTC  
> Closed at: 2023-08-06 09:00:35 UTC  
> Url: https://github.com/boostorg/redis/issues/123  

Currently there's not an `on_connect` callback, so it would be helpful to execute `SELECT` automatically after `HELLO`.

---

## Comment 1

> Username: criatura2  
> Created at: 2023-07-24 09:23:16 UTC  
> Url: https://github.com/boostorg/redis/issues/123#issuecomment-1647540482  

Good point. It would be even better if the `HELLO` command would allow `SELECT`ing automatically since this is the first command to be executed.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-07-24 21:52:33 UTC  
> Url: https://github.com/boostorg/redis/issues/123#issuecomment-1648677700  

This looks like an important addition, I will think more about where exactly it should be implemented. We can put it in the connection `config` so that if set it gets sent with `HELLO`.
