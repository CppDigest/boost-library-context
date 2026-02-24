# #34 Don't export all of gregorian::month, only select functions [Closed]

> Username: res2k  
> Created at: 2016-11-04 09:46:03 UTC  
> Updated at: 2018-11-12 13:24:50 UTC  
> Closed at: 2018-11-12 13:24:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/34  

This change is useful on MSVC: With the whole class exported MSVC will import even the simple ctors from the DLL. By restricting it to the few functions really implemented separately most uses of greg_month can be inlined (especially the simple ones).

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-11-16 23:35:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261107796  

I do not understand this at all. What you are doing is going to break user's code by not exporting functionality which others may be using, and then exporting implementation code instead of the previous functionality. Furthermore the functionality is used by other compilers and not just VC++. This PR does not seem as if it ever can be correct.

---

## Comment 2

> Username: res2k  
> Created_at: 2016-11-17 13:16:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261243717  

How does it break user's code? All method definitions are accounted for; they're either defined inline, or externally but declared BOOST_DATE_TIME_DECL.  
I don't see how the user-accessible functionality changed, all the methods still return what they used to; the strings are the same, as is the month_map.  
As for other compilers: true, the change was made with chiefly VC in mind, but I didn't find any issues with gcc either. Other compilers I don't use regularly.

---

## Comment 3

> Username: res2k  
> Created_at: 2016-11-17 13:30:40 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261246587  

Some additional explanation:  
If you tag a _class_ as DLL exported in VC, it will try to import every method, even inline-defined ones - e.g. constructors.  
The practical effect for greg_month in date_time is that, if you say you want the dynamic version of the library, you _have_ to link against the DLL if you use greg_month directly or indirectly, even though the code is actually only trivial and uses the constructor and maybe simple accessors. Further, other Boost code uses date_time, so you may end up with that requirement unwittingly (e.g. boost_thread can introduce such a date_time dependency).  
Now, with removing the dllexport from the _class_ you're preventing the wholesale importing of methods, the inline defined constructors are actually inlined. Of course, since it's not desireable to inline define all of the class, the externally defined methods are tagged as dllexport, so the dynamic version should work as it used to.  
True, the reasoning is pretty VC-centric, but I don't think other compilers suffer from this change. I guess either they used to inline appropriately anyway, or if they behaved similarly, the externally defined methods are still tagged for export.

---

## Comment 4

> Username: eldiener  
> Created_at: 2016-11-17 15:12:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261272478  

Whether inlined code allows exporting/importing of functionality in a DLL automatically is not something that is defined by the C++ standard, I could possibly accept your PR if it were changed to be strictly for VC++, but then I would need to test it on versions of VC++ from VS2005 on up. I am not going to accept this PR as it relates to all other compilers. You can use '#if defined(BOOST_MSVC)' after including '<boost/config.hpp>' to test for VC++ appropriately.

---

## Comment 5

> Username: res2k  
> Created_at: 2016-11-17 16:20:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261292674  

I don't understand your concerns because what I see as the result is just a class calling functions which are externally defined.  
  
Same principle in somewhat different approaches:  
https://gist.github.com/res2k/e7b257851d1838b8af3b39b6bfb2861c  
  
That the change was motivated by a VC quirk is, in a way, a red herring.

---

## Comment 6

> Username: eldiener  
> Created_at: 2016-11-17 18:46:20 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-261332690  

I do not see the value of what you are doing. I am not going to mess around with exporting/importing terminology for any given compiler with the hopes that it will somehow turn out correct, just because you are looking for some optimization technique for one particular compiler.

---

## Comment 7

> Username: Lastique  
> Created_at: 2016-11-30 16:47:40 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-263926317  

@eldiener The technique proposed in this PR is valid, I used it myself in Boost.Log (e.g. see https://github.com/boostorg/log/blob/develop/include/boost/log/core/core.hpp#L61). The point is to avoid generating inter-dll calls where the called function could otherwise be inlined or constexpr. Also, as a side effect, this can avoid generating compiler-generated constructors and assignment (because if the class is exported, its implicitly defined members are exported as well).  
  
Whether this change will have any effect on other compilers on Windows I can't tell, but the list of exported symbols should at least become more unified across compilers. I can see one potential issue though: by removing `BOOST_DATE_TIME_DECL` from the class definition you're making the class hidden on POSIX systems, if user's code is compiled with hidden visibility (this is related to https://github.com/boostorg/date_time/pull/27). I'm not sure that's ok because that can affect IO facets visibility (code inspection is needed to confirm that). I would suggest marking the class with `BOOST_SYMBOL_VISIBLE`.

---

## Comment 8

> Username: eldiener  
> Created_at: 2016-12-01 13:51:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-264177689  

I am not arguing whether the technique is valid or not. I also assume it works in the environment for which it was originally made. Our problem is that we support a whole host of compilers. I am not talking about just VC++, gcc, and clang. We also support Oracle C++ and Intel C++ and I am sure others for which admittedly we do little testing. So far we have accepted that exporting/importing entire classes using the Boost visibility constructs in Boost config "mostly" works on nearly all compilers Boost supports. Now, for the sake of an optimization, you want to go into the land of exporting/importing individual member functions and just assume that all compilers which Boost supports better work with this idiom. I just don't see making this change and breaking reams of user code with it for compilers in which this does not work as you suspect. I don't mind a one-off situation for particular compilers which can be closely tested but I see this as too chancy for any compiler using date/time functionality as a shared library. Perhaps I am totally wrong but I have run into other situations where using export/import macro functionality seems to hang by a thread, and even a major compiler ( clang targeting mingw-64/gcc on Windows, mingw-64/gcc on Windows itself ) has problems. So I oppose this, but if someone else wants to push this through and take responsibility for it on all compilers and deal with any possible problems which end-users report because of this generalized technique, go on ahead.

---

## Comment 9

> Username: Lastique  
> Created_at: 2016-12-01 14:32:19 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-264187448  

On 12/01/16 16:51, Edward Diener wrote:  
> I am not arguing whether the technique is valid or not. I also assume it  
> works in the environment for which it was originally made. Our problem  
> is that we support a whole host of compilers. I am not talking about  
> just VC++, gcc, and clang. We also support Oracle C++ and Intel C++ and  
> I am sure others for which admittedly we do little testing. So far we  
> have accepted that exporting/importing entire classes using the Boost  
> visibility constructs in Boost config "mostly" works on nearly all  
> compilers Boost supports. Now, for the sake of an optimization, you want  
> to go into the land of exporting/importing individual member functions  
> and just assume that all compilers which Boost supports better work with  
> this idiom.  
  
But it does work. I've been exporting individual class members for ages,   
not only in Boost.Log, and never heard of any problems caused by that. I   
would go as far as exporting individual members should be preferred over   
exporting the whole class for the reasons I outlined before.  
  
> I just don't see making this change and breaking reams of  
> user code with it for compilers in which this does not work as you  
> suspect. I don't mind a one-off situation for particular compilers which  
> can be closely tested but I see this as too chancy for any compiler  
> using date/time functionality as a shared library. Perhaps I am totally  
> wrong but I have run into other situations where using export/import  
> macro functionality seems to hang by a thread, and even a major compiler  
> ( clang targeting mingw-64/gcc on Windows, mingw-64/gcc on Windows  
> itself ) has problems. So I oppose this, but if someone else wants to  
> push this through and take responsibility for it on all compilers and  
> deal with any possible problems which end-users report because of this  
> generalized technique, go on ahead.  
  
No problem. Thanks for your comments.

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-01-18 03:36:51 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-358527870  

Do we really need a link library here at all?

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-01-23 01:54:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-359643965  

We can probably avoid that. I'm not sure what's the reason for linking nowdays.

---

## Comment 12

> Username: jeking3  
> Created_at: 2018-08-19 14:26:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-414131260  

I checked with the original author and there were some string definitions made early on in development of the library that caused it to have a link library.  Apparently this can be resolved with creative use of templates.

---

## Comment 13

> Username: res2k  
> Created_at: 2018-08-29 22:29:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-417127800  

Alternatively, one could remove `BOOST_DATE_TIME_DECL` from the class entirely (not on individual methods as well) and either put `as_long_string_impl()` etc as free functions, decorated with `BOOST_DATE_TIME_DECL`, into some namespace or put them as static methods into a `BOOST_DATE_TIME_DECL`ed class (ie poor man's namespace) - both approaches are so widespread that it's very likely they work anywhere.

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2018-08-30 00:30:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-417151106  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=h1) Report  
> Merging [#34](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/66ee8bf48fbec95ae4dfa59a128b3b3305261455?src=pr&el=desc) will **decrease** coverage by `0.04%`.  
> The diff coverage is `66.66%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/34/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #34      +/-   ##  
===========================================  
- Coverage    51.48%   51.44%   -0.05%       
===========================================  
  Files           83       83                
  Lines         4984     4988       +4       
  Branches      2441     2441                
===========================================  
  Hits          2566     2566                
  Misses         403      403                
- Partials      2015     2019       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_month.hpp](https://codecov.io/gh/boostorg/date_time/pull/34/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfbW9udGguaHBw) | `55% <0%> (-13.75%)` | :arrow_down: |  
| [src/gregorian/greg\_month.cpp](https://codecov.io/gh/boostorg/date_time/pull/34/diff?src=pr&el=tree#diff-c3JjL2dyZWdvcmlhbi9ncmVnX21vbnRoLmNwcA==) | `59.45% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=footer). Last update [66ee8bf...ceb46b9](https://codecov.io/gh/boostorg/date_time/pull/34?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 15

> Username: jeking3  
> Created_at: 2018-08-30 11:34:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-417287650  

I still believe a better solution is to eliminate the link library.

---

## Comment 16

> Username: JeffGarland  
> Created_at: 2018-08-31 01:48:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-417523748  

Just dropping by -- jeking is correct, the link library should simply be removed.  As I think he's mentioned we've discussed and you can see the technique that will work for all compilers back c++98 that will work in the input/output facet code.  That's the better way to go....

---

## Comment 17

> Username: res2k  
> Created_at: 2018-09-03 07:49:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-418030523  

My last comment was intended as a (quite belated) reply towards the skepticism of DLL-exporting inidividual methods of a class; I wanted to point out that there are multiple, functionally equivalent approaches for the change I made.  
  
As for link libraries in general: They _do_ have utility for library users. They can nicely encapsulate and hide the implementation, including any additional "baggage" pulled in by needed headers.  
For example, to get rid of the date_time link library, you'd have to pretty much inline `greg_month.cpp`. As it is now, this pulls in various additional headers, which would have to be parsed in every source file using the greg_month class, thus impacting build performance even if only a fraction of the functionality is used.

---

## Comment 18

> Username: jeking3  
> Created_at: 2018-11-12 13:24:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/34#issuecomment-437878579  

Closing this in favor of #85.

---
