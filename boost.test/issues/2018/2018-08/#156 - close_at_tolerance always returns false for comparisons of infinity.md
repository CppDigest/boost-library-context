# #156 - close_at_tolerance always returns false for comparisons of infinity [Closed]

> Username: Timmmm  
> Created at: 2018-08-31 15:04:17 UTC  
> Updated at: 2018-10-03 09:00:58 UTC  
> Closed at: 2018-10-01 22:48:53 UTC  
> Url: https://github.com/boostorg/test/issues/156  

Consider this code:  
  
```  
#include <boost/test/tools/floating_point_comparison.hpp>  
#include <iostream>  
#include <limits>  
  
namespace fpc = boost::math::fpc;  
int main() {  
	float a = std::numeric_limits<float>::infinity();  
	float b = std::numeric_limits<float>::infinity();  
  
	auto P = fpc::close_at_tolerance<float>(fpc::percent_tolerance<float>(0.01), fpc::FPC_WEAK);  
	std::cout << P(a, b) << "\n";  
}  
```  
  
It prints `0`. You might say "well that makes sense because one infinity is not the same as another". Except that the IEEE floating point standard considers `inf == inf` to be true, which means that you have the strange situation where this passes:  
  
```  
BOOST_AUTO_TEST_CASE(foo)  
) {  
	float a = std::numeric_limits<float>::infinity();  
	float b = a;  
	BOOST_TEST(a == b);  
}  
```  
  
But this does not:  
  
```  
BOOST_AUTO_TEST_CASE(foo,  
	*utf::tolerance<float>(fpc::percent_tolerance<float>(0.01))  
) {  
	float a = std::numeric_limits<float>::infinity();  
	float b = a;  
	BOOST_TEST(a == b);  
}  
```  
  
Also, I'm 90% sure the latter case did pass in the past (Boost 1.60 at least).

---

## Comment 1

> Username: Timmmm  
> Created at: 2018-08-31 15:22:34 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-417698538  

I have double checked and that test definitely passes on Boost 1.60.0 but not 1.67.0. Unfortunately brew doesn't have any versions between those so I can't easily narrow it down. :-(

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2018-08-31 18:21:30 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-417750399  

Thanks for filing this bug and checking about the regression. Not so much happened on this part since some time, and I believe the fix is quite easy. The only thing I do not know about is how other floating point types (like multiprecision) handle `infinity`.

---

## Comment 3

> Username: Timmmm  
> Created at: 2018-09-01 16:30:17 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-417870884  

Do you happen to know what the fix is or which commit changed the behaviour?

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-09-01 17:23:48 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-417874592  

I believe this is this one: https://svn.boost.org/trac10/ticket/13011  
Rev bf703c2628246708b35551f81c6e9b1080e2a767   
  
I will check tonight.

---

## Comment 5

> Username: Timmmm  
> Created at: 2018-09-03 08:37:38 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-418043115  

Ah cool, I patched it like this and it works for us now:  
  
```  
inline assertion_result  
compare_fpv( Lhs const& lhs, Rhs const& rhs, op::EQ<Lhs,Rhs>* )  
{  
    if (lhs == rhs) {  
        return true;  
    }  
    ...  
```

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-09-29 08:08:49 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-425626146  

It is fixed on the branch `topic/GH-156-comparison-to-infinity`. However `close_at_tolerance` is using a tolerance value, and does not handle 0 nor infinity. I updated the doc as well.  
  
Let me know if the branch works for you

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2018-10-01 22:48:53 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-426089671  

In develop, thanks again for the report. Closing.

---

## Comment 8

> Username: Timmmm  
> Created at: 2018-10-03 09:00:18 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-426562257  

Seems like our tests have changed and I can't get them to fail now, but the fix looks good to me - thanks!

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2018-10-03 09:00:58 UTC  
> Url: https://github.com/boostorg/test/issues/156#issuecomment-426562455  

You're welcome!
