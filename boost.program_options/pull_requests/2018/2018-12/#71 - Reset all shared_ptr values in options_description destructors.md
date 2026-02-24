# #71 Reset all shared_ptr values in options_description destructors [Closed]

> Username: nomis  
> Created at: 2018-12-22 17:37:26 UTC  
> Updated at: 2019-06-02 06:50:01 UTC  
> Closed at: 2019-06-02 06:50:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/71  

Reset all the shared_ptr that were constructed within the library  
from within so that a mismatch with the header implementation of  
shared_ptr does not result in code for the wrong implementation  
being executed in the shared_ptr destructor.  
  
Fixes #70.

---

## Comment 1

> Username: vprus  
> Created_at: 2018-12-22 17:41:57 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449586785  

Thanks for the PR!  
  
Just to clarify, we have shared_ptr, and it's primary benefit is that it frees the memory automatically. This patch appears to add destructors that explicitly clear those shared_ptr instances, so we're effectively back to manual memory management? Am I missing something?

---

## Comment 2

> Username: nomis  
> Created_at: 2018-12-22 17:44:40 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449586934  

See issue #70; if the implementation of `boost::shared_ptr` is different between the compiled library and the header version used to compile an application, these pointers can be destroyed with the wrong implementation causing a memory leak or other undefined behaviour.  
  
(It would be better if `std::shared_ptr` was used instead.)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-12-22 18:14:34 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449588755  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=h1) Report  
> Merging [#71](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/d95d31684832075fda04c9fc916f8ec875552763?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `33.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/71/graphs/tree.svg?width=650&token=UMdM0EgHK3&height=150&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #71      +/-   ##  
===========================================  
+ Coverage    49.89%   49.92%   +0.03%       
===========================================  
  Files           23       23                
  Lines         1385     1388       +3       
  Branches       707      709       +2       
===========================================  
+ Hits           691      693       +2       
- Misses         110      111       +1       
  Partials       584      584  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/program\_options/options\_description.hpp](https://codecov.io/gh/boostorg/program_options/pull/71/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvb3B0aW9uc19kZXNjcmlwdGlvbi5ocHA=) | `0% <ø> (ø)` | :arrow_up: |  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/71/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `48.47% <33.33%> (-0.18%)` | :arrow_down: |  
| [...de/boost/program\_options/detail/value\_semantic.hpp](https://codecov.io/gh/boostorg/program_options/pull/71/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3ZhbHVlX3NlbWFudGljLmhwcA==) | `43.75% <0%> (+1.56%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=footer). Last update [d95d316...0113647](https://codecov.io/gh/boostorg/program_options/pull/71?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vprus  
> Created_at: 2018-12-22 19:47:20 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449593780  

Per the discussion in #70, mixing single- and multi- threaded versions of program_options is not supported. While this patch fixes some issues on some platform, it does not change the fact that you'll have two different implementations of shared_ptr in your program, which is ODR violation and can explode in other ways; we can't really claim that this use-case is now supported.  And if Peter changes shared_ptr for next release, this patch will become superfluous. It looks we better wait for Peter.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-12-22 21:05:10 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449597557  

> (It would be better if std::shared_ptr was used instead.)  
  
Why are you defining BOOST_DISABLE_THREADS?

---

## Comment 6

> Username: nomis  
> Created_at: 2018-12-23 00:45:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449606610  

> Why are you defining BOOST_DISABLE_THREADS?  
  
Because my application runs in a single thread and I didn't want to have to link to pthreads. I actually only need `BOOST_ASIO_DISABLE_THREADS`.

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-12-23 02:07:27 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-449609362  

You can also keep BOOST_DISABLE_THREADS but override it for shared_ptr with BOOST_SP_ENABLE_THREADS.  
  
In any event, thanks for the report, shared_ptr was actually supposed to make an effort to maintain layout compatibility in this case, even though it's technically an ODR violation.

---

## Comment 8

> Username: vprus  
> Created_at: 2019-06-01 19:22:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-497971079  

Is there anything left here? It's ODR violation from program_options view and IiUC will/is worked around on shared_ptr side?

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-06-01 22:28:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-497982248  

I believe this was fixed by https://github.com/boostorg/smart_ptr/commit/2932ca420340d9be59380bf7d30e92ae9dcea606.

---

## Comment 10

> Username: vprus  
> Created_at: 2019-06-02 06:50:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/71#issuecomment-498004699  

Ok, great.

---
