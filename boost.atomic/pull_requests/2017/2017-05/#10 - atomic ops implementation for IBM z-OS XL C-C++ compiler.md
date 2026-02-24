# #10 atomic ops implementation for IBM z/OS XL C/C++ compiler [Closed]

> Username: pgroke-dt  
> Created at: 2017-05-05 17:31:18 UTC  
> Updated at: 2020-04-29 16:13:16 UTC  
> Closed at: 2020-04-29 16:13:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/10  

This is my implementation of atomic ops (and caps of course) for the IBM z/OS XL C/C++ compiler.  
  
It uses the compilers CAS intrinsics for exchange, compare_exchange and stores with ordering stronger than release. For loads/stores with no more than acquire/release it simply uses volatile loads/stores with appropriate compiler reordering barriers. (The z/OS XL C/C++ doesn't have any other suitable intrinsics, or at least I couldn't find any.)  
The z/Arch CPUs, to my best knowledge, use total store order though, so this should be sufficient.  
  
Requesting reviews :)  
  
ps: I've written this 2~3 month ago and while reviewing it now I realize that I only put in the Dynatrace (C). While not a direct rip-off of other implementations, the code is still significantly influenced-by/inspired-by/based-on other implementations, especially the Windows/MSVC ones. However, I can't tell anymore which idea/construct I took from which file. So... should I add additional (C) lines? If so, which ones? I'd personally say yes and am tempted to just add the 3 lines found in most of the Windows/MSVC implementations (see e.g. ops_windows.hpp). Would that make sense/be OK?

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-05-08 12:21:42 UTC  
> Updated_at: 2017-05-08 12:23:09 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-299851963  

1. Could you change the implementation to not implement ops in macros, specifically `BOOST_ATOMIC_DETAIL_XLCPP_ZOS_DEFINE_OPS_CAS`? This will make it difficult to debug the code. You can use conditional compilation when needed.  
  
2. Are you sure that `may_alias` attribute applies to typedefs with your compiler? Some compilers may only apply the attribute when a strong type is defined, such as a struct.  
  
3. Is there any open documentation on the hardware architecture? I'm specifically interested in memory model and ISA description so that we can be sure about memory ordering.  
  
4. Aren't any modifications to tests needed? Are the tests passing?  
  
PS: Regarding copyrights, do according to your best judgment. As far as I'm concerned, they are fine as long as they include you, the creator of the file.

---

## Comment 2

> Username: pgroke-dt  
> Created_at: 2017-05-08 13:44:20 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-299870644  

1) OK, I'll change that. It will produce some duplication, but I understand your concern. Debugging should probably take precedence over avoiding some lines of duplicated code.  
  
2) The compiler manual explicitly lists typedefs in the may_alias description and even contains an example that uses a typedef. See http://publibz.boulder.ibm.com/epubs/pdf/cbc1l210.pdf - PDF page 110 (document page 94).  
  
3) The only thing I could find are papers by Paul E. McKenney:  
https://www.ibm.com/support/knowledgecenter/linuxonibm/liaaw/ordering.2006.03.13a.pdf  
http://www.rdrop.com/users/paulmck/scalability/paper/ordering.2007.09.19a.pdf  
http://www.rdrop.com/users/paulmck/scalability/paper/whymb.2010.06.07c.pdf  
In them he lists zSeries as only doing "stores reordered after loads".  
The only one of his references that look relevant to me is the "Principles of Operation" manual:  
http://publibz.boulder.ibm.com/epubs/pdf/dz9zr003.pdf  
The section "Sequence of Storage References" (page 276 or document page "5-88") seems to be about visibility & ordering of memory accesses. But that document is quite a beast and full of unfamiliar (at least to me) technical terms. To be honest, I was simply relying on McKenney here :)  
  
4) I didn't run the Boost unit tests :) Mainly because I couldn't get them to compile on our z box with Boost's unit test build system. We have some unit tests of our own though, which my implementation passes, but of course it would be better to run all tests from the Boost.Atomic test suite. Is there any documentation on how to get Boost's unit test build system to work with compilers that aren't directly supported? Otherwise I'd have to port all Boost tests to our code base. Which is of course something I could do, but I'd want to try to run all Boost unit tests on z anyway (at least for the header only libraries).  
  
Ad copyright: My company told me to put (C) Dynatrace in so that's what I did. Is that OK or should I replace it with (C) Paul Groke for Dynatrace or something similar?

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-05-08 15:44:05 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-299905405  

1. You may be able to reduce code duplication if you wrap intrinsic calls into `BOOST_FORCEINLINE` functions, which can be conditionally defined. The ops template then may be able to use these functions and be defined only once. In other words, try to isolate the least piece of code that requires conditional compilation and leave the rest defined only once.  
  
2. Ok, good to know.  
  
3. I'll take a look at these documents later.  
  
4. You don't have to port all Boost tests to your platform, but you'll probably have to port libraries that are used by Boost.Atomic tests. To build and run Boost.Atomic tests you can just go into `libs/atomic/test` and run `b2` with any additional arguments like `toolset`, `release`, etc. This will also build any dependencies of the tests, including Boost.Atomic itself. Making sure the tests pass is essential because Boost.Atomic users will likely use its tests to see if the library supports their platform.  
  
Re copyrights: If you're doing this work as part of your employment for Dynatrace then just add whatever copyright your employer told you to (i.e. (c) Dynatrace is fine). In this case, "you, the creator of the file" is Dynatrace.

---

## Comment 4

> Username: pgroke-dt  
> Created_at: 2017-05-08 16:29:14 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-299917999  

Ad 4: I already tried that. But I couldn't get bootstrap.sh to complete without errors. Is there any documentation about how to get b2 working with an unsupported compiler?

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-05-08 21:38:00 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-299998423  

> Is there any documentation about how to get b2 working with an  
unsupported compiler?  
  
I don't know of any. You can try asking on the ML.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-05-30 10:39:40 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-304840431  

Any progress on this? Note that develop got updated, including some changes that add support for type aliasing, so you might want to rebase the PR.

---

## Comment 7

> Username: pgroke-dt  
> Created_at: 2017-05-30 11:02:29 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-304845003  

Hi. Thanks for the hint. Is there a config macro for "may alias" now?  
And no, no progress :(  
I'm swamped with work... I guess I just have to *take* the time to do this sooner or later. The changes you requested are no big deal, but getting the test framework to compile & run on our z box ...

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-05-30 13:52:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-304884881  

> Is there a config macro for "may alias" now?  
  
There is `BOOST_ATOMIC_DETAIL_MAY_ALIAS` and `BOOST_ATOMIC_DETAIL_STORAGE_TYPE_MAY_ALIAS`, see https://github.com/boostorg/atomic/blob/develop/include/boost/atomic/detail/config.hpp#L83. You may want to enable those macros for your compiler.

---

## Comment 9

> Username: pgroke-dt  
> Created_at: 2017-06-06 08:13:26 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-306414587  

OK. After hours of printf debugging I got Boost.Build running with two changes (and a mostly guessed compiler config file). Unfortunately the Boost.Atomic tests don't build however. One problem is that Boost.Thread doesn't build, and the Boost.Atomic tests use Boost.Thread -- which I think they shouldn't (I remember seeing "minimal threading" support code in the Boost.Test framework). I've also tried the Boost.SmartPtr tests, and - with two small modifications to the library - they build & pass successfully on z/OS.  
  
But I guess now the first order of business would be submitting the necessary Boost.Build changes, right?

---

## Comment 10

> Username: Lastique  
> Created_at: 2017-06-06 08:46:36 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-306422155  

Boost.Thread dependency is needed for thread synchronization primitives, especially on Windows, where there isn't an equivalent of pthreads. I'm not aware of Boost.Test services wrt threading.  
  
If Boost.Build modifications are necessary then yes, it looks like the first thing to tackle.

---

## Comment 11

> Username: pgroke-dt  
> Created_at: 2017-06-06 09:28:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-306431963  

OK, my mistake. The headers that the Boost.SmartPtr tests use are  
#include <boost/detail/lightweight_thread.hpp>  
#include <boost/detail/lightweight_mutex.hpp>  
They seem to be part of Boost.SmartPtr. Hm...  
IDK how much effort porting Boost.Thread would be... I'd rather re-use the lightweight_* versions though, 'cause I know they work on z/OS :)

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-06-06 09:46:12 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-306436155  

There is no `lightweight_condition_variable` and `lightweight_barrier`. Tools in `lightweight_thread.hpp` are quite different from `boost::thread`.  
  
All in all I'm not against removing the dependency on Boost.Thread, but it looks like a lot of work, specifically in the case of `condition_variable` on Windows. There are some of the needed components in Boost.Sync and I was planning to switch to it when finished, but that work had stalled for the lack of time on my part.

---

## Comment 13

> Username: pgroke-dt  
> Created_at: 2017-06-06 09:51:24 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-306437367  

Well... I guess we can table this until there's Boost.Build support for z. Then I can continue with my patch for Boost.SmartPtr. And if and when that is done, we can still think about what to do with my Boost.Atomic patch.

---

## Comment 14

> Username: Lastique  
> Created_at: 2020-04-29 16:01:51 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-621306465  

Is this still relevant? Any progress?

---

## Comment 15

> Username: pgroke-dt  
> Created_at: 2020-04-29 16:13:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/10#issuecomment-621312904  

No, it's no longer relevant. Sorry. I'll close it.

---
