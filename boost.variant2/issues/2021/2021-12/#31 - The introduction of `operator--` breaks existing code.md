# #31 - The introduction of `operator<<` breaks existing code [Closed]

> Username: reddwarf69  
> Created at: 2021-12-09 10:08:50 UTC  
> Updated at: 2022-05-14 21:09:30 UTC  
> Closed at: 2022-05-14 21:09:30 UTC  
> Url: https://github.com/boostorg/variant2/issues/31  

The introduction of `operator<<` in https://github.com/boostorg/variant2/commit/e668c099ce650dba28815ae78fdd4eaa262a8396 breaks existing code.  
  
For example  
```  
#define CATCH_CONFIG_MAIN  
#include <catch2/catch.hpp>  
#include <boost/variant2/variant.hpp>  
  
struct my_type{};  
  
bool  
operator==(const my_type&, const my_type&) {  
	return true;  
}  
  
TEST_CASE( "Test" ) {  
    REQUIRE( boost::variant2::variant<int,my_type>{} == boost::variant2::variant<int,my_type>{} );  
}  
```  
  
works with Boost 1.77 but not with Boost 1.78.  
  
I have not looked at the details. But Catch uses `operator<<` if available (https://github.com/catchorg/Catch2/blob/v2.x/docs/tostring.md / https://github.com/catchorg/Catch2/blob/v2.x/single_include/catch2/catch.hpp#L1562), and fallback to something else otherwise. It seems Catch now sees variant2 has `operator<<`, so it doesn't use the fallback, but when it tries to use it it can't unless all the types in the variant have `operator<<`.  
  
A similar thing happens at the very least with https://github.com/rollbear/trompeloeil.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-09 19:12:04 UTC  
> Url: https://github.com/boostorg/variant2/issues/31#issuecomment-990144353  

Should be fixed on develop with https://github.com/boostorg/variant2/commit/3fc75407332c8117d6fb281ab7e32667830db97a.

---

## Comment 2

> Username: enrico-samknows  
> Created at: 2021-12-10 08:28:23 UTC  
> Url: https://github.com/boostorg/variant2/issues/31#issuecomment-990731766  

I'm going to try it soon. Thanks for the super fast fix!!

---

## Comment 3

> Username: reddwarf69  
> Created at: 2021-12-10 09:08:17 UTC  
> Url: https://github.com/boostorg/variant2/issues/31#issuecomment-990757261  

> Should be fixed  
  
In case you are waiting for feedback to close the issue: It **is** fixed.
