# #498 - Option to disable setting charset on pooled connections resets? [Open]

> Username: bruno-viva  
> Created at: 2026-02-05 00:40:58 UTC  
> Updated at: 2026-02-14 23:37:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/498  

Hi!  
  
I launched my migration to boost mysql recently. I noticed that due to #187 , the charset is set every time I return a connection to the pool (I have to reset state due to using transactions, most of the time).  
  
This causes `SET NAMES 'utf8mb4'` to be the number one query in the statistics (this extra operation likely does not cause any noticeable performance hit). But, since MySql 8+ servers already have `utf8mb4` as default, is there an easy way to disable that additional SET query?  
  
Thanks in advance

---

## Comment 1

> Username: anarthal  
> Created at: 2026-02-05 13:34:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/498#issuecomment-3853703577  

I see. At the moment this is not possible. The `SET NAMES` query is pipelined with the reset operation, so it shouldn't incur into much cost.  
  
I can implement an option to skip this `SET NAMES` and assume that connections always use `utf8mb4`. But it hsa some implications.  
  
`any_connection` [tracks the character set that the connection is using](https://www.boost.org/doc/libs/latest/libs/mysql/doc/html/mysql/charsets.html). This is required to make `with_params` work. At the moment, resetting the connection sets the tracked character set to "don't know", since we don't know in the general case. So we'd need an option for the user to say "I guarantee that the server's default is utf8mb4, and I take responsibility if it's not".  
  
What I'd do is:  
  
* Add a `bool pool_params::assume_utf8mb4`, defaulting to `false`. If the user sets it to true, they assure us that the server's default is `utf8mb4`. If that's the case, we:  
  * Send a `connection_params::connection_collation` equal to zero when connecting. This means "use the server's default".  
  * Send only the reset, and not the `SET NAMES`, when cleaning up connections.  
  * Always maintain `utf8mb4` as the connection's tracked character set.  
* Implementing this requires changes to `any_connection` to tell it "don't set the tracked character set to unknown on reset". I can add another `bool any_connection_params::assume_utf8mb4` flag with similar semantics.  
  
Would this suit your needs?

---

## Comment 2

> Username: bruno-viva  
> Created at: 2026-02-05 17:01:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/498#issuecomment-3854949244  

Thanks @anarthal . I was thinking on something more generic, like adding to the pool_params a setting like:  
  
```cpp  
// If set, assumes that the server uses this character set, and does not force the utf8mb4 as the default connection charset.  
std::optional<character_set> server_charset;  
```  
  
If server_charset it means doing those 3 steps as you mentioned, but mantain the `server_charset` as the connection's tracked character set. This would work for any charset the user wants.  
  
Lower priority: Can the same be done for the collation? I am just thinking if there are cases where the charset can be the server's default, but the collation needs to be changed.

---

## Comment 3

> Username: anarthal  
> Created at: 2026-02-14 20:47:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/498#issuecomment-3902495954  

Connection establishment actually sets the collation. By using either the flag I proposed or the `server_charset` you're suggesting, connection establishment would have to set a collation ID equal to 0, which effectively means "please use the server's default". The collation is not required by the client-side anyhow. `async_reset_connection` will also reset the collation to the server's default.  
  
So I'm not sure what you're asking for in your second point exactly, can you please clarify?  
  
Otherwise, your proposal makes sense and is doable. It will target either Boost 1.91 if we're very lucky, otherwise 1.92.

---

## Comment 4

> Username: bruno-viva  
> Created at: 2026-02-14 23:37:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/498#issuecomment-3902788542  

Got it, please discard my second point. Thank you @anarthal !
