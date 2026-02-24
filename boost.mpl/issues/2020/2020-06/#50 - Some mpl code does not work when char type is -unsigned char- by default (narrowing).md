# #50 - Some mpl code does not work when char type is "unsigned char" by default (narrowing) [Closed]

> Username: trex58  
> Created at: 2020-06-15 17:04:16 UTC  
> Updated at: 2025-01-02 15:31:33 UTC  
> Closed at: 2025-01-02 15:31:33 UTC  
> Url: https://github.com/boostorg/mpl/issues/50  

When compiling libs/mpl/test/char.cpp on AIX with GCC 9.3, where "char" type is "unsigned char" by default, I have the following error:  
```  
   "g++"  -fvisibility-inlines-hidden -fPIC -maix32 -pthread -mcmodel=large -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -O2 -ffp-contract=off   
   -ftls-model=initial-exec   -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DNDEBUG  -I".." -c  
   -o "../bin.v2/libs/mpl/test/char.test/gcc-9/release/threading-multi/visibility-hidden/char.o" "../libs/mpl/test/char.cpp"  
  
In file included from ../boost/mpl/aux_/test/assert.hpp:17,  
                from ../boost/mpl/aux_/test.hpp:19,  
                from ../libs/mpl/test/integral_wrapper_test.hpp:15,  
                from ../libs/mpl/test/char.cpp:17:  
../libs/mpl/test/char.cpp: In function 'void test20()':  
../libs/mpl/test/char.cpp:22:33: error: narrowing conversion of '-1' from 'int' to 'char' [-Wnarrowing]  
  22 | #   define WRAPPER(T, i) char_< i >  
     |                                   ^  
```  
Looking at details, the exact error is:  
```  
./libs/mpl/test/char.cpp.E.cpp: In function 'void test20()':  
../libs/mpl/test/char.cpp.E.cpp:30698:162: error: narrowing conversion of '-1' from 'int' to 'char' [-Wnarrowing]  
30698 | n_line_23 = sizeof( boost::mpl::assertion_failed<false>( boost::mpl::assert_arg( (void (*) ( is_same< prior< char_<0> >::type, char_<0 -1> > ))0, 1 ) ) ) }; }  
```  
where -1 is used for creating a char, which is "unsigned char" on AIX (and on other systems). Thus, creating a char with value -1 is forbidden.  
  
I've already seen this issue on other tests of Boost.  
The possible solutions are:  
 1) manage "char" so that it works either with char="signed char" or with char="unsigned char". However, since the C/C++ says that the "char" type is "undefined", that depends on how each OS has implemented the details of the char type (like converting a negatif floating point value to an "unsigned" integer/char/short).  
 2) replace "char" by "signed char" everywhere.  
 3) do not use negative value  
  
Second solution seems to be the more appropriate solution, since it will work everywhere. Unless using negative values is required.

---

## Comment 1

> Username: trex58  
> Created at: 2020-06-16 09:01:47 UTC  
> Url: https://github.com/boostorg/mpl/issues/50#issuecomment-644634924  

An easy solution would be to replace char by "signed char" in all the places where char appears:  
```  
   ../libs/mpl/test/char.cpp :   
     -    BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, char)  
     +    BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, signed char)  
  
   ../boost/mpl/char.hpp :  
     -  #define AUX_WRAPPER_VALUE_TYPE char  
     + #define AUX_WRAPPER_VALUE_TYPE signed char  
```  
But it does not work because the macros using AUX_WRAPPER_VALUE_TYPE  are expecting a SINGLE word, like:   
```  
   ../boost/preprocessor/cat.hpp:29  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
```  
So more changes are required. Like:  
```  
   ../libs/mpl/test/char.cpp :  
     -#   define WRAPPER(T, i) char_<i>  
     -    BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, char)  
     +#   define WRAPPER(T, i) signedchar  
     +    BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, signedchar)  
   
 ../boost/mpl/char.hpp :  
     -#define AUX_WRAPPER_VALUE_TYPE char  
     +#define AUX_WRAPPER_VALUE_TYPE signed char  
     +#define AUX_WRAPPER_VALUE_NAME signedchar  
   
   ../boost/mpl/aux_/integral_wrapper.hpp :  
     -#   define AUX_WRAPPER_NAME BOOST_PP_CAT(AUX_WRAPPER_VALUE_TYPE,_)  
     +#   define AUX_WRAPPER_NAME BOOST_PP_CAT(AUX_WRAPPER_VALUE_NAME,_)  
   
     -    BOOST_CONSTEXPR operator AUX_WRAPPER_VALUE_TYPE() const { return static_cast<AUX_WRAPPER_VALUE_TYPE>(this->value); }  
     +    BOOST_CONSTEXPR operator AUX_WRAPPER_VALUE_NAME() const { return static_cast<AUX_WRAPPER_VALUE_TYPE>(this->value); }  
```  
But there are still compilation errors. Like:  
```  
../boost/mpl/char.hpp:20:32: error: expected type-specifier before 'signedchar'  
   20 | #define AUX_WRAPPER_VALUE_NAME signedchar  
      |                                ^~~~~~~~~~  
../boost/mpl/aux_/integral_wrapper.hpp:80:30: note: in expansion of macro 'AUX_WRAPPER_VALUE_NAME'  
   80 |     BOOST_CONSTEXPR operator AUX_WRAPPER_VALUE_NAME() const { return static_cast<AUX_WRAPPER_VALUE_TYPE>(this->value); }  
      |                              ^~~~~~~~~~~~~~~~~~~~~~  
```  
I'm not an expert of this. Someone should have a look and provide a proposal so that "signed char" can be handled by this code.

---

## Comment 2

> Username: jeking3  
> Created at: 2022-05-01 11:50:49 UTC  
> Url: https://github.com/boostorg/mpl/issues/50#issuecomment-1114213635  

When fixing this issue please enable the bigendian CI job on GitHub Actions as part of the changes, as it fails because of this issue.
