# #249 Change max_digits10 calculation to more accurate method. [Merged]

> Username: jzmaddock  
> Created at: 2020-06-11 17:31:16 UTC  
> Updated at: 2020-06-15 13:33:17 UTC  
> Merged at: 2020-06-15 10:43:09 UTC  
> Closed at: 2020-06-15 10:43:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249  



---

## Review 1 [Commented]

> Username: pabristow  
> Created_at: 2020-06-12 08:07:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/249#pullrequestreview-429543391  

More complicated, but much cooler. (@jzmaddock  never fails to rise to a challenge!)  
  
(I presume there will need to be/should be a change in some Boost.Multiprecision test of the results of this?)

---

## Review 2 [Commented]

> Username: cosurgi  
> Created_at: 2020-06-12 21:00:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/249#pullrequestreview-430034853  

📁 include/boost/multiprecision/traits/max_digits10.hpp

```diff
  17 |+       static constexpr unsigned constexpr_ceil(double d)
  18 |+       {
  19 |+          return static_cast<unsigned>(d) == d ? static_cast<unsigned>(d) : static_cast<unsigned>(d) + 1;
```

> Username: cosurgi  
> Created_at: 2020-06-12 21:00:00 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439640075  

about that `+ 1` at the end. I am [now doing](https://gitlab.com/cosurgi/minieigen-real/-/commit/54f3db9d6c0abe7360326ab20bcf6016deea4a48) some measurements for the value of how many [extra digits](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/lib/high-precision/RealHPConfig.cpp#L14) are necessary to properly [convert back and forth](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/lib/high-precision/RealIO.hpp#L34) and the results are quite surprising. The error consistently gets down. I thought that 3 or 4 is useful maximum. But so far, for example when calculating `sin(…)` in `long double` precision (BTW: I am testing all the precision types) I got maximum number of incorrect decimal digits (calculated as log10 of error with respect to ULP (unit of least precision - the last binary digit)):  
  
* for `1` extra string decimal digit the error is 3.1 decimal digits  
* for `2` error is 2.4 decimal digits  
* for `3` error is 0.9 decimal digits  
* for `4` error is 0.3 decimal digits  
* for `5` error is 0 decimal digits  
  
(these are preliminary results, since I'm searching for maximum error, it will only get larger)  
  
EDIT: these measurements aren't finished yet. I am waiting for the calculations to complete. This will take a few days. Then I will add [a section](https://yade-dem.org/doc/HighPrecisionReal.html) to yade documentation about that.  
  
EDIT2: In these calculations I am performing the same calculation for target type (eg. `long double` and compare the results with MPFR which has over 200 digits more).

> Username: jzmaddock  
> Created_at: 2020-06-13 08:35:40 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439721635  

I assume these are the additional digits required to get sin(x) correct to the last bit?  
  
Which is somewhat different to what we have here: "how many decimal places do I need to print to be able to round-trip back to the binary" ?

> Username: cosurgi  
> Created_at: 2020-06-13 11:59:23 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439733735  

You are correct. In principle I wanted to test the mathematical functions - to make sure that [my mapping](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/lib/high-precision/MathFunctions.hpp) finds the C++ code for implementation with correct precision. And that's how I found https://github.com/boostorg/math/issues/307  
  
Afterwards I realized how much the error depends on [`extraStringDigits10`](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/lib/high-precision/RealHPConfig.cpp#L14) and this forced me to make it configurable in runtime.  
  
So yeah, it's a bit more than roundtripping. I am doing here (for all math functions):  
  
xᵇᵃˢᵉ = random(-4π,4π) // other functions have a different range  
vᵇᵃˢᵉ = sinᵇᵃˢᵉ(xᵇᵃˢᵉ)   // where ᵇᵃˢᵉ means that it's using the tested type, eg. `long double`  
vᵐᵖᶠʳ = sinᵐᵖᶠʳ(binaryᵐᵖᶠʳ(string(xᵇᵃˢᵉ)))  
errorᵐᵖᶠʳ = |(binaryᵐᵖᶠʳ(string(vᵇᵃˢᵉ)) - vᵐᵖᶠʳ)/vᵐᵖᶠʳ|  
then I express error relative to ULP.  
  
Yeah, I think that it would be enough to test only the round tripping part. However I am just curious how well the particular implementations of math functions perform, and I wanted to check that :)  
  
> Which is somewhat different to what we have here: "how many decimal places do I need to print to be able to round-trip back to the binary" ?  
  
I will add a round tripping only test to the list of my tests :)


---

## Review 3 [Commented]

> Username: cosurgi  
> Created_at: 2020-06-12 21:04:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/249#pullrequestreview-430037105  

📁 include/boost/multiprecision/mpfr.hpp

```diff
2389 |    // Is this really correct???
2389 |-    BOOST_STATIC_CONSTEXPR int  max_digits10 = Digits10 + 3;
2390 |+    BOOST_STATIC_CONSTEXPR int  max_digits10 = boost::multiprecision::detail::calc_max_digits10<digits>::value;
```

> Username: cosurgi  
> Created_at: 2020-06-12 21:04:44 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439641836  

sorry, in my previous comment I was talking about this `+ 3` in fact, and the `+1` in `max_digits_10` :)

> Username: cosurgi  
> Created_at: 2020-06-12 21:18:57 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439646528  

BTW, that comment "`// Is this really correct???`" in this place is quite accurate :)

> Username: jzmaddock  
> Created_at: 2020-06-13 08:36:29 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439721699  

LOL, yes I know!  
  
I wish I could find a definitive reference for this somewhere, but it eludes me :(


---

## Comment 4

> Username: pabristow  
> Created_at: 2020-06-13 08:52:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643593648  

https://www.exploringbinary.com/number-of-digits-required-for-round-trip-conversions/ is helpful.  
  
And I believe there are later papers too but can't find at present.  
  
I fear that there is no absolutely correct answer to this so sorry if this is a wild goose chase.  
  
I just wanted the same max_digits10 for float128 and cpp_bin_float_quad

---

## Comment 5

> Username: cosurgi  
> Created_at: 2020-06-13 11:59:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643613622  

> I just wanted the same max_digits10 for float128 and cpp_bin_float_quad  
  
yeah. I know that I hijacked this thread a little. You were touching the lines of code which are very relevant to what I am doing right now ;)  
  
> https://www.exploringbinary.com/number-of-digits-required-for-round-trip-conversions/ is helpful.  
  
The table in this paper indicates that 3 digits is enough, my conclusions so far are more pessimistic :(

---

## Comment 6

> Username: pabristow  
> Created_at: 2020-06-13 14:50:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643633815  

https://github.com/mdickinson/float-proofs/blob/master/misc/base_conversion_notes.pdf  
  
Does brute force for all binary sizes extrapolating  inequality used in (above) example 8 provide a way for a confirmation of which formula is correct for round-tripping (the bottom line test)?

---

## Comment 7

> Username: cosurgi  
> Created_at: 2020-06-13 15:31:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643638909  

Thank you, this article is a good find. I am writing a brute force right now :) We will see pretty soon.

---

## Comment 8

> Username: cosurgi  
> Created_at: 2020-06-13 20:07:03 UTC  
> Updated_at: 2020-06-13 22:36:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643671624  

I realized that my tests which involved MPFR roundtripping have higher fidelity requirements than a basic binary ↔ string roundtripping. They require that the MPFR constructed from a string had all the "extra" bits to be set to zero. Otherwise going through the function would sometimes find a "spot" where the lower bits had significant impact on the function value (an unlikely example: the `tan(x)` sign change). This is why 4 or 5 extra digits make it better in my test: because it sets more and more lower bits to zero. So in fact I should have been using string with number of digits required by the MPFR_200 when feeding it a string from `long double`! I made here a quite specific mistake :) A lesson learnt.  
  
Simply put I needed to recognize the distinction that when I want to create MPFR_600 (later tests I was doing with 600) I need a lot more digits to make sure that all the lower-significant bits are created with zeros. Otherwise these lower bits reflect the "cut" decimal number it received.  
  
So that wasn't the test which we needed here for basic roundtripping.  
  
Now, getting back to topic. I did the brute force search by roundtripping a random number x∈[0,1) and 1/x∈[1,∞) 1e7 times. I did the search for two kinds: roundtrip between the same type. And roundtrip to MPFR_600 (this roundtrip which required that all lower significant bits are set to zero). Here are the results:  
  
| type                | sufficient extra digits    | found maximum extra digits | N=significand bits | length of 2^-N  |  
| ------------------- | -------------------- | -------------------- | ------------------ | --------------- |  
| float               | 3                    | 34                   | 24                 | 24              |  
| double              | 2                    | 56                   | 53                 | 53              |  
| long double         | 3                    | 61                   | 64                 | 64              |  
| float128            | 3                    | 96                   | 113                | 113             |  
| MPFR 62dec 207bit   | 2                    | 161                  | 207                | 207             |  
  
The first two rows in the table reflect results from roundtripping, and creating MPFR_600 with all extra bits set to zeros. Third column says how many bits are there.  
  
The last column "length of 2^-N" is the number of decimal digits after the point:  
  
```  
2^-24  = 0.000000059604644775390625  
2^-53  = 0.00000000000000011102230246251565404236316680908203125  
2^-64  = 0.0000000000000000000542101086242752217003726400434970855712890625  
2^-113 = 0.00000000000000000000000000000000009629649721936179265279889712924636592690508241076940976199693977832794189453125  
2^-207 = 0.000000000000000000000000000000000000000000000000000000000000004861730685829016958706300042015722062961134506813411822735247355304452214090143313424956893066963763247630148089939439159934408962726593017578125  
```  
  
You can look up details in the commit with [the C++ function](https://gitlab.com/cosurgi/minieigen-real/-/commit/3e90e3d68ac33dbea6708f8bde1748b810ba8917), and [the pipeline](https://gitlab.com/cosurgi/minieigen-real/-/pipelines/155933446) which calculates this stuff for each type.  
  
I am now quite confident that using extraStringDigits10==4 is always enough for the purposes of this thread. You could squeeze this into 3 and I would complain just a little ;) My findings also more or less agree with [the table](https://www.exploringbinary.com/number-of-digits-required-for-round-trip-conversions/) which you have linked before.

---

## Review 9 [Commented]

> Username: cosurgi  
> Created_at: 2020-06-13 20:18:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/249#pullrequestreview-430164501  

📁 include/boost/multiprecision/traits/max_digits10.hpp

```diff
  24 |+       // and avoid the call to ceil:
  25 |+       //
  26 |+       return static_cast<unsigned>(0.301029995663981195213738894724493026768189881462108541310 * d) + 2;
```

> Username: cosurgi  
> Created_at: 2020-06-13 20:18:10 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439766460  

How do you force compiler to use all these digits in this log10(2) value? I had problems forcing constexpr to work with `double` alone.

> Username: jzmaddock  
> Created_at: 2020-06-13 20:55:59 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439768599  

Ah, my bad, those extra digits are wasted - it's a double precision constant.  BTW I tried a reasonably exhaustive search comparing double precision calculation to 200-digit calculation and couldn't find a bit count value where they disagreed.  float precision is a different matter though.

> Username: cosurgi  
> Created_at: 2020-06-13 22:24:31 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439773698  

Hmm, I think you could leave these wasted digits. Maybe someday the compilers will start picking them up :)  
  
Since that's a double constant, with 15 (up to 17 :) digits, it means that this calculation will be incorrect for binary digits>1e15. That corresponds to 301029995663981 decimal digits. I didn't use MPFR_301029995663981 yet.  
  
> float precision is a different matter though.  
  
This calculation agrees for `float`, it has 24 bits and 6 digits10. And:  
  
`trunc(log10(2)*24)+2 == 9`, just like in that table and on page 46 of IEEE standard.  
  
> BTW I tried a reasonably exhaustive search comparing double precision calculation to 200-digit calculation and couldn't find a bit count value where they disagreed.  
  
oh. you meant a kind of calculation which I talked a lot about in the earlier post? I had plenty of examples where they disagree. But now that I increased extraStringDigits10, I have a hard time finding them again :) Today is too late. I will check tomorrow. Thank you for trying this!

> Username: cosurgi  
> Created_at: 2020-06-13 23:22:27 UTC  
> Updated_at: 2020-06-14 08:03:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439777204  

> That corresponds to 301029995663981 decimal digits  
  
... provided that the `unsigned` type is large enough :)  
  
About these errors: maybe they way in which I calculate the relative error is to blame. I will check this tomorrow.

> Username: cosurgi  
> Created_at: 2020-06-14 10:15:04 UTC  
> Updated_at: 2020-06-14 10:15:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439813667  

Do you know a clean method of checking a particular bit value in (any) floating point significand?  
  
Comparing bits would be much better, I suspect that my calculation of relative error has its own numerical inaccuracy problems :/

> Username: jzmaddock  
> Created_at: 2020-06-14 11:03:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439817643  

boost::math::float_distance ?  
  
Only for two types the same though.  For two different types, I would round the high precision type to the same number of bits as the low precision type so the comparison is against a "correctly rounded" value.

> Username: cosurgi  
> Created_at: 2020-06-14 16:11:11 UTC  
> Updated_at: 2020-06-14 16:12:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#discussion_r439844758  

I found a way with `frexp` function:  
  
```  
#include <boost/math/constants/constants.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <iostream>  
#include <limits>  
#include <vector>  
  
template <typename T> int sign(T val) { return (T(0) < val) - (val < T(0)); }  
  
class DecomposedReal {  
private:  
	int                        sig;  
	int                        exp;  
	std::vector<unsigned char> bits;  
  
public:  
	template <typename Rr> DecomposedReal(Rr x)  
	{  
		int ex   = 0;  
		Rr  norm = frexp(abs(x), &ex);  
		sig      = sign(x);  
		exp      = ex - 1;  
		ex       = 0;  
		int pos  = 0;  
		bits.resize(std::numeric_limits<Rr>::digits, 0);  
		while (norm != 0) {  
			pos -= ex;  
			assert((ex <= 0) and (pos < int(bits.size())) and (pos >= 0));  
			bits[pos] = 1;  
			norm -= static_cast<Rr>(0.5);  
			norm = frexp(norm, &ex);  
		};  
	}  
	template <typename Rr> Rr rebuild()  
	{  
		Rr  ret = 0;  
		int i   = 0;  
		for (auto c : bits) {  
			if (c != 0) {  
				ret += pow(static_cast<Rr>(2), static_cast<Rr>(exp - i));  
			}  
			++i;  
		}  
		return ret * static_cast<Rr>(sig);  
	}  
	template <typename Rr = double> void print()  
	{  
		std::cout << "sign : " << sig << "\n";  
		std::cout << "exp  : " << exp << "\n";  
		std::cout << "bits : ";  
		for (auto c : bits)  
			std::cout << int(c);  
		std::cout << "\nreconstructed number: " << rebuild<Rr>() << "\n\n";  
	}  
};  
  
template <typename Rr> void test(const Rr& arg)  
{  
	std::cout << std::setprecision(std::numeric_limits<Rr>::digits10 + 3);  
	std::cout << "original number     = " << arg << "\n";  
	DecomposedReal d(arg);  
	d.print<Rr>();  
	assert(arg == d.rebuild<Rr>());  
};  
  
int main()  
{  
	test<float>(-1);                                  // 2^0  
	test<double>(0);                                  // 0  
	test<long double>(-4);                            // -2^2  
	test<boost::multiprecision::float128>(0.375);     // 2^-2+2^-3  
	test<boost::multiprecision::mpfr_float_50>(1036); // 2^3+2^2+2^10  
	test<boost::multiprecision::mpfr_float_100>(boost::math::constants::pi<boost::multiprecision::mpfr_float_100>());  
}  
```  
  
gives output:  
  
```  
original number     = -1  
sign : -1  
exp  : 0  
bits : 100000000000000000000000  
reconstructed number: -1  
  
original number     = 0  
sign : 0  
exp  : -1  
bits : 00000000000000000000000000000000000000000000000000000  
reconstructed number: 0  
  
original number     = -4  
sign : -1  
exp  : 2  
bits : 1000000000000000000000000000000000000000000000000000000000000000  
reconstructed number: -4  
  
original number     = 0.375  
sign : 1  
exp  : -2  
bits : 11000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  
reconstructed number: 0.375  
  
original number     = 1036  
sign : 1  
exp  : 10  
bits : 100000011000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  
reconstructed number: 1036  
  
original number     = 3.141592653589793238462643383279502884197169399375105820974944592307816406286208998628034825342117067951  
sign : 1  
exp  : 1  
bits : 1100100100001111110110101010001000100001011010001100001000110100110001001100011001100010100010111000000011011100000111001101000100101001000000100100111000001000100010100110011111001100011101000000001000001011101111101010011000111011000100111001101100100010010100010100101000001000011110011000111000110100000001001101110111101111100101  
reconstructed number: 3.141592653589793238462643383279502884197169399375105820974944592307816406286208998628034825342117067951  
```  
  
I suppose that now I can do some bit comparison stuff :)


---

## Comment 10

> Username: cosurgi  
> Created_at: 2020-06-13 20:47:31 UTC  
> Updated_at: 2020-06-13 20:50:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643676307  

I'm not sure if this link will work for long, but I found the ["IEEE Standard for Floating-Point Arithmetic 2019"](https://sci-hub.tw/10.1109/IEEESTD.2019.8766229), there on pages 44-46 you have discussion about this conversion. Looks like your formula `trunc(log10(2) * d) + 2` is exactly the same as on page 46 in that doc! :)  
  
EDIT: I open that site with javascript disabled. In fact I have JS disabled almost everywhere, except github & gitlab ;)

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2020-06-13 20:56:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/249#issuecomment-643677216  

>Looks like your formula trunc(log10(2) * d) + 2 is exactly the same as on page 46 in that doc! :)  
  
Well that's a relief then ;)

---
