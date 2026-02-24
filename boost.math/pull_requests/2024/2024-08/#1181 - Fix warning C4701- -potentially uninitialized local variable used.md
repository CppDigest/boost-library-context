# #1181 Fix warning C4701: "potentially uninitialized local variable used" [Merged]

> Username: fsb4000  
> Created at: 2024-08-16 02:51:45 UTC  
> Updated at: 2025-04-18 08:49:52 UTC  
> Merged at: 2025-04-18 08:49:52 UTC  
> Closed at: 2025-04-18 08:49:52 UTC  
> Url: https://github.com/boostorg/math/pull/1181  

Hello.  
I tried to use your library (1.86.0)  and I faced with an issue. I didn't have the issue with 1.85.0.  
I use VS 2022 and I get an error.  
```  
boost-math\include\boost\math\special_functions\detail\bessel_ik.hpp(417) : error C2220: the following warning is treated as an error  
boost-math\include\boost\math\special_functions\detail\bessel_ik.hpp(417) : warning C4701: potentially uninitialized local variable 'n' used  
boost-math\include\boost\math\special_functions\detail\bessel_ik.hpp(417) : warning C4701: potentially uninitialized local variable 'u' used  
```  
As you can see we initialize `n` and `u` only in the `else` branch and later we use them in `if (reflect)`. We might not get into this `if` if we haven't been in that `else` but the compiler don't know about it. I tried to fix the issue.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-08-16 05:58:56 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2292868300  

Cool find. I'll try to maybe find/add a test case that hits the lines?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-08-16 06:02:40 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2292872761  

> Cool find. I'll try to maybe find/add a test case that hits the lines?  
  
Wait a sec, ...  
  
According to the [covrage report](https://app.codecov.io/gh/boostorg/math/blob/develop/include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_ik.hpp), the lines _are_ being hit. Is there maybe a better fix? Delay declaration of the variables or `static_cast<void>`?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-08-16 08:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2293047979  

I would need to double check, but I seem to remember fixing this while doing some code-coverage work.  Ah, yes it is, see: https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/detail/bessel_ik.hpp

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-08-16 08:33:36 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2293079309  

I think you can declare n and u at lines 340'and 341? Isn't that the point of the warning?

---

## Comment 5

> Username: fsb4000  
> Created_at: 2024-08-16 09:12:59 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2293141490  

>I think you can declare n and u at lines 340'and 341? Isn't that the point of the warning?  
  
They are used not only in the else but later in another if.  
https://github.com/boostorg/math/blob/5a4f8bab07aae3792614cf49e845cdf7f85c6e2d/include/boost/math/special_functions/detail/bessel_ik.hpp#L338-L341  
  
https://github.com/boostorg/math/blob/5a4f8bab07aae3792614cf49e845cdf7f85c6e2d/include/boost/math/special_functions/detail/bessel_ik.hpp#L415-L417  
  
Ok, I will try to create a test case...

---

## Comment 6

> Username: fsb4000  
> Created_at: 2024-08-16 09:44:59 UTC  
> Updated_at: 2024-08-16 11:06:30 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2293188540  

I found a test case for that:  
```  
boost::math::cyl_bessel_k(-1.25, std::numeric_limits<double>::infinity());  
```  
Where can I find what result should be for this?  
I added   
```  
std::cout << "v = " << v<< " x = " << x << " kind = " << kind << " reflect = " << reflect <<std::endl;  
std::cout << "u = " << u << " n = " << n << std::endl;  
```  
before `T z = (u + n % 2);` and I got(for version without my changes):  
```  
v = 1.25 x = inf kind = 2 reflect = 1  
u = 3.18226e-312 n = 4147116544 // I belive they are not initialized.  
```  
the call result was `0`  
What is the real result should be?  
Where do you add warning options? Sorry I'm not experienced with `b2`.

---

## Comment 7

> Username: fsb4000  
> Created_at: 2024-08-16 10:45:35 UTC  
> Updated_at: 2024-08-16 11:18:01 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2293277798  

I found a way to fail for that case(` BOOST_MATH_ASSERT(fabs(v - n - u) < tools::forth_root_epsilon<T>());`). And I think I found a more correct fix: calculate `n` and `u` before the `if`.  
I would also add the warning C4701 to your CI(and treat the warning as an error) but I don't know what to add to https://github.com/boostorg/math/blob/develop/.github/workflows/ci.yml for `toolset: [ msvc-14.2 ]`

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-08-16 19:22:26 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2294044022  

My bad, you are correct that this is NOT fixed in develop, but IS in this branch/PR: https://github.com/boostorg/math/pull/1111 that is still at least somewhat work in progress.  I would prefer not to create two competing fixes if possible, as the coverage test branch will be merged fairly soon anyway.  Please leave this open and I'll take a look at your test case once I get some time (which will be a couple of weeks).

---

## Comment 9

> Username: StephanTLavavej  
> Created_at: 2025-04-17 20:26:46 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2813955294  

@jzmaddock These warnings are still blocking MSVC's STL from updating its version of Boost.Math. Might you have a chance to look at this soon?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-04-18 08:49:46 UTC  
> Url: https://github.com/boostorg/math/pull/1181#issuecomment-2814947856  

Apologies @StephanTLavavej, since I haven't merged the other branch, lets just go ahead and merge this one, and I'll deal with the conflicts later.

---
