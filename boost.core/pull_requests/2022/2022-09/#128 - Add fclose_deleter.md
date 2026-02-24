# #128 Add fclose_deleter [Merged]

> Username: Lastique  
> Created at: 2022-09-20 23:20:56 UTC  
> Updated at: 2022-09-21 14:57:45 UTC  
> Merged at: 2022-09-21 13:59:11 UTC  
> Closed at: 2022-09-21 13:59:11 UTC  
> Url: https://github.com/boostorg/core/pull/128  

`fclose_deleter` can be used as a deleter function object for `std::FILE` pointers returned by `std::fopen`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-09-21 14:08:52 UTC  
> Url: https://github.com/boostorg/core/pull/128#issuecomment-1253767305  

I'm not quite sure this belongs here, even though we do have `null_deleter`. Which probably doesn't belong here either.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-09-21 14:16:29 UTC  
> Url: https://github.com/boostorg/core/pull/128#issuecomment-1253777676  

Should I revert?

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-09-21 14:27:15 UTC  
> Url: https://github.com/boostorg/core/pull/128#issuecomment-1253792425  

Utility seems a better place for `fclose_deleter`, but having `null_deleter` here and `fclose_deleter` there wouldn't be particularly consistent. So maybe not.  
  
You have to check `p` for `NULL` though. `unique_ptr` skips the deleter on `NULL`, but `shared_ptr` does not. And you should probably have a test where `fopen` returns `NULL`. Maybe test with `boost::shared_ptr` too.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-09-21 14:57:45 UTC  
> Url: https://github.com/boostorg/core/pull/128#issuecomment-1253833946  

> You have to check `p` for `NULL` though. `unique_ptr` skips the deleter on `NULL`, but `shared_ptr` does not.  
  
Hmm, I thought `shared_ptr` also didn't call the deleter on `NULL`. Will fix, thanks.

---
