# #376 ClickHouse Integration [Open]

> Username: Anefu  
> Created at: 2024-10-30 17:33:59 UTC  
> Updated at: 2024-10-30 19:17:26 UTC  
> Url: https://github.com/boostorg/mysql/pull/376  

Add support for ClickHouse MySQL  
  
Closes #346   
  
TODO:  
- [ ] Add `sha256_[password|hash]`, `bcrypt_[password|hash]` auth plugin  
- [ ] Find workaround for prepared statements (may not be supported by ClickHouse)  
- [ ] Extend col_def parsing  
- [ ] Integration tests  
- [ ] etc

---

## Review 1 [Commented]

> Username: anarthal  
> Created_at: 2024-10-30 17:55:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/376#pullrequestreview-2405810434  

📁 include/boost/mysql/impl/internal/protocol/deserialization.hpp

```diff
 294 |     {
 293 |-         err = pack.info.deserialize(ctx);
 295 |+         if (flavor == db_flavor::clickhouse) {
```

> Username: anarthal  
> Created_at: 2024-10-30 17:55:14 UTC  
> Url: https://github.com/boostorg/mysql/pull/376#discussion_r1823134656  

I think you can avoid the variant altogether - just create and deserialize the string_lenenc or string_eof depending on the case. Since this lib supports back to cpp11, unfortunately you can't use generic lambdas, so I think this is easier.


---
