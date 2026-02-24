# #650 Bezier polynomials. [Merged]

> Username: NAThompson  
> Created at: 2021-06-26 03:10:10 UTC  
> Updated at: 2021-07-01 23:31:51 UTC  
> Merged at: 2021-07-01 23:31:51 UTC  
> Closed at: 2021-07-01 23:31:51 UTC  
> Url: https://github.com/boostorg/math/pull/650  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-06-27 04:40:38 UTC  
> Updated_at: 2021-06-27 04:58:52 UTC  
> Url: https://github.com/boostorg/math/pull/650#issuecomment-869099463  

@mborland , @jzmaddock : I need a real programmer to look at this. The evaluation uses [de Casteljau's algorithm](https://en.wikipedia.org/wiki/De_Casteljau%27s_algorithm) but the particular issue is that I have to allocate an entire container on every evaluation of the interpolant.  
  
Is there a slick way around this? Should we ask the user to provide scratch space?  
  
EDIT: Tried requiring the user to provide scratch space; vastly faster:  
  
With scratch space:  
  
```  
BezierPolynomial<double>/2        7.77 ns         7.76 ns     84819698  
BezierPolynomial<double>/3        11.6 ns         11.6 ns     59953579  
BezierPolynomial<double>/4        16.1 ns         16.1 ns     43163516  
BezierPolynomial<double>/5        20.3 ns         20.3 ns     34360381  
BezierPolynomial<double>/6        24.6 ns         24.6 ns     25604448  
BezierPolynomial<double>/7        35.7 ns         35.7 ns     16327254  
BezierPolynomial<double>/8        33.7 ns         33.7 ns     15074868  
BezierPolynomial<double>/9        43.8 ns         43.8 ns     14816195  
BezierPolynomial<double>/10       55.3 ns         55.3 ns     12691276  
BezierPolynomial<double>/11       58.3 ns         58.3 ns     11221905  
BezierPolynomial<double>/12       92.4 ns         92.3 ns     10390227  
BezierPolynomial<double>/13       95.6 ns         95.6 ns      7492882  
BezierPolynomial<double>/14        128 ns          128 ns      6945823  
BezierPolynomial<double>/15        114 ns          114 ns      5547806  
BezierPolynomial<double>/16        128 ns          128 ns      5019900  
BezierPolynomial<double>/17        205 ns          205 ns      3637403  
BezierPolynomial<double>/18        157 ns          157 ns      5079015  
BezierPolynomial<double>/19        176 ns          175 ns      3958649  
BezierPolynomial<double>/20        183 ns          183 ns      3260075  
````  
  
Without scratch space:  
  
```  
BezierPolynomial<double>/2        71.7 ns         71.7 ns  
BezierPolynomial<double>/3        76.7 ns         76.7 ns  
BezierPolynomial<double>/4        81.7 ns         81.6 ns  
BezierPolynomial<double>/5        89.1 ns         89.0 ns  
BezierPolynomial<double>/6        91.8 ns         91.7 ns  
BezierPolynomial<double>/7        96.0 ns         95.9 ns  
BezierPolynomial<double>/8         105 ns          105 ns  
BezierPolynomial<double>/9         113 ns          113 ns  
BezierPolynomial<double>/10        127 ns          127 ns  
BezierPolynomial<double>/11        135 ns          135 ns  
BezierPolynomial<double>/12        194 ns          194 ns  
BezierPolynomial<double>/13        209 ns          209 ns  
BezierPolynomial<double>/14        222 ns          222 ns  
BezierPolynomial<double>/15        231 ns          230 ns  
BezierPolynomial<double>/16        264 ns          263 ns  
BezierPolynomial<double>/17        269 ns          268 ns  
BezierPolynomial<double>/18        298 ns          297 ns  
BezierPolynomial<double>/19        286 ns          286 ns  
```  
  
Not so elegant from the point of view of the user but effective in terms of speed.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-06-27 10:55:01 UTC  
> Url: https://github.com/boostorg/math/pull/650#issuecomment-869142152  

The only suggestions I have are:  
  
```  
static Container& get_storage()  
{  
   static threadlocal Container the_storage;  
   return the_storage;  
}  
```  
  
Then each thread has it's own storage, and if there are 50000 instances of the class, they all share the same storage.  
  
The other possibility, if it's a common use case for the number of points to be small, is to use a local array as storage in that case.  That way there's no allocation at all in the common case.... if it is indeed common ;)

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2021-06-27 14:46:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/650#pullrequestreview-693424921  

📁 include/boost/math/interpolators/detail/bezier_polynomial_detail.hpp

```diff
  17 |+     static RandomAccessContainer& get_storage()
  18 |+     {
  19 |+         static thread_local RandomAccessContainer the_storage;
```

> Username: mborland  
> Created_at: 2021-06-27 14:46:39 UTC  
> Updated_at: 2021-06-27 14:46:52 UTC  
> Url: https://github.com/boostorg/math/pull/650#discussion_r659332703  

This may run into issues with Mingw since it only supports using `thread_local` with trivial types. I think the following should work:  
  
```  
static  
#ifndef BOOST_MATH_NO_THREAD_LOCAL_WITH_NON_TRIVIAL_TYPES  
    BOOST_MATH_THREAD_LOCAL  
#endif  
RandomAccessContainer the_storage;  
```  
  
@jzmaddock feel free to correct me if I am wrong.

> Username: NAThompson  
> Created_at: 2021-06-27 14:48:27 UTC  
> Updated_at: 2021-06-27 14:48:28 UTC  
> Url: https://github.com/boostorg/math/pull/650#discussion_r659332933  

@mborland : I've restricted to C++-17; will that not help?

> Username: mborland  
> Created_at: 2021-06-27 14:58:04 UTC  
> Url: https://github.com/boostorg/math/pull/650#discussion_r659334144  

It probably will; I forget which versions of GCC it impacted but they are older. If the CI is clean I wouldn't worry about it.

> Username: jzmaddock  
> Created_at: 2021-06-27 15:31:07 UTC  
> Url: https://github.com/boostorg/math/pull/650#discussion_r659338499  

I'm afraid I've had issues up to and including gcc-10 with this.  Sometimes you hit it and sometimes you don't :(  
  
There are also those platforms which have zero thread support (no thread_local, <mutex> etc).

> Username: jzmaddock  
> Created_at: 2021-06-27 15:31:54 UTC  
> Url: https://github.com/boostorg/math/pull/650#discussion_r659338603  

For example: https://github.com/boostorg/math/issues/621


---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-06-27 16:11:44 UTC  
> Url: https://github.com/boostorg/math/pull/650#issuecomment-869187924  

@pabristow : Good idea; I did kinda decide it was a paranoid assert and removed it . . .

---

## Comment 5

> Username: NAThompson  
> Created_at: 2021-07-01 16:12:02 UTC  
> Updated_at: 2021-07-01 20:51:50 UTC  
> Url: https://github.com/boostorg/math/pull/650#issuecomment-872374397  

Ok, now it's a time for compromises, because as predicted, the thread_local storage doesn't work on mingw.  
  
A couple options:  
  
- Just disable it on MingW; it does give a compiler warning so maybe that's ok?  
- Use the Horner scheme discussed in "Bezier and B-Spline Techniques" rather than the de Casteljau recurrence. Unfortunately the polynomial coefficients then are products of geometric coordinates with binomial coefficients, so both instability and overflow are very possible.  
  
Any ideas?

---

## Comment 6

> Username: mborland  
> Created_at: 2021-07-01 17:09:36 UTC  
> Url: https://github.com/boostorg/math/pull/650#issuecomment-872413718  

> Ok, now it's a time for compromises, because as predicted, the thread_local storage doesn't work on mingw.  
>   
> A couple options:  
>   
>     * Just disable it on MingW; it does give a compiler warning so maybe that's ok?  
>   
>     * Use the Horner scheme discussed in "Bezier and B-Spline Techniques" rather than the de Casteljau recurrence. Unfortunately the polynomial coefficients then are products of geometric coordinates with binomial coefficients, so both instability and overflow are easy and possible.  
>   
>   
> Any ideas?  
  
My recommendation would be to disable the use of `thread_local` storage for both MingW and environments without threading (see #621). `<boost/math/tools/config.hpp>` provides a definition of `BOOST_DISABLE_THREADS` so this would continue to work in standalone mode.

---
