# #41 - complex number type [Closed]

> Username: jtravs  
> Created at: 2018-01-29 21:27:52 UTC  
> Updated at: 2018-08-02 05:14:20 UTC  
> Closed at: 2018-07-29 16:10:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41  

Is there any work or reference for a complex number type based on a ```gmp_float``` or ```mpfr_float```? I believe that I cannot just use e.g. g++ or clang ```std::complex<T>```.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-01-30 08:45:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-361517176  

Someone had a student working on it, I've just pinged them.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2018-02-01 21:27:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-362407204  

There was another person interested in this a few years ago. I think might even be on the review schedule (or was on it fow a while a few years ago). But no one is driving it forward. Recently, the student that John mentioned has also worked in this area. I have an implementation that I would be happy to share if there is any interest in comparing notes. Although std::complex is only specified for instantiation with float, double and long double, there is a chance that it might (in a non-specified way) work with UDTs.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2018-02-01 21:38:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-362410566  

Here it is if anyone would like to take a look. This code has been extensively tested with built-in float, double and long double. It has also been tested with Boost.Multiprecision types and with a variety of fixed-point types on target systems ranging from 8-bit, 16-bit, 32-bit, 64-bit.  
[https://github.com/ckormanyos/real-time-cpp/blob/master/ref_app/src/math/extended_complex/extended_complex.h](url)

---

## Comment 4

> Username: ckormanyos  
> Created at: 2018-02-01 21:40:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-362410987  

The link was broken. It is here.  
[https://github.com/ckormanyos/real-time-cpp/blob/master/ref_app/src/math/extended_complex/extended_complex.h]

---

## Comment 5

> Username: jtravs  
> Created at: 2018-02-01 21:44:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-362412089  

Thank you for sharing this. I will test it myself.

---

## Comment 6

> Username: mglisse  
> Created at: 2018-02-25 12:17:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-368304653  

Looking at  
https://gcc.gnu.org/ml/gcc-patches/2014-02/msg01377.html  
and the SO post referenced there, it seems that std::complex+Boost.Multiprecision worked at some point with libstdc++ (gcc), though it didn't work with libc++ (llvm). I don't know what the current status is.  
Writing a backend based on MPC should be better than using mpfr_float, but that's likely more work, especially if one wants optimized interaction with mpfr_float.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-02-25 13:04:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-368307745  

There's never been any guarantee that std::complex will work with anything except float, double and long double unfortunately.  Writing a backend based on MPC looks reasonable straightforward actually, there would be a lot of details to figure out though I'm sure.

---

## Comment 8

> Username: ofloveandhate  
> Created at: 2018-03-08 17:33:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-371562191  

indeed, as John says, std::complex with things other than float, double and long double can be made to "work" (compile) but some functions like `arg` produce low-precision numbers no matter the precision of the scalar type, so the results are not reliable.

---

## Comment 9

> Username: ofloveandhate  
> Created at: 2018-03-08 17:54:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-371568782  

i have prepared and attached a test file to contribute to the `mpc` effort.  i am sure there are holes in my tests (i welcome criticism!), and they perhaps test things other don't care about such as   
  
* random number generation, and   
* basic compatibility with   
  * Boost.Serialize and   
  * Eigen    
  
but I really want those features, so left them in.   
  
[complex_test.cpp.zip](https://github.com/boostorg/multiprecision/files/1794219/complex_test.cpp.zip)  
  
  
  
thanks!  ~Danielle

---

## Comment 10

> Username: mglisse  
> Created at: 2018-03-08 19:03:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-371589172  

> but some functions like arg produce low-precision numbers no matter the precision of the scalar type  
  
Do they? As I understand the libstdc++ code for arg, it calls atan2, and if you have an overload of atan2 for your multiprecision type, it uses it.  
(I know that's not portable to other implementations, I am only talking about libstdc++)

---

## Comment 11

> Username: ofloveandhate  
> Created at: 2018-03-08 19:27:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-371596324  

i have had problems with it personally... essentially getting a double precision result from my high-precision scalar type (`mpfr_float` with variable precision), so something in the middle got de-rezzed <word choice?>.  maybe i was doing something wrong, and that experience *was* several years ago.  if you are really interested in the failing test case, i can dig it up.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2018-03-18 19:23:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374037009  

In case anyone would like to give it a try... the branch "complex" has hopefully now complete support for complex numbers via mpc, see https://github.com/boostorg/multiprecision/tree/complex.  
  
The usual typedefs are:  
  
```  
using boost::multiprecision::backends::mpc_complex_backend;  
  
typedef number<mpc_complex_backend<50> >    mpc_complex_50;  
typedef number<mpc_complex_backend<100> >   mpc_complex_100;  
typedef number<mpc_complex_backend<500> >   mpc_complex_500;  
typedef number<mpc_complex_backend<1000> >  mpc_complex_1000;  
typedef number<mpc_complex_backend<0> >     mpc_complex;  
```

---

## Comment 13

> Username: ofloveandhate  
> Created at: 2018-03-19 16:11:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374268905  

i have boost 1.65 & 1.66 installed from homebrew on my machine i am testing with, and i am getting an include error when trying to use my clone of this repo (on complex branch):  
  
```  
In file included from src/basics/mpfr_extensions.cpp:33:  
In file included from ./include/bertini2/mpfr_extensions.hpp:38:  
In file included from /Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/mpfr.hpp:9:  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:25:10: fatal error:   
      'boost/container_hash/hash.hpp' file not found  
#include <boost/container_hash/hash.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
poking around in my installed boost, i do not have `boost/container_hash`.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2018-03-19 16:35:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374277674  

>poking around in my installed boost, i do not have boost/container_hash.  
  
Darn, it's been moved in current Boost develop, you can grab a copy from https://github.com/boostorg/container_hash

---

## Comment 15

> Username: ofloveandhate  
> Created at: 2018-03-19 16:41:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374279844  

thanks, got it off the ground now.

---

## Comment 16

> Username: ofloveandhate  
> Created at: 2018-03-19 17:21:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374295112  

i think i got my first error that wasn't my fault:  
  
```  
./include/bertini2/eigen_extensions.hpp:168:13: error: no matching constructor for initialization of 'Scalar' (aka  
      'number<mpc_complex_backend<0>, bmp::et_on>')  
  ...Scalar(numext::real(x)*numext::real(y) + numext::imag(x)*numext::imag(y), numext::imag(x)*numext::real(y) - numext::real(x)*numext::imag(y)...  
     ^      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:83:50: note:   
      candidate constructor [with V = boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>] not viable: no known conversion from  
      'detail::expression<detail::minus, detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'typename boost::enable_if_c<detail::is_explicitly_convertible<typename detail::canonical<expression<plus,  
      expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value && (detail::is_restricted_conversion<typename  
      detail::canonical<expression<plus, expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value || !is_convertible<typename  
      detail::canonical<expression<plus, expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value)>::type *' (aka 'void *') for 2nd argument  
   explicit BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const V& v, typename boost::enable_if_c<  
                                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:193:4: note:   
      candidate constructor [with tag = boost::multiprecision::detail::plus, Arg1 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg2 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg3 = void, Arg4 = void] not viable: no known conversion  
      from 'detail::expression<detail::minus, detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'typename boost::enable_if_c<is_convertible<typename detail::expression<plus, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>,  
      expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>::result_type, self_type>::value>::type *'  
      (aka 'void *') for 2nd argument  
   number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e, typename boost::enable_if_c<is_convertible<typename detail::...  
   ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:69:41: note:   
      candidate constructor not viable: no known conversion from 'detail::expression<detail::minus,  
      detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'unsigned int' for 2nd argument  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number& e, unsigned digits10)   
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:131:25: note:   
      candidate template ignored: deduced conflicting types for parameter 'V' ('expression<boost::multiprecision::detail::plus,  
      [4 * ...]>' vs. 'expression<boost::multiprecision::detail::minus, [4 * ...]>')  
   BOOST_MP_FORCEINLINE number(V v1, V v2, typename boost::enable_if<mpl::or_<boost::is_arithmetic<V>, is_same<std::string, V>, ...  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:49:25: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE number(const V& v, typename boost::enable_if_c<  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:61:41: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const V& v, typename boost::enable_if_c<  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:73:34: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   explicit BOOST_MP_FORCEINLINE number(const V& v, typename boost::enable_if_c<  
                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:107:25: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   BOOST_MP_FORCEINLINE number(const number<Other, ET>& val,  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:113:13: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   explicit number(const number<Other, ET>& val, typename boost::enable_if_c<  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:124:34: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   explicit BOOST_MP_FORCEINLINE number(const number<Other, ET>& val, typename boost::enable_if_c<  
                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:137:25: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   BOOST_MP_FORCEINLINE number(const number<Other, ET>& v1, const number<Other, ET>& v2, typename boost::enable_if<boost::is_con...  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:198:13: note:   
      candidate template ignored: substitution failure [with tag = boost::multiprecision::detail::plus, Arg1 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg2 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg3 = void, Arg4 = void]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   explicit number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e,  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:103:41: note:   
      candidate constructor template not viable: requires single argument 'val', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number<Backend, ET>& val)  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:47:41: note:   
      candidate constructor not viable: requires single argument 'e', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number& e) BOOST_MP_NOEXCEPT_IF(noexcept(Backend(std::declval<Backend const...  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:206:41: note:   
      candidate constructor not viable: requires single argument 'r', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(number&& r)  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:46:41: note:   
      candidate constructor not viable: requires 0 arguments, but 2 were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number() BOOST_MP_NOEXCEPT_IF(noexcept(Backend())) {}  
                                        ^  
  
```  
  
that is, is there a complex constructor that takes two real expressions?  or, is it the case that the real() and imag() getters are returning complexes instead of reals?  
  
based on this:  
```  
typename scalar_result_from_possible_complex<number<Backend, ExpressionTemplates> >::type  
      real()const  
   {  
      typename scalar_result_from_possible_complex<multiprecision::number<Backend, ExpressionTemplates> >::type result;  
      eval_real(result.backend(), backend());  
      return result;  
   }  
```  
 it appears that real() and imag() give complex, not real values.

---

## Comment 17

> Username: ofloveandhate  
> Created at: 2018-03-19 17:35:23 UTC  
> Updated at: 2018-03-19 17:39:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374299906  

the numext::real(...) function is defined in my version of Eigen (3.3.4 from homebrew) in `MathFunctions.h` at line 873 thusly:  
  
```  
template<typename Scalar>  
EIGEN_DEVICE_FUNC  
inline EIGEN_MATHFUNC_RETVAL(real, Scalar) real(const Scalar& x)  
{  
  return EIGEN_MATHFUNC_IMPL(real, Scalar)::run(x);  
}  
```  
  
that is, i believe that Eigen is not mangling the reality of the number it is returning (though i don't know whether it is returning a copy, possibly causing precision confusion -- there *is* a different function for returning by ref). i haven't caught a real-complex mismatch in eigen for a while.  keeping an open mind about their absence of real-complex bugs -- it has been a while since i caught one.

---

## Comment 18

> Username: ofloveandhate  
> Created at: 2018-03-19 18:47:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374323651  

tangentially, i also want to ensure that the `real` and `imag` getters return a const-reference, not a copy, so that if the current default precision differs from that of the number, it returns the actual value, not a copy of questionable precision.  looking at number.hpp:713, i don't know.  i guess it is an expression, not a value, being returned from `real`, right?

---

## Comment 19

> Username: jzmaddock  
> Created at: 2018-03-19 19:11:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374332775  

Do you have a simple example I can try and compile?  Note that I've done nothing to support Eigen interoperability so far, and I'm pretty sure that the default version of Eigen::NumTraits will not be correct.  
  
To answer some specific questions:   
  
1) The return values from real and imag are scalar types (mpfr_float) not complex ones.  
2) The values are returned by value not const-ref - there's no means of returning a const ref since we store an mpc_t and not a pair of mpfr_float's.  I need to double check that copying mpfr/mpc types results in precision preservation (irrespective of whatever the default is), but they certainly should do so...

---

## Comment 20

> Username: ofloveandhate  
> Created at: 2018-03-19 19:26:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374338404  

it'll take me a bit to make a minimal example.  thanks for your reply.

---

## Comment 21

> Username: jzmaddock  
> Created at: 2018-03-19 19:27:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374338713  

Any complex number examples in the Eigen docs?

---

## Comment 22

> Username: ofloveandhate  
> Created at: 2018-03-19 19:35:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374341602  

yes, there are some, for example, for doing a complex Schur decomposition:  
  
http://eigen.tuxfamily.org/dox/classEigen_1_1ComplexSchur.html  
  
however, as you say, there are some things needed to make custom types usable with eigen -- i am checking if i can just drop in `mpc` to my current extensions that allowed my custom multiprecision complex to be used in Eigen.  the error above was the first "no" answer i ran into.    
  
that is, i don't see any examples straight away that we could use.  the schur example requires random number generation to be defined with the custom type.  
  
my current NumTraits that work for my custom complex are at https://github.com/bertiniteam/b2/blob/develop/core/include/bertini2/eigen_extensions.hpp

---

## Comment 23

> Username: ofloveandhate  
> Created at: 2018-03-19 19:38:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374342764  

one thing i just noticed is that in my code, i provide a NumTraits for expressions of numbers:  
  
```  
	template<typename Expr1,typename Expr2, typename Expr3>  
	struct NumTraits<  
		boost::multiprecision::detail::expression<Expr1,  
      	Expr2, Expr3, void, void>  
      		> : NumTraits<mpfr_real> // permits to get the epsilon, dummy_precision, lowest, highest functions  
	{  
  
		typedef mpfr_real Real;  
		typedef mpfr_real NonInteger;  
		typedef mpfr_real Nested;  
	};  
```  
  
but it's only for reals.  so, i at least need one for complexes.  now, to make it co-vary with the scalar type...

---

## Comment 24

> Username: ofloveandhate  
> Created at: 2018-03-19 19:51:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374347652  

is this the constructor you think should be used when constructing a complex from two expressions of reals?  
  
```  
template <class V>  
   BOOST_MP_FORCEINLINE number(V v1, V v2, typename boost::enable_if<mpl::or_<boost::is_arithmetic<V>, is_same<std::string, V>, is_convertible<V, const char*> > >::type* = 0)  
   {  
      using default_ops::assign_components;  
      assign_components(m_backend, canonical_value(v1), canonical_value(v2));  
   }  
```

---

## Comment 25

> Username: jzmaddock  
> Created at: 2018-03-19 20:05:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374353241  

Yes that's the one.  
  
Just started with the first Eigen examples, here's a first cut at the traits class, no doubt still needing refinement:  
  
```  
namespace Eigen  
{  
   template <class Backend, boost::multiprecision::expression_template_option ExpressionTemplates>  
   struct NumTraits<boost::multiprecision::number<Backend, ExpressionTemplates> >  
   {  
      typedef boost::multiprecision::number<Backend, ExpressionTemplates> self_type;  
      typedef typename boost::multiprecision::scalar_result_from_possible_complex<self_type>::type Real;  
      typedef self_type NonInteger; // Not correct but we can't do much better??  
      typedef double Literal;  
      typedef self_type Nested;  
      enum {  
         IsComplex = boost::multiprecision::number_category<self_type>::value == boost::multiprecision::number_kind_complex,  
         IsInteger = boost::multiprecision::number_category<self_type>::value == boost::multiprecision::number_kind_integer,  
         ReadCost = 1,  
         AddCost = 4,  
         MulCost = 8,  
         IsSigned = std::numeric_limits<self_type>::is_specialized ? std::numeric_limits<self_type>::is_signed : true,  
         RequireInitialization = 1,  
      };  
      static Real epsilon() { return std::numeric_limits<Real>::epsilon(); }  
      static Real dummy_precision() { return epsilon(); }  
      static Real highest() { return (std::numeric_limits<Real>::max)(); }  
      static Real lowest() { return (std::numeric_limits<Real>::min)(); }  
      static int digits10() { return std::numeric_limits<Real>::digits10; }  
   };  
}  
```

---

## Comment 26

> Username: ofloveandhate  
> Created at: 2018-03-19 21:26:35 UTC  
> Updated at: 2018-03-19 21:36:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374384491  

i feel like these are more correct, since the precision varies at runtime (possibly)  
  
```  
inline static Real highest() {  
	return (mpfr_real(1) - epsilon()) * pow(mpfr_real(2),mpfr_get_emax()-1);  
}  
  
inline static Real lowest() {  
	return -highest();  
}  
  
inline static Real dummy_precision()  
{  
	using default_precision;  
	return pow( mpfr_real(10),-int(default_precision()-3));  
}  
  
inline static Real epsilon()  
{  
	using default_precision;  
	return pow(mpfr_real(10),-int(default_precision()));  
}  
  
static inline int digits10()  
{  
	return default_precision();  
}  
```

---

## Comment 27

> Username: ofloveandhate  
> Created at: 2018-03-19 21:29:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374385544  

is there a problem with that constructor in that it expects the same expression for each argument?

---

## Comment 28

> Username: ofloveandhate  
> Created at: 2018-03-19 21:35:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374387828  

you may also need some of these for eigen interoperability:  
  
```  
namespace Eigen {  
namespace internal {  
template<>  
struct abs2_impl<mpfr_complex>  
{  
	static inline mpfr_real run(const mpfr_complex& x)  
	{  
		return real(x)*real(x) + imag(x)*imag(x);  
	}  
};  
  
  
template<> inline mpfr_complex random<mpfr_complex>()  
{  
	return bertini::multiprecision::rand();  
}  
  
template<> inline mpfr_complex random<mpfr_complex>(const mpfr_complex& a, const mpfr_complex& b)  
{  
	return a + (b-a) * random<mpfr_complex>();  
}  
  
template<>  
struct conj_helper<mpfr_complex, mpfr_complex, false, true>  
{  
	typedef mpfr_complex Scalar;  
	EIGEN_STRONG_INLINE Scalar pmadd(const Scalar& x, const Scalar& y, const Scalar& c) const  
	{ return c + pmul(x,y); }  
  
	EIGEN_STRONG_INLINE Scalar pmul(const Scalar& x, const Scalar& y) const  
	{ return Scalar(real(x)*real(y) + imag(x)*imag(y), imag(x)*real(y) - real(x)*imag(y)); }  
};  
  
template<>  
struct conj_helper<mpfr_complex, mpfr_complex, true, false>  
{  
	typedef mpfr_complex Scalar;  
	EIGEN_STRONG_INLINE Scalar pmadd(const Scalar& x, const Scalar& y, const Scalar& c) const  
	{ return c + pmul(x,y); }  
  
	EIGEN_STRONG_INLINE Scalar pmul(const Scalar& x, const Scalar& y) const  
	{ return Scalar(real(x)*real(y) + imag(x)*imag(y), real(x)*imag(y) - imag(x)*real(y)); }  
};  
  
// see https://forum.kde.org/viewtopic.php?f=74&t=111176  
  
// specializations to allow mixed arithmetic in Eigen  
//int  
template<>  
struct scalar_product_traits<int,mpfr_complex>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
template<>  
struct scalar_product_traits<mpfr_complex, int>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
//long  
template<>  
struct scalar_product_traits<long,mpfr_complex>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
template<>  
struct scalar_product_traits<mpfr_complex, long>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
//long long  
template<>  
struct scalar_product_traits<long long,mpfr_complex>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
template<>  
struct scalar_product_traits<mpfr_complex, long long>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
  
//mpfr_real  
template<>  
struct scalar_product_traits<mpfr_real,mpfr_complex>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
template<>  
struct scalar_product_traits<mpfr_complex, mpfr_real>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
//mpz_int  
template<>  
struct scalar_product_traits<mpz_int,mpfr_complex>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
template<>  
struct scalar_product_traits<mpfr_complex, mpz_int>  
{  
	enum { Defined = 1 };  
	typedef mpfr_complex ReturnType;  
};  
  
} // re: namespace internal  
} // re: namespace Eigen  
```  
where going down the random number generation part opens yet another can of worms.

---

## Comment 29

> Username: ofloveandhate  
> Created at: 2018-03-20 15:04:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374631707  

i was just playing around with the error messages, and added two bits of code to the system in an effort to get past this error.  
  
1 -- number.hpp  
  
```  
   template <class V, class W>  
   BOOST_MP_FORCEINLINE number(V v1, W v2,   
      typename boost::enable_if<  
                           mpl::and_<  
                              mpl::or_<  
                                 boost::is_arithmetic<V>,   
                                 is_same<std::string, V>,   
                                 is_convertible<V, const char*>   
                                 >,  
                              mpl::or_<  
                                 boost::is_arithmetic<W>,   
                                 is_same<std::string, W>,   
                                 is_convertible<W, const char*>   
                                 >  
                              > // and_  
                              >::type* = 0)  
   {  
      using default_ops::assign_components;  
      assign_components(m_backend, canonical_value(v1), canonical_value(v2));  
   }  
```  
  
2. default_ops.hpp  
  
```  
template <class T, class V1, class V2>   
inline void assign_components(T& result, const V1& v1, const V2& v2)  
{  
   return assign_components_imp(result, v1, v2, typename number_category<T>::type());  
}  
```  
  
at least gets past the conficting template type error coming from the singly-templated constructor i referred to above:  
  
```  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:131:25: note:   
      candidate template ignored: deduced conflicting types for parameter 'V' ('expression<boost::multiprecision::detail::plus,  
      [4 * ...]>' vs. 'expression<boost::multiprecision::detail::minus, [4 * ...]>')  
   BOOST_MP_FORCEINLINE number(V v1, V v2, typename boost::enable_if<mpl::or_<boost::is_arithmetic<V>, is_same<std::string, V>, ...  
```  
  
however, the compiler is claiming the two-parameter constructor i added was disabled:  
  
```  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:141:28: note:   
      candidate template ignored: disabled by 'enable_if' [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>, W =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]  
                           mpl::and_<  
```  
  
relaxing the conditional, the `or_` is failing for both `V` and `W`.    
  
replacing my test constructor with  
```  
template <class V1, class V2>  
   BOOST_MP_FORCEINLINE number(V1 v1, V2 v2,   
      typename boost::enable_if<  
                           mpl::and_<  
                              mpl::or_<  
                                 boost::is_arithmetic<V1>,   
                                 is_number_expression<V1>,   
                                 is_same<std::string, V1>,   
                                 is_convertible<V1, const char*>   
                                 >,  
                              mpl::or_<  
                                 boost::is_arithmetic<V2>,   
                                 is_number_expression<V2>,   
                                 is_same<std::string, V2>,   
                                 is_convertible<V2, const char*>   
                                 >  
                              >  
                              >::type* = 0)  
   {  
      using default_ops::assign_components;  
      assign_components(m_backend, canonical_value(v1), canonical_value(v2));  
   }  
```  
  
eliminated the error and let me move on to other errors that are clearly my fault, though i am only through like 5% of the compile process.  (i am trying to *just use* the new type in my project, not building up to it)  
  
is it nonsense to use `is_number_expression` in this context?

---

## Comment 30

> Username: ofloveandhate  
> Created at: 2018-03-20 15:10:04 UTC  
> Updated at: 2018-03-20 15:16:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374633708  

may we also please have `real(...)` and `imag(...)` setters, in addition to getters?    
```  
./include/bertini2/function_tree/symbols/linear_product.hpp:105:36: error: too many arguments to function call, expected 0, have 1  
                                                coeffs_mpfr_ref(ii,jj).real( static_cast<mpfr_float>(coeffs_rat_real_(ii,jj)) );  
                                                ~~~~~~~~~~~~~~~~~~~~~~~~~~~  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:739:4: note: 'real'  
      declared here  
   typename scalar_result_from_possible_complex<number<Backend, ExpressionTemplates> >::type  
   ^  
In file included from src/function_tree/node.cpp:27:  
In file included from ./include/bertini2/function_tree.hpp:42:  
./include/bertini2/function_tree/symbols/linear_product.hpp:106:36: error: too many arguments to function call, expected 0, have 1  
                                                coeffs_mpfr_ref(ii,jj).imag( static_cast<mpfr_float>(coeffs_rat_imag_(ii,jj)) );  
                                                ~~~~~~~~~~~~~~~~~~~~~~~~~~~  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:746:4: note: 'imag'  
      declared here  
   typename scalar_result_from_possible_complex<number<Backend, ExpressionTemplates> >::type  
   ^  
  
```  
i am still not 100% with the internal yoga for this implementation, so perhaps need a bit of coaching to implement them myself.

---

## Comment 31

> Username: ofloveandhate  
> Created at: 2018-03-20 15:32:03 UTC  
> Updated at: 2018-03-20 15:36:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374641767  

looks like i declared victory a bit early on the constructor i added.  getting further in the compile process revealed this new error:  
  
```  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/default_ops.hpp:836:11: error:   
      no matching function for call to 'assign_components_imp'  
   return assign_components_imp(result, v1, v2, typename number_category<T>::type());  
          ^~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:134:7: note: in  
      instantiation of function template specialization  
      'boost::multiprecision::default_ops::assign_components<boost::multiprecision::backends::mpc_complex_backend<0>, const char *>'  
      requested here  
      assign_components(m_backend, canonical_value(v1), canonical_value(v2));  
      ^  
./include/bertini2/num_traits.hpp:281:11: note: in instantiation of function template specialization  
      'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>,  
      boost::multiprecision::expression_template_option::et_on>::number<std::__1::basic_string<char> >' requested here  
                        return mpfr_complex(s,t);  
                               ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/default_ops.hpp:825:13: note:   
      candidate function not viable: no known conversion from 'int_<4>' to 'const int_<number_kind_rational aka 2>' for 4th  
      argument  
inline void assign_components_imp(T& result, const V& v1, const V& v2, const mpl::int_<number_kind_rational>&)  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/default_ops.hpp:842:11: error:   
      no matching function for call to 'assign_components_imp'  
   return assign_components_imp(result, v1, v2, typename number_category<T>::type());  
          ^~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:158:7: note: in  
      instantiation of function template specialization  
      'boost::multiprecision::default_ops::assign_components<boost::multiprecision::backends::mpc_complex_backend<0>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void> >' requested here  
      assign_components(m_backend, canonical_value(v1), canonical_value(v2));  
      ^  
./include/bertini2/eigen_extensions.hpp:168:13: note: in instantiation of function template specialization  
      'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>,  
      boost::multiprecision::expression_template_option::et_on>::number<boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void> >' requested here  
                        { return Scalar(real(x)*real(y) + imag(x)*imag(y), imag(x)*real(y) - real(x)*imag(y)); }  
                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/default_ops.hpp:825:13: note:   
      candidate template ignored: deduced conflicting types for parameter 'V' ('expression<boost::multiprecision::detail::plus,  
      [4 * ...]>' vs. 'expression<boost::multiprecision::detail::minus, [4 * ...]>')  
inline void assign_components_imp(T& result, const V& v1, const V& v2, const mpl::int_<number_kind_rational>&)  
            ^  
```  
  
the first error immediately above comes from the following code:  
```  
inline static   
		mpfr_complex FromString(std::string const& s, std::string const& t)  
		{  
			return mpfr_complex(s,t);  
		}  
```  
  
in sum, i think we still need `assign_components_imp` for complex numbers, with expression and string inputs for the real and complex components.

---

## Comment 32

> Username: jzmaddock  
> Created at: 2018-03-20 18:02:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374698514  

Hi Danielle, I'm a bit behind you on this - what I'm trying to do at present is work through the Eigen examples in the docs and gradually add everything that needs adding (mostly Eigen traits classes so far).  
  
With regard to the 2-arg constructor, yes it should be possible to make it work with 2 different args and/or 2 args which are not the same as the component_type, but a 2-arg string constructor would be `explicit` just like the single argument one.

---

## Comment 33

> Username: ofloveandhate  
> Created at: 2018-03-21 15:32:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-374980741  

Thanks so much for your work on this!!!    
  
I have a moderate amount of test code for Eigen compatibility that i'd like to share, to help speed your process.  LU and SVD are notably tested (at least as far as compilability) in this code.   
[eigen_test.cpp.zip](https://github.com/boostorg/multiprecision/files/1833923/eigen_test.cpp.zip)  
  
* includes some helper functions at the top of the test file, things like checks on LU decomposition, etc, that are used in my codebase, but i thought they might be helpful  
* is missing some stuff related to random number generation.  i leave that to some other blob of code...  
* there's some double precision stuff in the file i shared, but i consciously left it in so that if analogous tests were desired for multiprecision types, a model is there.  
* the tests i attached assume a free function returning the precision of an arbitrary numeric object, in digits (double is labeled as 16)  
* could probably be made far more generic, to operate on arbitrary numeric types, and all tests that work on a specific numeric type made just one, then typedef'd to something specific up top.  that way things like the various fixed extended precision types could be used.  
  
hope this helps.  trying to get stuff like this done this week while on spring break!

---

## Comment 34

> Username: jzmaddock  
> Created at: 2018-03-21 20:19:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-375082055  

I've pushed a partial fix for the 2-arg constructor issue, also first cut at an Eigen test suite (at least one of the tests is generating garbage results though).  See: https://github.com/boostorg/multiprecision/commit/1a32ade24c343a74d6faa27e23f81b8821e3aab2

---

## Comment 35

> Username: ofloveandhate  
> Created at: 2018-03-21 21:54:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-375108612  

still getting an error on the two-expression constructor:  
  
```  
./include/bertini2/eigen_extensions.hpp:168:13: error: no matching constructor for initialization of 'Scalar' (aka  
      'number<mpc_complex_backend<0>, bmp::et_on>')  
                        { return Scalar(real(x)*real(y) + imag(x)*imag(y), imag(x)*real(y) - real(x)*imag(y)); }  
                                 ^      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:83:50: note:   
      candidate constructor [with V = boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>] not viable: no known conversion from  
      'detail::expression<detail::minus, detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'typename boost::enable_if_c<detail::is_explicitly_convertible<typename detail::canonical<expression<plus,  
      expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value && (detail::is_restricted_conversion<typename  
      detail::canonical<expression<plus, expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value || !is_convertible<typename  
      detail::canonical<expression<plus, expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>, void,  
      void>, mpc_complex_backend<0> >::type, mpc_complex_backend<0> >::value)>::type *' (aka 'void *') for 2nd argument  
   explicit BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const V& v, typename boost::enable_if_c<  
                                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:202:4: note:   
      candidate constructor [with tag = boost::multiprecision::detail::plus, Arg1 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg2 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg3 = void, Arg4 = void] not viable: no known conversion  
      from 'detail::expression<detail::minus, detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'typename boost::enable_if_c<is_convertible<typename detail::expression<plus, expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void>,  
      expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>::result_type, self_type>::value>::type *'  
      (aka 'void *') for 2nd argument  
   number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e, typename boost::enable_if_c<is_convertible<typename detail::...  
   ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:69:41: note:   
      candidate constructor not viable: no known conversion from 'detail::expression<detail::minus,  
      detail::expression<multiply_immediates, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, number<mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, detail::expression<multiply_immediates,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>,  
      number<mpfr_float_backend<0, allocate_dynamic>, boost::multiprecision::expression_template_option::et_on>, void, void> >' to  
      'unsigned int' for 2nd argument  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number& e, unsigned digits10)   
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:49:25: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE number(const V& v, typename boost::enable_if_c<  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:61:41: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const V& v, typename boost::enable_if_c<  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:73:34: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   explicit BOOST_MP_FORCEINLINE number(const V& v, typename boost::enable_if_c<  
                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:107:25: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   BOOST_MP_FORCEINLINE number(const number<Other, ET>& val,  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:113:13: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   explicit number(const number<Other, ET>& val, typename boost::enable_if_c<  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:124:34: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   explicit BOOST_MP_FORCEINLINE number(const number<Other, ET>& val, typename boost::enable_if_c<  
                                 ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:131:25: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>, U =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE number(const V& v1, const U& v2,   
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:138:25: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>, U =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE number(const V& v1, const U& v2,  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:146:25: note:   
      candidate template ignored: could not match 'number' against 'expression'  
   BOOST_MP_FORCEINLINE number(const number<Other, ET>& v1, const number<Other, ET>& v2, typename boost::enable_if<boost::is_con...  
                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:207:13: note:   
      candidate template ignored: substitution failure [with tag = boost::multiprecision::detail::plus, Arg1 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg2 =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, Arg3 = void, Arg4 = void]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   explicit number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e,  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:103:41: note:   
      candidate constructor template not viable: requires single argument 'val', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number<Backend, ET>& val)  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:47:41: note:   
      candidate constructor not viable: requires single argument 'e', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const number& e) BOOST_MP_NOEXCEPT_IF(noexcept(Backend(std::declval<Backend const...  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:215:41: note:   
      candidate constructor not viable: requires single argument 'r', but 2 arguments were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(number&& r)  
                                        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:46:41: note:   
      candidate constructor not viable: requires 0 arguments, but 2 were provided  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number() BOOST_MP_NOEXCEPT_IF(noexcept(Backend())) {}  
                                        ^  
```  
  
where the one that i think is supposed to work is the constructor at number.hpp:131:  
  
```  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/number.hpp:131:25: note:   
      candidate template ignored: substitution failure [with V =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::plus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>, U =  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::minus,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<0, allocate_dynamic>,  
      boost::multiprecision::expression_template_option::et_on>, void, void>, void, void>]: no type named 'type' in  
      'boost::enable_if_c<false, void>'  
   BOOST_MP_FORCEINLINE number(const V& v1, const U& v2,   
                        ^  
```  
  
you may have already known that, since you indicated it is a partial fix.    
  
what version of eigen are you testing against?  i've been at 3.3.4 for a while, supplied by brew.  happy to upgrade to match you.  happy to stay where i am.  i'll not go back to 3.2.x -- there are some bugs fixed going to 3.3.x.

---

## Comment 36

> Username: jzmaddock  
> Created at: 2018-03-25 18:34:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-375992589  

OK, latest code in the "complex" branch should have fixed those errors.  I have most of the Eigen examples building, plus a modified version of your test suite (attached).  
  
What isn't working, is scalar operations on related types:  
  
```  
my_matrix *= some_related_type;  
```  
  
where `some_related_type` is would normally interoperate with the the matrix's value_type: for example multiplying a matrix of mpfr_foat's by an mpz_int.  
The issue is that Eigen has it's own partial specialisation of `ScalarBinaryOpTraits` for complex types, and I've been unable to find other partial specializations that don't generate ambiguities with that.  Well actually, I had it all working with msvc, but gcc and clang rejected the code :(  
Currently your mpz_int interoperability tests are commented out for that reason.  Strangely msvc tries to instantiate `ScalarBinaryOpTraits<mpz_int, float, Op>` if you uncomment that code - and that static_asserts (with good reason).  Even stranger, gcc does seem to accept the code, but it really shouldn't!  
[eigen_test.zip](https://github.com/boostorg/multiprecision/files/1845635/eigen_test.zip)

---

## Comment 37

> Username: jzmaddock  
> Created at: 2018-03-25 19:02:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-375994644  

I take back the comment on matrixes of mpc_complex not interoperating with mpz_int: they do, since the complex type is implicitly constructed from the mpz_int.  I don't know why msvc triggers the static asserts, it's extremely annoying as it has no business instantiating those templates at all (and the instantiation backtrace is useless).  
  
Eigen could actually be a bit more efficient in this context as it's creating a copy of the scalar multiplier rather than passing the actual type of the multiplier down to the inner loop.  Not that it matters in this context...

---

## Comment 38

> Username: ofloveandhate  
> Created at: 2018-03-25 20:55:59 UTC  
> Updated at: 2018-03-25 20:56:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376002724  

see [this so post](https://stackoverflow.com/questions/43477416/custom-type-matrix-int-in-eigen) for a question i asked related to `*=`

---

## Comment 39

> Username: jzmaddock  
> Created at: 2018-03-26 12:47:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376153472  

>see this so post for a question i asked related to *=  
  
Actually I think it's all working with latest Eigen and the code I posted above, this all works OK:  
  
```  
      Eigen::Matrix<boost::multiprecision::mpc_complex, 3, 1> mat, mat2;  
      mat << 1, 2, 3;  
      mat *= 2;  
      mat *= 3.0f;  
  
      boost::multiprecision::mpc_complex com(2, 3);  
      mat *= com;  
      mat *= real(com);  
  
      boost::multiprecision::mpz_int z(56);  
      mat /= z;  
  
      Eigen::Matrix<boost::multiprecision::cpp_bin_float_50, 4, 1> cpp_mat, cpp_mat2;  
      cpp_mat *= 2;  
      cpp_mat *= 3.0;  
      boost::multiprecision::cpp_bin_float_quad q(2.0);  
      cpp_mat *= q;  
  
      mat2 = mat * 2;  
      mat2 = 2 * mat;  
      mat2 = mat * 3.0L;  
      mat2 = 2.0 * mat;  
      mat2 = mat * com;  
      mat2 = com * mat;  
      mat2 = real(com) * mat;  
      mat2 = mat * real(com);  
      mat2 = mat * z;  
      mat2 = z * mat;  
  
      cpp_mat2 = cpp_mat * 2;  
      cpp_mat2 = 2 * cpp_mat;  
      cpp_mat2 = cpp_mat * 2.0;  
      cpp_mat2 = 2.0 * cpp_mat;  
      cpp_mat2 = cpp_mat * q;  
      cpp_mat2 = q * cpp_mat;  
```

---

## Comment 40

> Username: ofloveandhate  
> Created at: 2018-03-26 13:49:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376172371  

yep, things look good on the Eigen front!

---

## Comment 41

> Username: ofloveandhate  
> Created at: 2018-03-26 13:50:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376172579  

next thing that keeps me from compiling is a `real`/`imag` pair of setters

---

## Comment 42

> Username: jzmaddock  
> Created at: 2018-03-26 16:46:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376232976  

>next thing that keeps me from compiling is a real/imag pair of setters  
  
I'm conflicted about that:  it's not part of the std::complex interface, and there's no way to set just one part of an mpc_t anyway.  What's the use case?

---

## Comment 43

> Username: mglisse  
> Created at: 2018-03-26 17:27:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376246185  

> there's no way to set just one part of an mpc_t anyway  
  
mpc_realref and mpc_imagref?

---

## Comment 44

> Username: jzmaddock  
> Created at: 2018-03-26 17:36:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-376249029  

>mpc_realref and mpc_imagref?  
  
You got me there! :)  
  
Good catch.

---

## Comment 45

> Username: jzmaddock  
> Created at: 2018-03-31 10:36:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-377683562  

Getters and setters for real/imag now added: I'm hoping that's the coding done now, give or take...

---

## Comment 46

> Username: ofloveandhate  
> Created at: 2018-04-02 20:00:50 UTC  
> Updated at: 2018-04-02 20:01:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-378027310  

i got **much** further.  here's my first test that failed:  
  
test case:  
```  
auto exact_mpfr = pow(sqrt(xnum_mpc),anum_mpc);  
somethingsomething exact_mpfr.imag();  
```  
  
with error:   
```  
test/classes/function_tree_test.cpp:1301:55: error: no member named 'real' in 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function,  
      boost::multiprecision::detail::number_kind_complexpow_funct<boost::multiprecision::backends::mpc_complex_backend<0> >,  
      boost::multiprecision::detail::expression<boost::multiprecision::detail::function,  
      boost::multiprecision::detail::number_kind_complexsqrt_funct<boost::multiprecision::backends::mpc_complex_backend<0> >,  
      boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>, boost::multiprecision::expression_template_option::et_on>, void, void>,  
      boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>, boost::multiprecision::expression_template_option::et_on>, void>'  
        BOOST_CHECK(fabs(N->Eval<mpfr>().real() - exact_mpfr.real() ) < threshold_clearance_mp);  
```  
  
is this forbidden, and the result of an incorrect use of `auto` with ET-types running around?  should expressions of complexes have an `imag` getter?

---

## Comment 47

> Username: jzmaddock  
> Created at: 2018-04-05 10:12:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-378886927  

>is this forbidden, and the result of an incorrect use of auto with ET-types running around? should expressions of complexes have an imag getter?  
  
I think I'm going to say "don't do that".  
  
We do allow:  
  
`real(come-complex-expression)`  
  
However, the danger with allowing the member function is that you inadvertently write:  
  
```  
auto x = some-complex-expression;  
auto r = x.real();  
auto i = x.imag();  // Oops, "some-complex-expression" just got evaluated twice!  
```  
  
I might be able to improve the error messages somewhat though...

---

## Comment 48

> Username: ofloveandhate  
> Created at: 2018-04-05 22:45:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-379098841  

gotcha.  thanks.  almost done with the compiling stage for my first test executable for bertini2.  
  
next question is about `isnan` and `isinf` functions.  would you be into providing a pair of them?   here's my probably janky implementation so i could get further:  
```  
	inline   
	bool isnan(mpfr_complex const& num)  
	{  
		using boost::math::isnan;  
		if (isnan(num.real()) || isnan(num.imag()))  
			return true;  
		else  
			return false;  
	}  
```  
  
after this, it appears i have a few linker errors for undefined symbols, that will have to wait for a bit so i can catch up on some grading on which i am badly behind...  here's a preview:  
```  
Undefined symbols for architecture x86_64:  
  "_mpc_acos", referenced from:  
      boost::multiprecision::detail::number_kind_complexacos_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-function_tree_test.o  
      boost::multiprecision::detail::number_kind_complexacos_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-complex_test.o  
  "_mpc_acosh", referenced from:  
      boost::multiprecision::detail::number_kind_complexacosh_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-complex_test.o  
  "_mpc_add", referenced from:  
      void boost::multiprecision::backends::eval_add<0u, 0u, 0u>(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) in b2_class_test-eigen_test.o  
```  
i think i need to link to mpc, wherever that is coming from.

---

## Comment 49

> Username: ofloveandhate  
> Created at: 2018-04-06 13:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-379250427  

got it, of course i have to link with `mpc`.

---

## Comment 50

> Username: ckormanyos  
> Created at: 2018-04-08 21:18:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-379583430  

> i think i need to link to mpc, wherever that is coming from.  
Probably. But this might involve a few more steps.  
  
MPC means "Multiple Precision Complex". It is a library that abstractscomplex multiple precision numbers on top of several layers of additionalsoftware.  
If I recall, then MPC is based on MPFR, which is subsequently basedon GMP. So in fact, you might need all of these libraries in order tosuccessfully link the project.  
GMP provides real-valued multiple precicision numbers, but with justa few functions. MPFR adds additional precise rounging modes andeven more elementary functions on top of GMP. MPC wraps MPFRand provides the complex number type.  
Depending on your system, you can select (for the low-level real type)either GMP or MPIR.  
I have only followed your work indirectly, but it seems like you mightbe wrapping MPC in yet another layer that allows this complex typein Boost.Multiprecision so that it behaves very much like an STLcomplex type, but can be instantiated for user-defined types.  
During the early work of Boost.Multiprecision, John ensured thatthese types were properly wrapped as back end number typesso that the so-called "number" layer could be instantiated withGMP or MPFR types.  
With kind regards, Chris  
  
  
  
  
   
  
    On Friday, April 6, 2018, 12:45:50 AM GMT+2, danielle brake <notifications@github.com> wrote:    
   
   
gotcha. thanks. almost done with the compiling stage for my first test executable for bertini2.  
  
next question is about isnan and isinf functions. would you be into providing a pair of them? here's my probably janky implementation so i could get further:  
	inline   
	bool isnan(mpfr_complex const& num)  
	{  
		using boost::math::isnan;  
		if (isnan(num.real()) || isnan(num.imag()))  
			return true;  
		else  
			return false;  
	}  
  
after this, it appears i have a few linker errors for undefined symbols, that will have to wait for a bit so i can catch up on some grading on which i am badly behind... here's a preview:  
Undefined symbols for architecture x86_64:  
  "_mpc_acos", referenced from:  
      boost::multiprecision::detail::number_kind_complexacos_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-function_tree_test.o  
      boost::multiprecision::detail::number_kind_complexacos_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-complex_test.o  
  "_mpc_acosh", referenced from:  
      boost::multiprecision::detail::number_kind_complexacosh_funct<boost::multiprecision::backends::mpc_complex_backend<0u> >::operator()(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) const in b2_class_test-complex_test.o  
  "_mpc_add", referenced from:  
      void boost::multiprecision::backends::eval_add<0u, 0u, 0u>(boost::multiprecision::backends::mpc_complex_backend<0u>&, boost::multiprecision::backends::mpc_complex_backend<0u> const&, boost::multiprecision::backends::mpc_complex_backend<0u> const&) in b2_class_test-eigen_test.o  
  
i think i need to link to mpc, wherever that is coming from.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 51

> Username: ofloveandhate  
> Created at: 2018-04-08 21:39:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-379584847  

it did compile correctly after adding  
```  
#find mpc  
AC_SEARCH_LIBS([mpc_clear],[mpc], [],[  
  AC_MSG_ERROR([unable to find `mpc` for complex arithmetic. should have come with mpfr?])  
])  
```   
to my `configure.ac`.  now i have tons of failing tests, but no time to diagnose.  i doubt they are from an incorrectly linked library.  i will attempt to put in some time perhaps thursday.  things will be slow on this front for me 'til the end of the spring semester, as i am teaching a whopping 16 credits.

---

## Comment 52

> Username: jzmaddock  
> Created at: 2018-04-22 16:14:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-383392818  

Update:  
* Added `complex_adaptor` which turns any floating point backend into a complex number.  
* Added `cpp_complex` and `complex128` which make use of the above (the former based on cpp_bin_float, the latter on GCC's `__float128`).  
* Added docs for Eigen interop.  
* Merged everything to develop.

---

## Comment 53

> Username: ofloveandhate  
> Created at: 2018-04-23 13:03:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-383567229  

outstanding, thanks!  i still have some (many) failing tests, and do not know their solution.  it will, however, be mid-may before i can guarantee that i can finish this project.   don't let me hold anything up!

---

## Comment 54

> Username: jzmaddock  
> Created at: 2018-04-24 17:15:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-384010800  

I'm working on some improved tests now - so far just corner cases on the transcendental functions that are failing.  In the mean time if can produce test cases, please do file bug reports.

---

## Comment 55

> Username: NAThompson  
> Created at: 2018-04-25 02:03:39 UTC  
> Updated at: 2018-04-25 02:20:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-384137800  

Looks like `complex128` has a small problem. I've pushed it as a failing unit test in the boost.math `contour_integration` branch. The compilation error is:  
  
```  
boost/math$ make test.x  
g++-7 --std=c++11 -O3 -march=native -Wall -Wextra -Wfatal-errors -DBOOST_HAS_FLOAT128 -fext-numeric-literals -I./include -I../multiprecision/include -I../container_hash/include -I../atomic/include test/test_trapezoidal.cpp -o test.x -pthread -lquadmath -lm  
In file included from /usr/local/include/boost/integer.hpp:23:0,  
                 from /usr/local/include/boost/function/function_base.hpp:20,  
                 from /usr/local/include/boost/function/detail/prologue.hpp:17,  
                 from /usr/local/include/boost/function/function_template.hpp:13,  
                 from /usr/local/include/boost/function/detail/maybe_include.hpp:15,  
                 from /usr/local/include/boost/function/function0.hpp:11,  
                 from /usr/local/include/boost/test/execution_monitor.hpp:28,  
                 from /usr/local/include/boost/test/impl/compiler_log_formatter.ipp:22,  
                 from /usr/local/include/boost/test/included/unit_test.hpp:18,  
                 from test/test_trapezoidal.cpp:12:  
../multiprecision/include/boost/multiprecision/detail/number_base.hpp: In instantiation of 'struct boost::multiprecision::detail::bits_of<__float128>':  
../multiprecision/include/boost/multiprecision/detail/number_base.hpp:228:81:   required from 'struct boost::multiprecision::detail::canonical_imp<__float128, boost::multiprecision::backends::float128_backend, mpl_::int_<2> >'  
../multiprecision/include/boost/multiprecision/detail/number_base.hpp:266:65:   required from 'struct boost::multiprecision::detail::canonical<__float128, boost::multiprecision::backends::float128_backend>'  
../multiprecision/include/boost/multiprecision/number.hpp:51:16:   required by substitution of 'template<class V> boost::multiprecision::number<boost::multiprecision::backends::float128_backend, (boost::multiprecision::expression_template_option)0>::number(const V&, typename boost::enable_if_c<(((((boost::is_arithmetic<T>::value || boost::is_same<std::__cxx11::basic_string<char>, V>::value) || boost::is_convertible<Val, const char*>::value) && (! boost::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::float128_backend>::type, boost::multiprecision::backends::float128_backend>::value)) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::float128_backend>::type, boost::multiprecision::backends::float128_backend>::value)) && (! boost::is_same<V, __float128>::value)), void>::type*) [with V = __float128]'  
../multiprecision/include/boost/multiprecision/float128.hpp:658:42:   required from 'static std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, ExpressionTemplates> >::number_type std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, ExpressionTemplates> >::epsilon() [with boost::multiprecision::expression_template_option ExpressionTemplates = (boost::multiprecision::expression_template_option)0; std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::float128_backend, ExpressionTemplates> >::number_type = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, (boost::multiprecision::expression_template_option)0>]'  
test/test_trapezoidal.cpp:57:53:   required from 'void test_complex_bessel() [with Complex = boost::multiprecision::number<boost::multiprecision::backends::complex_adaptor<boost::multiprecision::backends::float128_backend>, (boost::multiprecision::expression_template_option)0>]'  
test/test_trapezoidal.cpp:285:60:   required from here  
../multiprecision/include/boost/multiprecision/detail/number_base.hpp:158:4: error: static assertion failed: is_integral<T>::value || is_enum<T>::value || std::numeric_limits<T>::is_specialized  
    BOOST_STATIC_ASSERT(is_integral<T>::value || is_enum<T>::value || std::numeric_limits<T>::is_specialized);  
    ^  
compilation terminated due to -Wfatal-errors.  
make: *** [test.x] Error 1  
```  
  
The test passes if the `static_assert` is removed from `number_base.hpp`, but of course this is a hamhanded fix.

---

## Comment 56

> Username: AuroraDysis  
> Created at: 2018-04-30 12:42:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-385387218  

awesome work, thank you very much. But how to convert between `mprf_float_backend` and `mpc_complex_backend`?

---

## Comment 57

> Username: NAThompson  
> Created at: 2018-04-30 13:20:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-385395903  

You mean via taking norms, real part or imaginary part?

---

## Comment 58

> Username: AuroraDysis  
> Created at: 2018-04-30 13:36:51 UTC  
> Updated at: 2018-04-30 13:37:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-385400032  

I mean `convert_to<>`, I need mix them, but `convert_to<complex::value_type>()` doesn't work now.  
for example use `number<mprf_float_backend<>>` to construct `number<mpc_complex_backend<>>` as real part

---

## Comment 59

> Username: jzmaddock  
> Created at: 2018-05-04 07:45:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-386527933  

It would help if you posted some code, but construction of complex from float types (one arg or two arg) should just work, as in:  
  
```  
mpfr_float_50 f(1);  
f /= 10;  
mpc_complex_50 c(f);   // real part only  
mpc_complex_50 c2(f, f *2);  // real and imag parts  
```

---

## Comment 60

> Username: jzmaddock  
> Created at: 2018-05-17 07:51:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-389777335  

>I mean convert_to<>, I need mix them, but convert_to<complex::value_type>() doesn't work now.  
for example use number<mprf_float_backend<>> to construct number<mpc_complex_backend<>> as real part  
  
Given:  
  
```  
   boost::multiprecision::mpc_complex_50 c50;  
   boost::multiprecision::mpfr_float_50 f50(2);  
```  
  
Then this works for me:  
  
```  
c50 = f50;  
```  
  
as does:  
  
```  
f50.convert_to<boost::multiprecision::mpc_complex_50>();  
```  
  
and:  
  
```  
c50.convert_to<boost::multiprecision::mpfr_float_50>();  
```

---

## Comment 61

> Username: AuroraDysis  
> Created at: 2018-05-17 16:47:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-389933539  

@jzmaddock thank you very much, I made a mistake. I have solved this problem. But there are some bugs when work together with mpfr_float and mpc_complex; together with eigen. After a lot of hard work, I finish my work. I will give some feedback if I'm free.   
  
For example  
Expression with pow(mpfr_float)  
Once I meet compilation failed, when I want to use a very long expression contains `pow(mpfr_float, 2) and mpc_complex`, I need to manually extract pow.  
  
With eigen3  
unaryexpr seems not compatible  
Eigen3 have many std::complex, but mpc_complex can't work well with std  
  
And so on.  
  
Finally, thanks for your awesome work.

---

## Comment 62

> Username: AuroraDysis  
> Created at: 2018-05-21 05:38:58 UTC  
> Updated at: 2018-05-21 07:35:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-390558075  

```cpp  
using complex_t = number<mpc_complex_backend<500>>;  
using scalar_t = complex_t::value_type;  
  
scalar_t b{ 1 };  
complex_t a{ 1,1 };  
complex_t c1 = pow(b, 2)*a; //compile error  
scalar_t b2 = pow(b, 2);  
complex_t c2 = b2 *a; //work fine  
```  
  
```log  
[ 33%] Building CXX object CMakeFiles/RNQNMdesitter.dir/main.cpp.o  
In file included from /var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/main.cpp:3:  
In file included from /var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/evolution.h:3:  
In file included from /var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/main.h:5:  
In file included from /var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/mpc.hpp:9:  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:1259:7: error: no matching function for call to object of type 'const boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >'  
      f(m_backend, function_arg_value(val1), function_arg_value(val2));  
      ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:1254:7: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::do_assign_function_2<boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int>' requested here  
      do_assign_function_2(e.left().value(), e.middle_ref(), e.right_ref(), tag_type(), end_tag());  
      ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:1139:7: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::do_assign_function<boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void> >' requested here  
      do_assign_function(e, tag_type());  
      ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:921:10: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::do_assign<boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void> >' requested here  
         do_assign(e.left(), typename left_type::tag_type());  
         ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:756:7: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::do_assign<boost::multiprecision::detail::expression<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void>, boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>, void, void> >' requested here  
      do_assign(e, tag());  
      ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:156:7: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::do_assign<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void>, boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>, void, void>' requested here  
      do_assign(e, tag_type());  
      ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/number.hpp:204:13: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::operator=<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void>, boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>, void, void>' requested here  
      *this = e;  
            ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/main.cpp:18:17: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>::number<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointpow_funct<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> >, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_on>, int, void>, boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<500>, boost::multiprecision::et_on>, void, void>' requested here  
        complex_t c1 = pow(b, 2)*a;  
                       ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3587:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::backends::mpc_complex_backend<500>' to 'boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> &' for 1st argument  
BINARY_OP_FUNCTOR(pow, number_kind_floating_point)  
^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3095:9: note: expanded from macro 'BINARY_OP_FUNCTOR'  
   void operator()(Backend& result, const Backend& arg, const Arithmetic& a)const\  
        ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3587:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::backends::mpc_complex_backend<500>' to 'boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> &' for 1st argument  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3089:9: note: expanded from macro 'BINARY_OP_FUNCTOR'  
   void operator()(Backend& result, const Backend& arg, const Backend& a)const\  
        ^  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3587:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::backends::mpc_complex_backend<500>' to 'boost::multiprecision::backends::mpfr_float_backend<500, boost::multiprecision::allocate_dynamic> &' for 1st argument  
/var/tmp/src/70a66999-9bf9-5936-a5d2-08b44f9f7350/Linux-Release/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:3101:9: note: expanded from macro 'BINARY_OP_FUNCTOR'  
   void operator()(Backend& result, const Arithmetic& arg, const Backend& a)const\  
        ^  
1 error generated.  
```

---

## Comment 63

> Username: jzmaddock  
> Created at: 2018-05-21 07:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-390573639  

```  
complex_t c = boost::math::pow(b)*a;  
```  
I'm not sure what you intended there, but that should either be `pow(b, a)` or `boost::math::pow<N>(a) * b;`

---

## Comment 64

> Username: AuroraDysis  
> Created at: 2018-05-21 07:38:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-390577838  

@jzmaddock Sorry, I have updated the original comment.

---

## Comment 65

> Username: jzmaddock  
> Created at: 2018-05-21 17:11:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-390719559  

Got it, I missed some function overloads.  A fix is in develop.  CI running now, but so far so good.

---

## Comment 66

> Username: ofloveandhate  
> Created at: 2018-07-26 18:30:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-408192312  

at this point (late july), should I be using the `complex` branch, or `develop`?

---

## Comment 67

> Username: jzmaddock  
> Created at: 2018-07-26 18:49:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-408197789  

develop or master, or even the beta of the next release.

---

## Comment 68

> Username: jtravs  
> Created at: 2018-08-02 05:14:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/41#issuecomment-409808070  

Thanks! This is extremely useful. Great work.
