# #668 - [Qi] double parser sometimes incorrect by one LSB [Open]

> Username: taam  
> Created at: 2021-04-23 15:40:14 UTC  
> Updated at: 2025-05-09 10:28:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/668  

Let's start with an example:  
  
```C++  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <boost/spirit/include/qi.hpp>  
  
int main() {  
        using namespace boost::spirit::qi;  
  
        std::string s = "-0.00093083996033856359";  
        double x;  
        if (parse(s.begin(), s.end(), double_ >> eoi, x)) {  
                std::cout << s << " -> " << std::setprecision(std::numeric_limits<double>::max_digits10) << x << std::endl;  
        }  
}  
```  
  
Output: `-0.00093083996033856359 -> -0.00093083996033856348`  
  
Another string/number that fails is "19403720.399719302" (`19403720.399719302 -> 19403720.399719305`), and there are many more. For all cases I've checked the binary comparison shows the mantissa being off by one. This is very bad e.g. due to (non-existing) round-trip safety.  
  
Tested on different platforms (32 and 64 bit) with at least Boost versions 1.73.0, 1.75.0 and 1.76.0.  
  
(Replacing `double_` with `long_double` appears to give correct results, but is a bad hack and also slow.)

---

## Comment 1

> Username: djowel  
> Created at: 2021-04-23 17:52:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825820804  

You are hitting the limits of the precision of double that is the underlying computation data type used by, double_. There were attempts to extract the last bit of precision, up to a certain point. The thing is, Spirit uses only the specified data type to provide as much speed as possible. Other parsers use higher precision data for computation, at the expense of speed. That is also why you can use long_double with ample accuracy to represent the least significant bits. In general, you need an accumulator that can provide more precision than a type T provides because floating-point computation is lossy (e.g. float -> double, double -> long_double, or perhaps some non-lossy  big-num data formats).

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-04-23 18:11:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825832030  

I heard of the issue and was actually looking into a reproducer.  
  
> In general, you need an accumulator that can provide more precision than a type T provides  
  
Not in this case. The problem is not in accumulator, but in the target type itself. `double(93083996033856359)` is `93083996033856352.0` (`93083996033856359.0 == 93083996033856352.0`).

---

## Comment 3

> Username: djowel  
> Created at: 2021-04-23 18:58:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825857794  

> I heard of the issue and was actually looking into a reproducer.  
>   
> > In general, you need an accumulator that can provide more precision than a type T provides  
>   
> Not in this case. The problem is not in accumulator, but in the target type itself. `double(93083996033856359)` is `93083996033856352.0` (`93083996033856359.0 == 93083996033856352.0`).  
  
General tip: Don't rely on the lowest bits of floating point types. They are lossy. If you want total precision, use a fixed type such as integers or rationals or even fixed-point.

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-04-23 19:37:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825876925  

> > I heard of the issue and was actually looking into a reproducer.  
> > > In general, you need an accumulator that can provide more precision than a type T provides  
> >   
> >   
> > Not in this case. The problem is not in accumulator, but in the target type itself. `double(93083996033856359)` is `93083996033856352.0` (`93083996033856359.0 == 93083996033856352.0`).  
>   
> General tip: Don't rely on the lowest bits of floating point types. They are lossy. If you want total precision, use a fixed type such as integers or rationals or even fixed-point.  
  
I cannot agree with that. AFAIK IEEE 754 was developed to achieve exact and consistent results. `93083996033856359` just does not have an exact mapping in `double` type, and Spirit parser assumes that `double(93083996033856359) / 10**20 == 0.00093083996033856359`, while it is not. I have seen that floating-point parser benchmarks usually disqualify Spirit because of the problem.

---

## Comment 5

> Username: jpyllman  
> Created at: 2021-04-23 21:21:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825931983  

If you look at the value you get if the compiler converts `double v = -0.00093083996033856359` as the last bit different then what spirit does it kind of looks like the way spirit does it is wrong. And I guess IEEE 754 specify that this is the only correct way. Anyway, the fmt library only ouputs the number as -0.0009308399603385636. I wonder if that actually would be correct according to the IEEE 754 and the C++ standard.

---

## Comment 6

> Username: Kojoley  
> Created at: 2021-04-23 21:44:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825941069  

> Anyway, the fmt library only ouputs the number as -0.0009308399603385636. I wonder if that actually would be correct according to the IEEE 754 and the C++ standard.  
  
fmt gets it right https://godbolt.org/z/6ds8nWqhG

---

## Comment 7

> Username: djowel  
> Created at: 2021-04-23 23:07:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825985747  

> I cannot agree with that. AFAIK IEEE 754 was developed to achieve exact and consistent results. `93083996033856359` just does not have an exact mapping in `double` type, and Spirit parser assumes that `double(93083996033856359) / 10**20 == 0.00093083996033856359`, while it is not. I have seen that floating-point parser benchmarks usually disqualify Spirit because of the problem.  
  
Yes, IEEE 754 was developed to give consistent results, at least within epsilon. IIRC, the accumulator has the same type (i.e. double in this case).   
  
@Kojoley, if you have time to track this issue down with a repro, that would be great.

---

## Comment 8

> Username: djowel  
> Created at: 2021-04-23 23:31:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825991895  

Just to be clear: I am not dismissing this issue... It is looking like it is a bug. It would be good to tighten up the test cases for numbers near the limits.

---

## Comment 9

> Username: Kojoley  
> Created at: 2021-04-23 23:37:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825993427  

> IIRC, the accumulator has the same type (i.e. double in this case).  
  
No, Qi uses integer accumulator since https://github.com/boostorg/spirit/commit/6832dc229872d7f9573a506cfe1ed640dc79871e  
  
> @Kojoley, if you have time to track this issue down with a repro, that would be great.  
  
I already tracked it down to `double(93083996033856359) / 10**20 != 0.00093083996033856359`. As for now I do not have any idea how to improve precision here. I hope there is a cheap solution to detect the situation and branch in some slowpath that will fix the result.

---

## Comment 10

> Username: djowel  
> Created at: 2021-04-24 00:00:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825998042  

> > IIRC, the accumulator has the same type (i.e. double in this case).  
>   
> No, Qi uses integer accumulator since [6832dc2](https://github.com/boostorg/spirit/commit/6832dc229872d7f9573a506cfe1ed640dc79871e)  
  
Ah, thanks for reminding me!  
  
> > @Kojoley, if you have time to track this issue down with a repro, that would be great.  
>   
> I already tracked it down to `double(93083996033856359) / 10**20 != 0.00093083996033856359`. As for now I do not have any idea how to improve precision here. I hope there is a cheap solution to detect the situation and branch in some slowpath that will fix the result.  
  
I see. And there it is again: the double computation. I'll have a look and add this test case. Can you point where in the code that happens? Thanks for looking, @Kojoley

---

## Comment 11

> Username: Kojoley  
> Created at: 2021-04-24 00:05:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-825999316  

> > > @Kojoley, if you have time to track this issue down with a repro, that would be great.  
> >   
> >   
> > I already tracked it down to `double(93083996033856359) / 10**20 != 0.00093083996033856359`. As for now I do not have any idea how to improve precision here. I hope there is a cheap solution to detect the situation and branch in some slowpath that will fix the result.  
>   
> I see. And there it is again: the double computation. I'll have a look and add this test case. Can you point where in the code that happens? Thanks for looking, @Kojoley  
  
Here https://github.com/boostorg/spirit/blob/56b4a5b99c4b55d30b72c8e6969b88f406bc7576/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp#L95

---

## Comment 12

> Username: djowel  
> Created at: 2021-04-24 00:15:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-826001448  

> Here  
>   
> https://github.com/boostorg/spirit/blob/56b4a5b99c4b55d30b72c8e6969b88f406bc7576/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp#L95  
  
Right. And this is kinda what I was saying. Spirit is using the underlying type for the computation (except that I forgot about the accumulator using integer). Other parsers are using a type with more precision for this, which slows down parsing. We can use a promoted data type in this case (which is already a special case with the if-else, so might not impact the speed too much), i.e. float->double and double->long double, but then we'll have to deal with long double another way.

---

## Comment 13

> Username: djowel  
> Created at: 2021-04-24 02:16:44 UTC  
> Updated at: 2021-04-24 02:20:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/668#issuecomment-826019120  

> We can use a promoted data type in this case (which is already a special case with the if-else, so might not impact the speed too much), i.e. float->double and double->long double, but then we'll have to deal with long double another way.  
  
I think I am leaning towards type promotion for the `else` branch. That will take care of `float` and `double`. We can deal with `long double` later, with a clear caveat in the docs.
