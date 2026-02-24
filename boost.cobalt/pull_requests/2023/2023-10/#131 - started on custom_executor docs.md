# #131 started on custom_executor docs. [Merged]

> Username: klemens-morgenstern  
> Created at: 2023-10-10 08:32:21 UTC  
> Updated at: 2023-10-16 13:51:10 UTC  
> Merged at: 2023-10-16 13:51:06 UTC  
> Closed at: 2023-10-16 13:51:06 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131  



---

## Review 1 [Commented]

> Username: anarthal  
> Created_at: 2023-10-10 10:28:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cobalt/pull/131#pullrequestreview-1667168156  

📁 doc/background/custom_executors.adoc

```diff
  52 |+ ==== Properties of the `asio::any_io_executor`
  53 |+ 
  54 |+ In order to wrap an executor in an `asio::any_io_executor` two properties are required:
```

> Username: anarthal  
> Created_at: 2023-10-10 10:28:32 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1352127184  

I think this is incomplete, looking at the source code:  
  
      execution::context_as_t<execution_context&>,  
      execution::blocking_t::never_t,  
      execution::prefer_only<execution::blocking_t::possibly_t>,  
      execution::prefer_only<execution::outstanding_work_t::tracked_t>,  
      execution::prefer_only<execution::outstanding_work_t::untracked_t>,  
      execution::prefer_only<execution::relationship_t::fork_t>,  
      execution::prefer_only<execution::relationship_t::continuation_t>

> Username: klemens-morgenstern  
> Created_at: 2023-10-10 14:50:38 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1352712097  

The `prefer_only`s are required?


---

## Comment 2

> Username: anarthal  
> Created_at: 2023-10-10 15:42:34 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#issuecomment-1755716682  

No. `prefer_only` makes the property on `any_io_executor` preferable, but not requirable.  
  
This is a guess, but it's likely that the only hard requirement is `blocking.never` and the context one (that's used to retrieve the execution_context pointer).  
  
My theory is that if a property cannot be required, prefer will just return the original executor. But I'd have to test it.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-10-10 23:57:54 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#issuecomment-1756480137  

Tested it, seems to be the case.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-10-11 00:11:17 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#issuecomment-1756489594  

See https://gcc.godbolt.org/z/qEj98M5KT

---

## Review 5 [Commented]

> Username: anarthal  
> Created_at: 2023-10-11 10:04:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cobalt/pull/131#pullrequestreview-1670660491  

📁 doc/background/custom_executors.adoc

```diff
  20 |+ {
  21 |+   template<typename Fn>
  22 |+   void executor(Fn && fn) const;
```

> Username: anarthal  
> Created_at: 2023-10-11 10:00:43 UTC  
> Updated_at: 2023-10-11 10:04:05 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1354606020  

Shouldn't this be `execute`?

> Username: klemens-morgenstern  
> Created_at: 2023-10-11 12:46:22 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1354909324  

yes

---

📁 doc/background/custom_executors.adoc

```diff
 143 |+ 
 144 |+   template<class F>
 145 |+   void execute(F f) const;
```

> Username: anarthal  
> Created_at: 2023-10-11 10:02:49 UTC  
> Updated_at: 2023-10-11 10:04:05 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1354608536  

Shouldn't be `F&&`?

> Username: klemens-morgenstern  
> Created_at: 2023-10-11 12:46:29 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1354909487  

yes

---

📁 doc/background/custom_executors.adoc

```diff
 150 |+ ----
 151 |+ 
 152 |+ NOTE: See example/python.cpp for an implementation using python's `asyncio` event-loop.
```

> Username: anarthal  
> Created_at: 2023-10-11 10:03:07 UTC  
> Updated_at: 2023-10-11 10:04:05 UTC  
> Url: https://github.com/boostorg/cobalt/pull/131#discussion_r1354608955  

A link to the file would be nice


---
