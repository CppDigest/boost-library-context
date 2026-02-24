# #566 Add circle constant tau to constants.hpp [Merged]

> Username: felixvd  
> Created at: 2021-03-13 04:22:34 UTC  
> Updated at: 2021-03-19 08:48:51 UTC  
> Merged at: 2021-03-16 08:42:37 UTC  
> Closed at: 2021-03-16 08:42:37 UTC  
> Url: https://github.com/boostorg/math/pull/566  

This is a proposal to add the circle constant `tau`, which is equal to 2*pi and represents one full rotation around the unit circle. Tau has seen increasing use as it makes the definition of rotations more intuitive. The motivation is explained better in [this](https://github.com/dotnet/runtime/issues/24678) or [this](https://github.com/ros-planning/moveit_tutorials/pull/554) PR, or this image from the original [Tau proposal](https://tauday.com/tau-manifesto):  
  
![image](https://user-images.githubusercontent.com/4535737/110638446-ec825d00-81f1-11eb-8cef-ca8062146773.png)  
  
I understand that there are quite a number of pi-based constants in Boost, and adding a tau version for all of them seems excessive. But adding tau itself would be a welcome convenience to users who are accustomed to it. Tau is already a constant in [.NET](https://github.com/dotnet/runtime/issues/24678), [Python](https://www.python.org/dev/peps/pep-0628/), [Rust](https://doc.rust-lang.org/nightly/std/f64/consts/constant.TAU.html), Unity, Julia and other programming languages. It would be great to be able to use it in C++ without resorting to a manual definition.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-03-13 14:00:02 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798416621  

Probably be easier to somehow alias `two_pi`. Note that there is a file `calculate_constants.hpp` where they must be evaluated to arbitrary precision, not just the string to 100 decimal digits.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-13 14:36:09 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798453763  

The following diff accomplishes what you want with aliasing as @NAThompson suggested:  
  
```  
diff --git a/include/boost/math/constants/constants.hpp b/include/boost/math/constants/constants.hpp  
index 9d2c9e503..02e113c99 100644  
--- a/include/boost/math/constants/constants.hpp  
+++ b/include/boost/math/constants/constants.hpp  
@@ -327,6 +327,18 @@ namespace boost{ namespace math  
   BOOST_DEFINE_MATH_CONSTANT(laplace_limit, 0.662743419349181580974742097109252907056233549115022417, "0.66274341934918158097474209710925290705623354911502241752039253499097185308651127724965480259895818168")  
 #endif  
   
+template<typename T, typename std::enable_if<std::is_trivially_constructible<T>::value, bool>::type = true>  
+constexpr T tau()  
+{  
+   return two_pi<T>();  
+}  
+  
+template<typename T, typename std::enable_if<!std::is_trivially_constructible<T>::value, bool>::type = true>  
+const T tau()  
+{  
+   return two_pi<T>();  
+}  
+  
 } // namespace constants  
 } // namespace math  
 } // namespace boost  
diff --git a/test/test_constants.cpp b/test/test_constants.cpp  
index 72ab2e19b..a107980f7 100644  
--- a/test/test_constants.cpp  
+++ b/test/test_constants.cpp  
@@ -150,6 +150,7 @@ void test_spots(RealType)  
    CHECK_ULP_CLOSE(3.14159265358979323846264338327950288419716939937510L/4, quarter_pi<RealType>(), 2);  
    CHECK_ULP_CLOSE(3.14159265358979323846264338327950288419716939937510L/6, sixth_pi<RealType>(), 2);  
    CHECK_ULP_CLOSE(2 * 3.14159265358979323846264338327950288419716939937510L, two_pi<RealType>(), 2);  
+   CHECK_ULP_CLOSE(2 * 3.14159265358979323846264338327950288419716939937510L, tau<RealType>(), 2);  
    CHECK_ULP_CLOSE(3 * 3.14159265358979323846264338327950288419716939937510L / 4, three_quarters_pi<RealType>(), 2);  
    CHECK_ULP_CLOSE(4 * 3.14159265358979323846264338327950288419716939937510L / 3, four_thirds_pi<RealType>(), 2);  
    CHECK_ULP_CLOSE(1 / (3.14159265358979323846264338327950288419716939937510L), one_div_pi<RealType>(), 2);  
(END)  
```

---

## Comment 3

> Username: felixvd  
> Created_at: 2021-03-13 14:59:35 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798480107  

Thanks for the pointer and the quick response! I added the diff as suggested, and removed the original definition. I hope this is right :)

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-03-13 16:08:14 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798564267  

Also the docs:  
  
https://github.com/boostorg/math/blob/develop/doc/constants/constants.qbk  
  
Should be a diff around line 320:  
  
```  
[[two_pi] [2[pi]] [6.28318] [Many uses, most simply, circumference of a circle]]  
```

---

## Comment 5

> Username: pabristow  
> Created_at: 2021-03-13 16:25:34 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798586967  

An alias is OK (though I've never encountered use of tau for 2 * pi - everyone in the world has heard of pi, even me ;-) ) .  And there are lots of uses of every other Greek letter which is why many of the names chosen are deliberately wordy  (Boost prefers clarity to conciseness).  
  
And you don't even think about removing two_pi - or there will be millions of users who will hate you!  
  
There are also C++ std::constants,  https://en.cppreference.com/w/cpp/numeric/constants which mean that only people wanting millions of decimal digits will want to use Boost.Constants.  These give up to 128-bit long double if you are lucky enough that your doubles are that big.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2021-03-13 19:18:26 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798770836  

@pabristow : Actually if you read the Tau Day Manifesto I think you'll be convinced that π is indeed wrong:  
  
https://tauday.com/tau-manifesto

---

## Comment 7

> Username: felixvd  
> Created_at: 2021-03-14 04:09:55 UTC  
> Updated_at: 2021-03-14 04:10:04 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798829575  

Thanks, I added an explanation to the doc, including this link that should have been in the original post: https://en.wikipedia.org/wiki/Turn_(angle)#Tau_proposals  
  
> And you don't even think about removing two_pi - or there will be millions of users who will hate you!  
  
That would be an unnecessary API change, and this change is for convenience, not annoyance. But there might be a case for making two_pi an alias of tau ;)  
  
> There are also C++ std::constants, https://en.cppreference.com/w/cpp/numeric/constants   
  
Yes, one step after the other. I don't even know where to submit the request.

---

## Comment 8

> Username: felixvd  
> Created_at: 2021-03-14 05:25:03 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798835682  

I noticed that today is Pi day, and that adding Tau on Pi day would be positively delightful.

---

## Comment 9

> Username: pabristow  
> Created_at: 2021-03-14 11:44:01 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-798892830  

I is wrong, obs ;-)

---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 12:56:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/566#pullrequestreview-611693015  

📁 include/boost/math/constants/constants.hpp

```diff
 329 | 
 330 |+ template<typename T, typename std::enable_if<std::is_trivially_constructible<T>::value, bool>::type = true>
 331 |+ constexpr T tau()
```

> Username: jzmaddock  
> Created_at: 2021-03-14 12:56:00 UTC  
> Updated_at: 2021-03-15 08:42:13 UTC  
> Url: https://github.com/boostorg/math/pull/566#discussion_r593896195  

Sorry to be late to the party here... I don't believe we need the two overloads here, simply:  
  
```  
template <typename T>  
inline constexpr T tau() {  return two_pi<T>(); }  
```  
  
should suffice as non-trivial types can still be constexpr.

> Username: felixvd  
> Created_at: 2021-03-15 06:44:40 UTC  
> Updated_at: 2021-03-15 08:42:13 UTC  
> Url: https://github.com/boostorg/math/pull/566#discussion_r594083479  

I don't know enough to judge this, but I'm happy to change this if there is a consensus.

> Username: ckormanyos  
> Created_at: 2021-03-15 06:54:44 UTC  
> Updated_at: 2021-03-15 08:42:13 UTC  
> Url: https://github.com/boostorg/math/pull/566#discussion_r594087117  

> happy to change this if there is a consensus  
  
Yes, to me also it seems like the single top-layer function should properly find the underlying overloads as suggested by @jzmaddock.  
  
... And this has the added, subtle advantage of not creating yet another storage requirement for a separate constant.

> Username: felixvd  
> Created_at: 2021-03-15 08:43:47 UTC  
> Url: https://github.com/boostorg/math/pull/566#discussion_r594142595  

I pushed the change. Since @mborland kindly included a test in his diff, CI should show if it doesn't work.


---

## Comment 11

> Username: felixvd  
> Created_at: 2021-03-16 01:22:53 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-799871822  

The CI job `ubuntu-bionic (clang++-8, c++14, interpolators)` failed, but I don't see more information than "Check failed". It is also curious that the same `interpolators` test passes in `ubuntu-focal` and `ubuntu-xenial`. Is this a fluke? Does the test need to be retriggered? Let me know if anything needs to be done.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-03-16 08:42:30 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-800069242  

Failures look spurious, merging.

---

## Comment 13

> Username: felixvd  
> Created_at: 2021-03-19 06:54:13 UTC  
> Updated_at: 2021-03-19 06:55:01 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-802600959  

Thank you for the kind support in writing this PR. It seems that a consensus had been brewing silently.  
  
After @pabristow brought up std::numbers, I took the liberty of emailing the authors of the proposal that became the `std::numbers` header, asking about the procedure and chances of success of a proposal to add tau (and other numbers). I received their permission to post the conversation, and I think this is the best place to put it. I hope no one minds.  
  
**TL;DR**: A proposal for std::numbers is feasible, but probably more like to succeed with a batch of convenience constants (like this collection), and there are technical considerations to consider.   
  
---  
  
**From: felixvd**  
  
Hi Lev, hi John,  
  
I hope you are well. I found your email address in [your proposal for Math Constants](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0631r8.pdf), and I am emailing you because I have a few questions about this sort of proposal. It would be very kind if you could share your insights or point me in the right direction.  
  
Recently, the circle constant tau was added to boost-math, and in the PR it was suggested to add it to std::numbers as well. Is it realistic to submit a similar proposal to add tau to std::numbers? If yes, what would need to be done to make it happen?  
  
Thank you in advance for your time and best regards  
  
Background: As explained in [these](https://github.com/dotnet/runtime/issues/24678) [two](https://github.com/ros-planning/moveit_tutorials/pull/554) PRs (and the [original proposal](https://tauday.com/tau-manifesto)), tau is convenient for defining rotations, and it is already a constant in .NET, Python, Rust, Unity, Julia and other languages. Having it in C++ would be quite helpful.  
  
---  
  
**From: John McFarlane**  
  
Hi Felix,  
  
Thanks for getting in touch.  
  
I think it would not be entirely surprising for the committee to receive such a proposal and I can believe there is a chance of adoption. But a few pieces of advice come to mind.  
  
Firstly, as I explain in [this post](https://stackoverflow.com/questions/61900861/why-isnt-inv-sqrt2-defined-in-the-c-standard-library), the set of numbers which were standardised were ones which are hard to derive accurately. It doesn't get much easier to derive tau -- at least using binary floating-point types -- so nobody is left high and dry by its absence the same way that are without pi. That should at least explain one reason why it's not there. It may be a reason why the proposal is not considered to be as high a priority as tau proponents would wish.  
  
Secondly, I'm not familiar with Boost.Math but would be interested to know whether its definitions are meant to be customisation points. The ones in std::numbers are, which affects the cost of adding new ones.  
  
Imagine you define a new number type which you want to use in existing algorithms. If those algorithms use tau, you ideally need to specialise tau for your type. That is a burden on the implementer of any new numeric types (of which I am one). So I would hope that any proposal differentiated values such as tau and inv_sqrt2 from the others such as pi and sqrt2, in order to cut down on duplicated effort. (Conversely, it would still be beneficial to allow customisation for types which cannot easily derive those values.)  
  
Thirdly, depending on many random factors such as people in the room and local weather conditions, there may be a drive for one of either:  
  
1. a minimal paper introducing one single new value, to keep things simple and manageable;  
2. or (more likely) a 'bargain bucket' paper introducing everything that everybody would like to see, to economise on committee time.  
  
You would probably wish to mention any such options in a paper and briefly weigh up the pros and cons of different choices. When it is presented to WG21, make sure that a decision is made -- one way or the other -- with the result of any polls recorded in a follow-up revision of the paper. In that way, you can reduce the incident of repeated discussions which often occur when the wind changes direction.  
  
HTH,  
John  
  
---  
  
**From: felixvd**  
  
Hi John, hi Lev,  
  
Thank you very much for your prompt, detailed and insightful reply. It helped me understand the situation better, and it was good to see that my own impressions of committee work align with your tactical suggestions.   
  
I was not familiar with customization points, so your explanation about the implications of a change in this header was particularly educational. I understand that a separation between the current std::numbers (difficult to derive) and others (easily-derived but convenient) would be required, and that a mechanism to allow optional specialization of the latter would be desirable.  
  
Considering the inevitable discussion about the choice of numbers to include, the second alternative you proposed (a catalog of commonly used constants) seems more likely to pass. The Boost.Math module might actually make a good point of departure for such a catalog, given the number of constants added to it by popular demand. I will give it some more thought.  
  
Would you mind if I posted this exchange? I think your points are of general interest, and I feel like I should not be the only one to read them.  
  
Thank you once again and have a good week.  
  
---  
  
**From: John McFarlane**  
  
>  [...]  
  
In case you're not familiar with P0631, Lev's analysis of existing sets of numbers includes Boost and other sources.   
  
> Would you mind if I posted this exchange? I think your points are of general interest, and I feel like I should not be the only one to read them.  
  
By all means.   
  
> Thank you once again and have a good week.  
  
You too and good luck!  
John  
  
---

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-03-19 08:48:51 UTC  
> Url: https://github.com/boostorg/math/pull/566#issuecomment-802660337  

>  a 'bargain bucket' paper introducing everything that everybody would like to see, to economise on committee time.  
  
This seems like a wise course of action to me. After the success of obtaining `<numbers>` in C++20 (many thanks to all who worked hard on this), it seems like experience may teach us what number(s) might be missing, have strong interest in the community, and be worth adding in sort of a cumulative update in a future language standard.

---
