# #152 - Can't directly compare optional<int> to nullopt under GCC [Open]

> Username: tonyelewis  
> Created at: 2018-08-24 14:26:46 UTC  
> Updated at: 2018-10-04 20:58:14 UTC  
> Url: https://github.com/boostorg/test/issues/152  

The following code compiles under Clang but not under GCC:  
  
~~~.cpp  
#include <boost/test/unit_test.hpp>  
#include <optional>  
  
BOOST_TEST_DONT_PRINT_LOG_VALUE( ::std::nullopt_t     )  
BOOST_TEST_DONT_PRINT_LOG_VALUE( ::std::optional<int> )  
  
BOOST_AUTO_TEST_CASE(y) {  
    BOOST_TEST( ::std::make_optional( 5 ) == ::std::nullopt );  
}  
~~~  
  
I suspect that there's an issue with GCC's `std::is_constructible` needlessly explicitly instantiating `boost::test_tools::assertion::expression_base`'s `operator bool()` and I've raised [87093](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87093) about that.  
  
However, (a) the GCC devs may well reject that issue and (b) I suspect it's workaround-able either way and it'd be really nice to be able to express this directly. Please can you look into this?  
  
Thanks for all your work on this library.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-10-02 00:04:23 UTC  
> Url: https://github.com/boostorg/test/issues/152#issuecomment-426103047  

Thank you for those kind word!  
  
So ... the patch is in GCC now, what do you want me to do ? :-) I can create a dedicated comparison for `std::optional`.

---

## Comment 2

> Username: tonyelewis  
> Created at: 2018-10-04 20:58:13 UTC  
> Url: https://github.com/boostorg/test/issues/152#issuecomment-427166883  

I was wondering whether there's a way to achieve whatever the relevant piece of metaprogramming is trying to achieve with out stepping on this particular GCC issue.  
  
In particular, this GCC issue appears to be having trouble with `std::is_constructible<int, boost::test_tools::assertion::value_expr<std::optional<int> >&&>` in that it tries to instantiate an `operator bool` being considered for the conversion.  
  
Is there a way to perform the checks that get to this problem without invoking this issue?
