# #367 Version 42 [Closed]

> Username: vinniefalco  
> Created at: 2017-05-18 15:52:29 UTC  
> Updated at: 2017-05-19 04:21:47 UTC  
> Closed at: 2017-05-19 04:21:28 UTC  
> Url: https://github.com/boostorg/beast/pull/367  

* Fix javadoc typo  
* Add formal review notes  
* Make buffers_view a public interface  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v42

---

## Review 1 [Changes requested]

> Username: nbougalis  
> Created_at: 2017-05-19 01:50:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/367#pullrequestreview-39094075  

Pointed out some small typos. You're all ready for review, Mr. Falco. Godspeed! :wave:

📁 doc/review.qbk

```diff
  31 |+     However, management of listening sockets, io_service threads,
  32 |+     individual connections, and HTTP semantics such as pipelining,
  33 |+     redirection, Expect: 100-continue, cookies, request routing,
```

> Username: nbougalis  
> Created_at: 2017-05-19 01:44:31 UTC  
> Updated_at: 2017-05-19 01:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117389206  

You probably want "and" after "cookies" and also ditch the comma after "request routing"

> Username: vinniefalco  
> Created_at: 2017-05-19 02:37:25 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117393517  

willfix

---

📁 doc/review.qbk

```diff
  36 |+     will establish it as a building block which the greater C++
  37 |+     community at large can leverage to produce higher level
  38 |+     libraries; competing with each other to figure out the best
```

> Username: nbougalis  
> Created_at: 2017-05-19 01:45:13 UTC  
> Updated_at: 2017-05-19 01:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117389265  

The semicolon here is a bit weird. A comma seems better suited.

> Username: vinniefalco  
> Created_at: 2017-05-19 02:38:22 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117393584  

willfix

---

📁 doc/review.qbk

```diff
 112 |+          be only a secondary concern in most Boost libraries."
 113 |+ 
 114 |+     As the library matures it will undergo optimization passes, benchmarks
```

> Username: nbougalis  
> Created_at: 2017-05-19 01:47:36 UTC  
> Updated_at: 2017-05-19 01:50:13 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117389468  

the comma after "passes" should be a semicolon.

> Username: vinniefalco  
> Created_at: 2017-05-19 04:21:32 UTC  
> Url: https://github.com/boostorg/beast/pull/367#discussion_r117401204  

willfix


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-05-19 04:21:28 UTC  
> Url: https://github.com/boostorg/beast/pull/367#issuecomment-302604884  

Merged to _master_

---
