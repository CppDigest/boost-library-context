# #610 - boost 1.78.0 (and many previous versions?) do not support the documented GCC versions.. "This library now requires a C++11 or later compiler" should minimal requirements be updated? [Open]

> Username: gcflymoto  
> Created at: 2022-01-24 16:33:16 UTC  
> Updated at: 2022-02-13 09:06:32 UTC  
> Url: https://github.com/boostorg/boost/issues/610  

Cross-filed with https://github.com/boostorg/multiprecision/issues/420  
  
As documented on the [release notes](https://www.boost.org/users/history/version_1_78_0.html)  
  
"""  
Boost's primary test compilers are:  
  
Linux:  
...  
GCC, C++11: 4.7.3, 4.8.5, 11  
"""  
  
But this does play out in practice.. compiling with GCC4.7.3, GCC4.8.5 with -std=c++ fails. Should the release notes change?  
  
boost/1.78.0/boost/multiprecision/detail/number_base.hpp:36:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_HDR_TYPE_TRAITS being set"  
  
/usr/bin/gcc/4.7.3/bin/g++ -c -std=c++11 -fPIC -DBOOST_PP_VARIADICS  test.cpp  
  
from test.cpp:13:  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:36:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_HDR_TYPE_TRAITS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:48:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_REF_QUALIFIERS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:72:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_REF_QUALIFIERS being set"  
/usr/boost/1.78.0/boost/multiprecision/detail/number_base.hpp:92:0: warning: ignoring #pragma GCC warning [-Wunknown-pragmas]  
In file included from /usr/boost/1.78.0/boost/multiprecision/cpp_int.hpp:14:0,  
                 from /usr/boost/1.78.0/boost/multiprecision/integer.hpp:10,  
                 from test.cpp:13:  
  
The same happens with GCC 4.8.5  ... the lowest GCC that works for me is 5.3.0

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-01-24 19:25:23 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1020461247  

> a non C++11 conforming compiler  
  
There is some discussion here. What are considered interpretatins of a C++11 compiler? Which compilers CI?  
  
I/we could take a try at more details in the docs. In the GCC world, I feel that C++11 is 5.2, 5.3, 5.4 or higher.  
I believe we only test these days in CI at GCC6 and higher.  
  
I don't knoe when an MSVC might be considered C++11 ( I think all the way up to MSVC 2017). And the clang story i also do not know.  
  
What happens if somewone goes for GCC 5.2, which may very wel compile Math/Multiprecision, but might not be in CI.  
  
We should describe, elucidate and narrow these fields in docs in my opinion...  
  
See also [related issue](https://github.com/boostorg/multiprecision/issues/420)  
  
Cc: @mborland and @jzmaddock

---

## Comment 2

> Username: gcflymoto  
> Created at: 2022-01-24 19:30:09 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1020465344  

Yes, it looks like the minimum is 5.2 from my testing to compile boost MP.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-01-25 09:22:01 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1020974083  

I agree the top level release notes are incorrect here - we decided some time ago on the ML that libraries could drop C++03 support, and that means dropping compilers with only pre-C++03 support from the "primary" list in the release notes IMO. @pdimov, @mclow does anything need to change other than the docs?  How do the release managers feel about this?

---

## Comment 4

> Username: mclow  
> Created at: 2022-01-28 19:53:56 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1024587489  

I'll just point out here that Tom Kent runs test bots that test gcc 4.6, 4.7, 4.8, and 4.9 (and others)  
See https://www.boost.org/development/tests/master/developer/summary.html

---

## Comment 5

> Username: gcflymoto  
> Created at: 2022-01-28 20:08:56 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1024599195  

@mclow and multi-precision fails on all of those:  
  
https://www.boost.org/development/tests/master/developer/multiprecision.html

---

## Comment 6

> Username: pdimov  
> Created at: 2022-01-29 04:16:50 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1024829316  

> I don't knoe when an MSVC might be considered C++11 ( I think all the way up to MSVC 2017).  
  
The earliest MSVC version that can be considered reasonably C++11 is MSVC 2013 (it doesn't support constexpr and noexcept). The earliest kind of "fully conforming" C++11 is 2015.  
  
> And the clang story i also do not know.  
  
All versions of Clang we have on CI are C++11, but on Linux it can use the underlying GCC stdlib, and that's only reasonably C++11 starting from 4.8, and "fully conforming" from 4.9 or 5.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-01-29 04:18:03 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1024829487  

More generally, this is exactly why I argued (at length) that the decision to move to C++11 as minimum needs to be done on the Boost project level, rather than at the individual library level, but, here we are.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-01-30 11:11:20 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025120289  

> and multi-precision fails on all of those:  
  
As expected, we no longer support pre-c++11 compilers.  
  
> More generally, this is exactly why I argued (at length) that the decision to move to C++11 as minimum needs to be done on the Boost project level, rather than at the individual library level, but, here we are.  
  
I thought the consensus was that libraries could if they wished (provided suitable notice was given) drop support for pre-C++11 compilers?  If that's the case then anything prior to gcc-5 should be considered "optionally" supported?

---

## Comment 9

> Username: ckormanyos  
> Created at: 2022-01-30 12:50:30 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025136763  

> But this does play out in practice.. compiling with GCC4.7.3, GCC4.8.5 with -std=c++ fails. Should the release notes change?  
  
> boost/1.78.0/boost/multiprecision/detail/number_base.hpp:36:2: error: #error "This library now requires a C++11 or later compiler - this message was generated as a result of BOOST_NO_CXX11_HDR_TYPE_TRAITS being set"  
  
I was going over this thread again. There are quite a few ins-and-outs here.  
  
Then I got a bit sad.   
  
The original quote above caught my attention. When we went C++11 in Multiprecision, we decided to drop support (purposely) for non-C++11 compilers. But we did not exactly specify _which_ compilers retain support. The exact compilers mentioned above actually _do_ have some preliminary version(s) of `<type_traits>`. But these are only activated when `-std=c++11` is specified on the command line.  
  
Then I was wondering about the scope and determination of this post. I think getting us to roll back on C++11 in either Math or Multiprecision would be something we authors of those packages would resist to some extent.  
  
In summary, I hope we did not overlook anything too big. I had thought we were well within the spirit of Boost when going with C++11. In fact, I had believed it was a completely optional on a library-to-library basis to make the choice to require C++11. John has mentioned this above.  
  
As a final _technical_ remark, I think we could more clearly state which compilers do/do-not work with Math/Multiprecision. Going back to 5.2 might be within reach. But I think dropping the C++11 requirments would require some negotiation and might not be well received by all library authors in Math/Multiprecision.

---

## Comment 10

> Username: pdimov  
> Created at: 2022-01-30 15:44:58 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025170822  

I think that we should probably take GCC 4.6 and 4.7 out of our primary test compilers, because most of our primary testing is now done with Github Actions and 4.8 is the minimum there (if one doesn't want to bother with docker images of 16.04.)  
  
I'm not sure that the early Clangs can also reasonably be called primary, because the earliest on GHA is 3.9 now.  
  
Also, our primary compiler list doesn't reflect the relative importance of the targets, because the default -std level for GCC until 6.0 is -std=c++98, and -std=c++14 onwards (until 11, when it changes to c++17). And the default is important because that's how the distros build Boost.  
  
As for Multiprecision itself, it's your choice whether to support GCC 4.8 or not. It's almost C++11, but not entirely. I wouldn't be opposed to declaring GCC 5 the earliest usable C++11 compiler, and updating the primary compiler list to reflect that.  
  
> I thought the consensus was that libraries could if they wished (provided suitable notice was given) drop support for pre-C++11 compilers?  
  
A significant fraction of Boost users view Boost as a single thing and don't really care for our internal consensus (to the extent that it exists.) If Boost effectively requires GCC 5 and C++11 now to be usable, or if it effectively requires GCC 5 in order for its C++11 portions to be usable, this should be reflected in the Boost release notes.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2022-01-31 12:23:47 UTC  
> Updated at: 2022-01-31 12:25:15 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025685623  

It is good to read the advice above.  
  
> As for Multiprecision itself, it's your choice whether to support GCC 4.8 or not. It's almost C++11, but not entirely. I wouldn't be opposed to declaring GCC 5 the earliest usable C++11 compiler, and updating the primary compiler list to reflect that.  
  
From the operational perspective, does it help anything to strive to get Math/Multiprecision back tested and released for GCC 5.2? Or has that already sailed?  
  
We would need to guage the feasibility of this in the library team. Subjectively, I think 5.2 should/could be do-able, as it's a pretty good C++11 compiler. But along those lines, we would need the flag `-std=c++11`.  
  
If it helps and if there is added value, I could look into CI for GCC 5.2 on these two LIBs --- but definitely post-1.79 in spring/summer. I might not get too far, but I could look.  
  
Cc: @jzmaddock your thoughts on recovering GCC 5.2?

---

## Comment 12

> Username: pdimov  
> Created at: 2022-01-31 15:15:00 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025870659  

I'm not sure what's the relevance of GCC 5.2? It's a minor version (a patch release), and GCC 5 is up to 5.5 now I think?  
  
Note that the version scheme of GCC changed with 5 and above; the 2 in 5.2 is the equivalent of 5 in 4.8.5.  
  
E.g. the base (as shipped) version of GCC on Xenial (Ubuntu 16.04 LTS), where it's the system compiler, is 5.3, and the updated version is 5.4, according to https://www.ubuntuupdates.org/package/core/xenial/universe/updates/gcc-5. I don't think there's any point of trying to support 5.2.

---

## Comment 13

> Username: ckormanyos  
> Created at: 2022-01-31 15:24:29 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025887176  

> I'm not sure what's the relevance of GCC 5.2?  
  
I would like to know:  
- Does supporting GCC `5.any` help for the purposes of this thread or not?  
  
I was, however, unaware (or forgot) the major/minor/patch philosophy change. Thx very much for reminding of that Peter (@pdimov).

---

## Comment 14

> Username: pdimov  
> Created at: 2022-01-31 15:33:29 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025902481  

Since the default for GCC 6 is C++14, there's not much point in keeping the library C++11 if you don't support GCC 5.

---

## Comment 15

> Username: ckormanyos  
> Created at: 2022-01-31 15:52:45 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025927760  

> Since the default for GCC 6 is C++14, there's not much point in keeping the library C++11 if you don't support GCC 5.  
  
Of course... That's what was running through  the back of my mind. 5 was the end of the line for 11-only --- if that makes any sense... It's been a while and I had kind of forgotten that.

---

## Comment 16

> Username: ckormanyos  
> Created at: 2022-01-31 15:56:27 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025931459  

Having said all that, does this ticket --- the one here --- benefit from Math/Multiprecision to be made to support GCC 5, or is that entirely optional? Somehow I get the feeling Math and Multiprecision broke a kind of contract, unknowingly, and I need to know if anything can/should be done to improve the situation.  
  
Otherwise, how did we end up in this thread?

---

## Comment 17

> Username: gcflymoto  
> Created at: 2022-01-31 16:33:14 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025971386  

Note as an end-user I am more than fine if some boost library drops support for all GCC versions < GCC6 -std=c++14 and that becomes the minimum version for boost, but that should be clearly reflected in the top-level communication in the boost release notes. That's the first document we went through to determine whether we could use this boost. We relied on that only to be bitten and found out in practice MP was not compatible. As @pdimov mentioned we treat boost as a whole, not individual libraries, and wouldn't expect to have to scour the automated bot's results to determine the minimum version of GCC that is supported.

---

## Comment 18

> Username: pdimov  
> Created at: 2022-01-31 16:44:38 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025982539  

Yes, I think the reason we're in this thread is that the Boost release notes (what it says on the tin) do not correspond to the actual content. It would be better if, ideally, these are aligned, so that when the release notes say that GCC 5.4 is a primary C++11 compiler, and when a library says that it requires C++11, that library can be reasonably expected to work with GCC 5.4 -std=c++11.  
  
The release notes are the domain of the release managers (e.g. @mclow) though, so it's their call what they say.  
  
At this point it might be worth taking this to the developer list.

---

## Comment 19

> Username: ckormanyos  
> Created at: 2022-01-31 16:49:05 UTC  
> Updated at: 2022-01-31 16:49:55 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1025986840  

OK thanks for the patience and details.  
  
It seems like this thread has two related topics:  
  
1. Originally there was/is a call for correct docs if needed (now or future) as Math/Multiprecision stopped supporting GCC 4/5.  
2. The related sub-topic is if we internally at Math/Multiprecision would like to try for regaining GCC 5 compatibility.  
  
> fine if some boost library drops support for all GCC versions < GCC6 -std=c++14 and that becomes the minimum version for boost, but that should be clearly reflected in the top-level communication in the boost release notes.  
  
That's our part. But (in my subjective opinion, and having cleared up that GCC 5 is actually the least C++11 to have) I do not want to be that LIB that causes GCC 5 to be dropped. I do not know what the other Math/Multiprecision authors think and I will ask.  
  
I will take the GCC 5 topic over to the Multiprecision/Math projects now.  
  
> That's the first document we went through to determine whether we could use this boost. We relied on that only to be bitten and found out in practice MP was not compatible.  
  
This seems like the original point. Sorry if I distracted that issue.

---

## Comment 20

> Username: ckormanyos  
> Created at: 2022-02-06 14:10:46 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1030840338  

> the reason we're in this thread is that the Boost release notes (what it says on the tin) do not correspond to the actual content.  
  
Indeed. We will try to ensure the release notes agree with the content for 1.79. Additionally, we have, in fact, brought back GCC 5 support to Multiprecision for 1.79.

---

## Comment 21

> Username: gcflymoto  
> Created at: 2022-02-13 02:01:20 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1037669547  

boost/optional also does not function under GCC4.7.2 -std=cxx11  
  
/p/pkgs/boost/1.70.01/boost/type_traits/is_default_constructible.hpp: In instantiation of 'struct boost::is_default_constructible<std::basic_ostream<char> >':  
/p/pkgs/boost/1.70.01/boost/multiprecision/number.hpp:884:23:   required from here  
/p/pkgs/gcc/4.7.2/.bin/../lib64/gcc/x86_64-suse-linux/4.7.2/../../../../include/c++/4.7.2/ostream:382:7: error: 'std::basic_ostream<_CharT, _Traits>::basic_ostream() [with _CharT = char; _Traits = std::char_traits<char>]' is protected  
In file included from /p/pkgs/boost/1.70.01/boost/type_traits/has_nothrow_constructor.hpp:22:0,  
                 from /p/pkgs/boost/1.70.01/boost/optional/optional.hpp:38,  
                 from /p/pkgs/boost/1.70.01/boost/optional.hpp:15,  
                 from /p/pkgs/uncore_bfm/base/include/vt.h:44,              #include <boost/optional.hpp>  
                 from /p/pkgs/uncore_bfm/kti/bfm/include/CLLUce.h:4,  
                 from CLLUce.cpp:1:  
/p/pkgs/boost/1.70.01/boost/type_traits/is_default_constructible.hpp:35:33: error: within this context  
In file included from /p/pkgs/gcc/4.7.2/.bin/../lib64/gcc/x86_64-suse-linux/4.7.2/../../../../include/c++/4.

---

## Comment 22

> Username: pdimov  
> Created at: 2022-02-13 03:17:14 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1037715649  

I'm not sure Optional has something to do with this error, even if the include chain points to it because it happened to include `boost/type_traits/is_default_constructible.hpp`. The instantiation point is actually Multiprecision, although the Boost version seems to be 1.70 and we don't support old releases (although this may be a typo.)

---

## Comment 23

> Username: gcflymoto  
> Created at: 2022-02-13 03:41:01 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1037729985  

Not a typos, this was an attempt to narrow down the last boost version which functions with GCC472 -std=c++11

---

## Comment 24

> Username: jzmaddock  
> Created at: 2022-02-13 09:06:32 UTC  
> Url: https://github.com/boostorg/boost/issues/610#issuecomment-1037946716  

I dimly remember fixing a clash between optional and multiprecision.... here's one anyway: https://github.com/boostorg/multiprecision/pull/95, I'm fairly sure there was another somewhere - basically both had some under-constrained enable_if's which could cause the compiler to instantiate all kinds of surprising things if they were used together.
