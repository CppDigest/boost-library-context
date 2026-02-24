# #40 Make std lib detection a bit more lightweight [Closed]

> Username: igaztanaga  
> Created at: 2014-09-26 09:05:42 UTC  
> Updated at: 2015-04-23 10:10:09 UTC  
> Closed at: 2014-10-02 17:53:18 UTC  
> Url: https://github.com/boostorg/config/pull/40  

...on environments where cstddef is enough to detect BOOST_STDLIB_CONFIG. Backup to `<utility>` (which has become heavier in C++11, as it needs traits for move_if_noexcept..) if std lib has not been detected.  
  
The patch splits the detection in an auxiliary header and includes it twice in case cstddef is not enough.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-09-26 10:10:09 UTC  
> Url: https://github.com/boostorg/config/pull/40#issuecomment-56943221  

My only concern about this is that we might not correctly detect replacement libraries - for example STLPort or libc++ which are sitting on top of something else - by which I mean if including <cstddef> doesn't detect STLPort (for example) but does detect the underlying library then we go badly wrong.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2014-09-26 22:52:41 UTC  
> Url: https://github.com/boostorg/config/pull/40#issuecomment-57030632  

Currently STLport is detected only including cstddef so the patch does not change this detection. libc++'s <cstddef> initial SVN import:  
  
http://llvm.org/viewvc/llvm-project/libcxx/trunk/include/cstddef?revision=103490&view=markup  
  
shows that <__config> is included and it should be detected by the patch.  
  
I can't speak for other STLs.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-10-02 17:53:18 UTC  
> Url: https://github.com/boostorg/config/pull/40#issuecomment-57671636  

OK, I applied a slightly different fix https://github.com/boostorg/config/commit/d978d311177954b0a65bac78e113732b45170c21

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2014-10-03 15:42:50 UTC  
> Url: https://github.com/boostorg/config/pull/40#issuecomment-57813670  

Great. Thanks John!

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2015-04-23 10:10:09 UTC  
> Url: https://github.com/boostorg/config/pull/40#issuecomment-95523701  

Update, issue: https://github.com/boostorg/config/pull/58 reintroduces a dependency on <ultility> and we're trying to figure out what to do about it :(

---
