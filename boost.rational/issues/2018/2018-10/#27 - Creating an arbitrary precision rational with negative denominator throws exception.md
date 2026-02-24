# #27 - Creating an arbitrary precision rational with negative denominator throws exception [Closed]

> Username: Schniwarixl  
> Created at: 2018-10-09 07:38:40 UTC  
> Updated at: 2025-07-03 16:43:07 UTC  
> Closed at: 2025-07-03 16:43:07 UTC  
> Url: https://github.com/boostorg/rational/issues/27  

When using a rational with an arbitrary precision integer as given by...  
  
typedef boost::multiprecision::number<boost::multiprecision::cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::checked>> RationalIntType;  
  
typedef boost::rational<RationalIntType> Rational;  
  
The following will throw an exception...  
  
Rational test(-2, -4);  
  
This is due to the line   
  
   if (den < -(std::numeric_limits<IntType>::max)()) {  
        BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
    }  
  
in "template <typename IntType>void rational<IntType>::normalize()"  
  
where std::numeric_limits<IntType>::max() gives 0. Therefore all negative denominators will throw an exception.  
  
In previous boost version there was no test against max().

---

## Comment 1

> Username: psavouli  
> Created at: 2020-02-12 21:23:06 UTC  
> Updated at: 2020-02-12 21:24:06 UTC  
> Url: https://github.com/boostorg/rational/issues/27#issuecomment-585424199  

This bug was introduced with the fix for https://github.com/boostorg/rational/issues/18 released in boost 1.68.  
  
It can be reproduced with the following single line (throws at construction):  
  
`boost::multiprecision::cpp_rational rational(1, -2);`  
  
As Schniwarixl commented, the problem is the the check using `std::numeric_limits<T>::max` before ensuring that the denominator is positive. I believe this check is there to handle edge cases like `-(-2147483648)` which, for example, is not valid for a 32bit signed integer (maximum positive value is 2147483647).  
  
However, `std::numeric_limits<T>::max` [is only meaningful for bounded types](https://en.cppreference.com/w/cpp/types/numeric_limits/max). In the case of boost arbitrary precisions types, `std::numeric_limits<T>::max` [will return the default constructed value](https://www.boost.org/doc/libs/1_72_0/libs/multiprecision/doc/html/boost_multiprecision/tut/limits/functions.html). For a `cpp_int` specifically, this is 0.  
  
A solution is to check if the type is bounded or not, using `std::numeric_limits<T>::is_bounded` before calling `std::numeric_limits<T>::max`.

---

## Comment 2

> Username: psavouli  
> Created at: 2020-02-12 21:29:08 UTC  
> Url: https://github.com/boostorg/rational/issues/27#issuecomment-585426764  

Opened pull request with the proposed fix plus a unit test: https://github.com/boostorg/rational/pull/41.

---

## Comment 3

> Username: mekhontsev  
> Created at: 2021-04-08 09:43:29 UTC  
> Url: https://github.com/boostorg/rational/issues/27#issuecomment-815617924  

The code below throws exception:  
```cpp  
boost::rational<boost::multiprecision::cpp_int> r(1, -1)  
```  
  
Solution: the following line in the boost::rational::normalize() :  
```cpp  
if (den < -(std::numeric_limits<IntType>::max)()) {  
```  
  
must be replaced with:  
```cpp  
if (den == -den) {  
```

---

## Comment 4

> Username: PolarNick239  
> Created at: 2021-08-27 11:26:02 UTC  
> Url: https://github.com/boostorg/rational/issues/27#issuecomment-907132449  

+1, updated from boost 1.64 to 1.77 and encountered the same error.  
  
Workarounded it with  
  
```C++  
	if (d < 0) {  
		n *= -1;  
		d *= -1;  
	}  
        boost::rational<boost::multiprecision::cpp_int> rational(n, d);  
```

---

## Comment 5

> Username: Bolpat  
> Created at: 2023-08-10 19:33:09 UTC  
> Url: https://github.com/boostorg/rational/issues/27#issuecomment-1673796910  

I found a comment in my company’s code base referencing this issue by link. This seems to be solved (at least in 1.80.0). Why is the issue still open?
