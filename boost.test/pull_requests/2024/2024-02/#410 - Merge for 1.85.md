# #410 Merge for 1.85 [Merged]

> Username: mborland  
> Created at: 2024-02-28 07:13:50 UTC  
> Updated at: 2024-02-29 12:51:39 UTC  
> Merged at: 2024-02-29 12:51:38 UTC  
> Closed at: 2024-02-29 12:51:39 UTC  
> Url: https://github.com/boostorg/test/pull/410  



---

## Comment 1

> Username: mborland  
> Created_at: 2024-02-28 09:47:32 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1968605643  

@raffienficiaud Any idea how to fix the one new test case failure here: https://github.com/boostorg/test/actions/runs/8076642786/job/22065447368?pr=410#step:8:2024. The build starts failing once this commit is applied to boost::function: https://github.com/boostorg/function/commit/af8e66d03b4366c7039a138306470567c2243130. There's only 4 uses of `safe_bool` in the library, but I am not entirely sure how they are relevant.  
  
CC: @pdimov

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2024-02-28 10:00:13 UTC  
> Updated_at: 2024-02-28 10:00:52 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1968628786  

Hi @mborland . From what I quickly see, it comes from statements like [this](https://github.com/boostorg/test/blob/2134870a35a030679670200d65c624075e2f79c8/test/test-organization-ts/test-tree-management-test.cpp#L118) and this basically checks that the underlying `boost::function` is initalized. From the changes in `boost::function` you are referring to, now the cast to `bool` is explicit, which might explain the error.  
  
I can give you more details tonight.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-02-28 10:51:38 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1968722398  

The error is  
```  
./boost/test/utils/class_properties.hpp:86:15: error: no match for 'operator<<' (operand types are 'std::basic_ostream<char>' and 'const boost::function<void()>')  
     return os << p.get();  
               ^  
```  
which tries to output a `boost::function<void>` to an `ostream`.  
  
I don't think this is supposed to happen. It probably worked by chance before by implicitly casting the `function` to `bool` and outputting that, but I don't think this was intentional.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2024-02-28 22:35:04 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1970034932  

@mborland replacing that faulty line by this seems to work  
  
```  
BOOST_TEST( static_cast<bool>(tc1->p_test_func.get()) );  
```  
  
the diff:  
```  
raffi@raffi-macpro test % git diff  
diff --git a/test/test-organization-ts/test-tree-management-test.cpp b/test/test-organization-ts/test-tree-management-test.cpp  
index 97a22804..37947c9c 100644  
--- a/test/test-organization-ts/test-tree-management-test.cpp  
+++ b/test/test-organization-ts/test-tree-management-test.cpp  
@@ -115,7 +115,7 @@ BOOST_AUTO_TEST_CASE( manual_test_case_creation_test )  
     BOOST_TEST( tc1->p_expected_failures == 0U );  
     BOOST_TEST( tc1->p_timeout == 0U );  
     BOOST_TEST( tc1->p_name == const_string( "empty_" ) );  
-    BOOST_TEST( tc1->p_test_func );  
+    BOOST_TEST( static_cast<bool>(tc1->p_test_func.get()) );  
     BOOST_TEST( tc1->p_default_status == test_unit::RS_INHERIT );  
     BOOST_TEST( tc1->p_run_status == test_unit::RS_INVALID );  
     BOOST_TEST( !tc1->is_enabled() );  
```  
  
I am actually not seeing any other error on `develop`. Do you want me to push a fix?

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-02-28 23:55:25 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1970122937  

But that's not the right fix. This BOOST_TEST should work (as it should for any type that has an `explicit operator bool` and does not support `os << x`.)  
  
The question is why it tries to print using `operator<<`, when the type (`boost::function` in this case) doesn't support it.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-02-29 00:06:38 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1970139787  

Or maybe BOOST_TEST always requires the values to be printable? I looked at the documentation but can't find this stated one way or another.  
  
I tried  
  
```  
#define BOOST_TEST_MODULE something  
#include <boost/test/included/unit_test.hpp>  
  
struct X  
{  
    explicit operator bool () const noexcept  
    {  
        return true;  
    }  
};  
  
BOOST_AUTO_TEST_CASE(something)  
{  
    X x;  
    BOOST_TEST( x );  
}  
```  
  
and it causes a static assertion  
  
```  
1>c:\boost-git\develop\boost\test\tools\detail\print_helper.hpp(53,13): error C2338: static_assert failed: 'Type has to implement operator<< to be printable'  
```  
  
so maybe having `operator<<` _is_ a requirement?

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2024-02-29 00:40:34 UTC  
> Updated_at: 2024-02-29 00:42:03 UTC  
> Url: https://github.com/boostorg/test/pull/410#issuecomment-1970172115  

> The question is why it tries to print using `operator<<`, when the type (`boost::function` in this case) doesn't support it.  
  
you gave an answer to your question: the underlying type [has to be printable](https://www.boost.org/doc/libs/1_84_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/testing_tool_output_disable.html).  
  
Note that here, `tc1->p_test_func` is not the `boost::function<void ()>` itself, but a wrapper `readonly_property` which derives from `class_property`. The `class_property::get()` in the proposed fix gives access to the underlying `boost::function`, the cast to `bool` operates on the `boost::function<void ()>`.  
  
`class_property` itself delegates the `operator<<` to the underlying type [here](https://github.com/boostorg/test/blob/2134870a35a030679670200d65c624075e2f79c8/include/boost/test/utils/class_properties.hpp#L86).  
  
> so maybe having operator<< is a requirement?  
  
There are other ways (see link above) but this is usually a pain point and should be improved.

---
