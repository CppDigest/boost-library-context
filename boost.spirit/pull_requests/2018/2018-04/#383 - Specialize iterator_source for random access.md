# #383 Specialize iterator_source for random access [Merged]

> Username: sehe  
> Created at: 2018-04-07 17:23:11 UTC  
> Updated at: 2018-04-08 22:25:15 UTC  
> Merged at: 2018-04-08 22:24:40 UTC  
> Closed at: 2018-04-08 22:24:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/383  

Optimizing this case allows the compiler to achieve a large speedup  
especially in the case where the underlying range is contiguous in  
memory.  
  
The effective result is for the performance to be equivalent to directly  
using `boost::iostreams::array_source<char_type>` if the source range so  
allows.  
  
This patch arose from benchmarking several varying approaches to parsing  
posix timestamps here:  
  
 - https://stackoverflow.com/questions/49693376/why-does-using-a-stream-in-boost-spirit-penalize-performance-so-much  
 - https://stackoverflow.com/questions/49671674/use-a-trait-for-parsing-a-date-in-boostspirit  
  
I'll refer to that location for some benchmark results.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-04-07 17:36:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379486384  

Have you tried to replace the loop with `std::copy`?  
IIRC `std::copy` must work on all iterator types and should be optimized for random iterators.

---

## Comment 2

> Username: sehe  
> Created_at: 2018-04-07 18:19:40 UTC  
> Updated_at: 2018-04-07 18:29:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379489103  

@Kojoley std::copy doesn't have the same semantics, obviously, which is exactly what makes the iterator category relevant, and why there's this PR.  
  
Note also that my specialization **does** use `std::copy`, and how.

---

## Comment 3

> Username: sehe  
> Created_at: 2018-04-07 18:28:13 UTC  
> Updated_at: 2018-04-07 18:39:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379489611  

Added more benchmarks:  
-----  
  
Without the specialization we can observe these timings: **[Interactive Plot.ly](https://plot.ly/~sehe/7/)**  
  
(100 samples, confidence interval 0.95)  
  
benchmark | mean | std dev  
---- | ---- | ----  
std::string + seek     |   31.9222 ms | 228.862  μs  
std::string + ignoring |   16.1855 ms | 257.903  μs  
stream + seek     | 1075.46   ms |  22.23   ms  
stream + ignoring | 1064.41   ms |  26.7218 ms  
                                                
[![enter image description here][1]][1]         
                                                
With the specialization we can observe these timings: **[Interactive Plot.ly](https://plot.ly/~sehe/9/)**  
                                                
benchmark | mean | std dev  
---- | ---- | ----  
std::string + seek |  31.8703 ms | 529.196  μs  
std::string + ignoring |  15.913  ms | 848.514  μs  
stream + seek | 436.263  ms |  19.4035 ms  
stream + ignoring | 419.539  ms |  20.0511 ms  
  
[![enter image description here][2]][2]  
  
> Nonius Benchmark code over on https://stackoverflow.com/questions/49693376/why-does-using-a-stream-in-boost-spirit-penalize-performance-so-much/49710367#49710367  
  
  [1]: https://i.stack.imgur.com/Bu7s6.png  
  [2]: https://i.stack.imgur.com/QJGgo.png

---

## Comment 4

> Username: djowel  
> Created_at: 2018-04-07 23:06:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379505503  

Cool! Those are nice numbers!

---

## Comment 5

> Username: djowel  
> Created_at: 2018-04-07 23:09:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379505650  

I'll give this a go, unless there are objections. But what's up with that "Some checks were not successful" (AppVeyor build failed) error? Do you have an idea, @Kojoley ?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-04-07 23:16:34 UTC  
> Updated_at: 2018-04-07 23:17:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379505969  

```  
.\boost/spirit/home/qi/stream/detail/iterator_source.hpp(114) : error C2780: 'const _Ty &std::min(const _Ty &,const _Ty &,_Pr)' : expects 3 arguments - 2 provided  
        C:\Program Files (x86)\Microsoft Visual Studio 11.0\VC\INCLUDE\xutility(2056) : see declaration of 'std::min'  
```  
  
Despite of the error message it maybe a type mismatch. I think `n` should not be used without casting to `iterator_traits<Iterator>::difference_type`.

---

## Comment 7

> Username: sehe  
> Created_at: 2018-04-08 11:38:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379543425  

@Kojoley yup I agree. Amended the commit. Also remembered to use [BOOST_PREVENT_MACRO_SUBSTITUTION](https://www.boost.org/doc/libs/master/libs/config/doc/html/boost_config/boost_macro_reference.html) this time.

---

## Comment 8

> Username: djowel  
> Created_at: 2018-04-08 20:37:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379580588  

LGTM

---

## Comment 9

> Username: djowel  
> Created_at: 2018-04-08 22:25:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/383#issuecomment-379587776  

Thanks, @sehe !

---
