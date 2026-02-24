# #423 - Abs function on cpp_bin_float throwing C2665 error [Closed]

> Username: gpive  
> Created at: 2022-01-28 16:42:31 UTC  
> Updated at: 2022-02-15 20:51:12 UTC  
> Closed at: 2022-02-15 19:03:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423  

Build is done using MSVC 2015.  
  
This code throws a C2665 error on Boost 1.78.0 but worked on Boost 1.70.0.  
  
typedef boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<256, boost::multiprecision::digit_base_2>> high_prec_double;  
high_prec_double x(0.0);  
abs(x);

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-30 11:07:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1025119602  

Works for me with:  
  
Microsoft (R) C/C++ Optimizing Compiler Version 19.00.24245 for x64

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-02-14 17:09:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039335454  

Ping?  Can the OP provide a test case please?

---

## Comment 3

> Username: gpive  
> Created at: 2022-02-14 18:47:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039431598  

Build was on MSVC Professional 2015 Version 14.0.25431.01 Update 3.  
  
Isolated it better and found code segment only failed with a specific template being included.  
  
Error does not occur on Boost 1.70 or using "fabs" instead of "abs".  
  
Here's the full sample code...  
  
#include <boost\multiprecision\cpp_bin_float.hpp>  
  
template<class T, class U> static inline bool operator!=(const T& a, const U& b)  
{  
    return !(a == b);  
};  
  
typedef boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<256, boost::multiprecision::digit_base_2>> high_prec_double;  
  
void TestAbs()  
{  
    high_prec_double x(0.0);  
    abs(x);  
}  
  
Which throws this error  
  
1><Removed>.cpp(13): error C2665: 'abs': none of the 6 overloads could convert all the argument types  
1>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\cmath(328): note: could be 'long double abs(long double) noexcept'  
1>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\cmath(30): note: or       'float abs(float) noexcept'  
1>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\cmath(17): note: or       'double abs(double) noexcept'  
1>  C:\Program Files (x86)\Windows Kits\10\Include\10.0.10240.0\ucrt\stdlib.h(359): note: or       '__int64 abs(const __int64) throw()'  
1>  C:\Program Files (x86)\Windows Kits\10\Include\10.0.10240.0\ucrt\stdlib.h(354): note: or       'long abs(const long) throw()'  
1>  C:\Program Files (x86)\Windows Kits\10\Include\10.0.10240.0\ucrt\stdlib.h(284): note: or       'int abs(int)'  
1>  <Removed>.cpp(13): note: while trying to match the argument list '(DataHandling::high_prec_double)'

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-02-14 19:12:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039455174  

That's an older setup.  
  
To me it looks like the compiler can't resolve the exact instantiation of `abs`to use.  
  
Could you maybe try to place the line `using std::abs;` as the first line within the scope of the subroutine `TestAbs()`?

---

## Comment 5

> Username: gpive  
> Created at: 2022-02-14 19:49:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039488327  

> That's an older setup.  
>   
> To me it looks like the compiler can't resolve the exact instantiation of `abs`to use.  
>   
> Could you maybe try to place the line `using std::abs;` as the first line within the scope of the subroutine `TestAbs()`?  
  
Explicitly defining the call as std::abs also failed with the same error.  
  
I'm removing these templates now but not sure why this particular one is causing issues.  Was there to save me having to define operator!=, operator>, operator>=, and operator <= when I already defined operator== and operator<.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-02-14 21:13:27 UTC  
> Updated at: 2022-02-14 21:17:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039565797  

I tried 1.70 on a newer MSVC compiler and had no problem. So at the moment, I can not exactly reproduce the behavior. I would need the old compiler, which I do not have at the moment.  
  
Out of historical interest, it's kind of interesting.  
  
I had meant try a few things such as:  
  
```  
void TestAbs()  
{  
  using std::abs;  
  
  high_prec_double x(0.0);  
  
  abs(x);  
}  
```  
  
I think this might, however, not make a difference in your particular use case. I'm pretty sure Multiprecision's namespace version of `abs` is/was present at the time. I am not exactly sure why the compiler can't resolve it.

---

## Comment 7

> Username: gpive  
> Created at: 2022-02-14 21:44:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1039598620  

Whatever is causing the problem, I at least have a work around.  
  
In the above comment, I tried "std::abs(x)" instead of the using function but it'd be the same result.  
  
Also moving from that nuclear set of templates to a more conditional set of macros should help.  Just interesting this is the only function I've ever found it to cause a problem on.  
  
#define AT5_OP_NE(x) bool operator!=(const x& rhs) const { return !(operator==(rhs));}  
#define AT5_OP_LTE(x) bool operator<=(const x& rhs) const { return !(rhs.operator<(*this));}  
#define AT5_OP_GT(x) bool operator>(const x& rhs) const { return (rhs.operator<(*this));}  
#define AT5_OP_GTE(x) bool operator>=(const x& rhs) const { return !(operator<(rhs));}  
#define AT5_OP_COMP(x) AT5_OP_NE(x) AT5_OP_LTE(x) AT5_OP_GT(x) AT5_OP_GTE(x)

---

## Comment 8

> Username: ckormanyos  
> Created at: 2022-02-15 18:40:19 UTC  
> Updated at: 2022-02-15 18:41:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1040638656  

>  have a work around  
  
That is best. There is, in fact, not usually a strong effort to post-patch a 2 year old Boost. Unfortunately, we can't at the moment seem to find an equitable fix, but please keep going with the workaround.  
  
One last query from my side, wondering if using `fabs` might be a viable option and if the compiler resolution is better therewith?  
  
For the sake of completeness, ... I don't know all the features of your environment. If you do, however, have the chance to jump up a few compiler/Boost versions, there has really been some great progress. In particular the next and past few releases have been key since we are/have-gone standalone and C++11 on Math/Multiprecision. So if you get a chance, some of the modern stuff might be more flexible and easier to manage. MSVC has made fantastic strides toward language adherence. So things are really a lot more rosy these days.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-02-15 19:03:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1040671941  

I can reproduce, but I'm going to close this on the basis that you've shot yourself in the foot ;)  Let me try and explain:  
  
The overload for abs which we need to specialize looks like this:  
  
```  
template <class Backend>  
typename std::enable_if<number_category<Backend>::value != number_kind_complex, number<Backend, et_off> >::type  
abs(const number<Backend, et_off>& arg)  
```  
  
And if I change your unqualified call to abs with a qualified call to boost::multiprecision::abs we get a slightly clearer error message:  
  
```  
t.cpp(13): error C2672: 'boost::multiprecision::abs': no matching overloaded function found  
t.cpp(13): error C2893: Failed to specialize function template 'std::enable_if<boost::multiprecision::number_category<Num>::value==,boost::multiprecision::component_type<boost::multiprecision::number<Backend,ExpressionTemplates>>>::type::type boost::multiprecision::abs(const boost::multiprecision::number<Backend,ExpressionTemplates> &)'  
t.cpp(13): note: With the following template arguments:  
t.cpp(13): note: 'T=boost::multiprecision::backends::cpp_bin_float<256,boost::multiprecision::backends::digit_base_2,void,int,0,0>'  
t.cpp(13): note: 'ExpressionTemplates=boost::multiprecision::et_off'  
```  
  
Digging deeper, the reason it failed to specialize, is that your != operator is found for the enable_if argument where two enum's are compared.  VC2015 thinks the operator is now ambiguous, so it rejects the whole function overload.  
  
In short, don't write overloads that are so greedy, as all kinds of unexpected interactions may occur!  
  
I note that more recent msvc versions do not have this issue, but none the less your operators are still over-greedy IMO.  
  
HTH.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2022-02-15 19:14:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1040688359  

Here's an example which fails without boost, and which illustrates the issue:  
  
```  
template<class T, class U> static inline bool operator!=(const T& a, const U& b)  
{  
   return !(a == b);  
};  
  
enum number_category_type  
{  
   number_kind_unknown = -1,  
   number_kind_integer = 0,  
   number_kind_floating_point = 1,  
   number_kind_rational = 2,  
   number_kind_fixed_point = 3,  
   number_kind_complex = 4  
};  
  
template <bool>  
struct dont_do_this {};  
  
dont_do_this<number_kind_unknown != 0> t;  
```

---

## Comment 11

> Username: gpive  
> Created at: 2022-02-15 19:28:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1040701004  

Roger that.  Sorry about the blood on the carpet. : )

---

## Comment 12

> Username: ckormanyos  
> Created at: 2022-02-15 20:50:58 UTC  
> Updated at: 2022-02-15 20:51:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/423#issuecomment-1040779937  

Thank you John (@jzmaddock) for providing such clarity. It is good you found an explanation.
