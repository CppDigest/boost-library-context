# #31 Unbreak build on FreeBSD after e941cb580649 [Merged]

> Username: jbeich  
> Created at: 2018-04-10 17:25:41 UTC  
> Updated at: 2018-07-06 15:40:58 UTC  
> Merged at: 2018-04-10 19:57:56 UTC  
> Closed at: 2018-04-10 19:57:56 UTC  
> Url: https://github.com/boostorg/wave/pull/31  

Wrong `T_DIVIDE` is bootlegged. Let's undefine it similar to https://github.com/boostorg/wave/blob/6ca4f8229d1128392b2b27c829b12af5b5f3387c/include/boost/wave/token_ids.hpp#L30-L32  
```  
In file included from libs/wave/src/instantiate_re2c_lexer.cpp:29:  
In file included from ./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:25:  
In file included from ./boost/spirit/include/classic_core.hpp:11:  
In file included from ./boost/spirit/home/classic/core.hpp:42:  
In file included from ./boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from ./boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from ./boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:14:  
In file included from ./boost/shared_ptr.hpp:17:  
In file included from ./boost/smart_ptr/shared_ptr.hpp:36:  
In file included from ./boost/smart_ptr/detail/spinlock_pool.hpp:25:  
In file included from ./boost/smart_ptr/detail/spinlock.hpp:47:  
In file included from ./boost/smart_ptr/detail/spinlock_std_atomic.hpp:18:  
In file included from ./boost/smart_ptr/detail/yield_k.hpp:28:  
In file included from ./boost/predef.h:17:  
In file included from ./boost/predef/os.h:18:  
In file included from ./boost/predef/os/bsd.h:52:  
In file included from ./boost/predef/os/bsd/bsdi.h:11:  
In file included from ./boost/predef/os/bsd.h:95:  
In file included from ./boost/predef/os/bsd/dragonfly.h:11:  
In file included from ./boost/predef/os/bsd.h:96:  
In file included from ./boost/predef/os/bsd/free.h:37:  
In file included from /usr/include/sys/param.h:134:  
In file included from /usr/include/sys/signal.h:46:  
In file included from /usr/include/machine/signal.h:6:  
In file included from /usr/include/x86/signal.h:45:  
In file included from /usr/include/machine/trap.h:6:  
/usr/include/x86/trap.h:52:2: error: wrong T_DIVIDE inherited, aborting  
```

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2018-04-10 19:57:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/31#pullrequestreview-110988323  

LGTM, thanks!

---

## Comment 2

> Username: jbeich  
> Created_at: 2018-07-06 15:40:58 UTC  
> Url: https://github.com/boostorg/wave/pull/31#issuecomment-403070713  

`master` branch hasn't been updated for a while. Can you cherry-pick the fix just for Boost 1.68 then?

---
