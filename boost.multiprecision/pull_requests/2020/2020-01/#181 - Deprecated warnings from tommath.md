# #181 Deprecated warnings from tommath [Closed]

> Username: madhur4127  
> Created at: 2020-01-09 08:16:10 UTC  
> Updated at: 2020-03-09 17:02:46 UTC  
> Closed at: 2020-03-09 17:02:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/181  

I saw some "deprecated" warnings from tommath while running the tests.  
  
```  
../../../boost/multiprecision/tommath.hpp:228:13: warning: DIGIT_BIT macro is deprecated, MP_DIGIT_BIT instead  
  228 |             unsigned               block_count = DIGIT_BIT / shift;  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
../../../boost/multiprecision/tommath.hpp:457:13: warning: SIGN macro is deprecated, use z->sign instead  
  457 |    BOOST_MP_TOMMATH_BIT_OP_CHECK(result);  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
../../../boost/multiprecision/tommath.hpp:572:13: warning: SIGN macro is deprecated, use z->sign instead  
  572 |    return mp_iszero(&val.data()) ? 0 : SIGN(&val.data()) ? -1 : 1;  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
../../../boost/multiprecision/tommath.hpp:651:13: warning: DIGIT_BIT macro is deprecated, MP_DIGIT_BIT instead  
  651 |    static const mp_digit m = (static_cast<mp_digit>(1) << DIGIT_BIT) - 1;  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
../../../boost/multiprecision/tommath.hpp:155:58: warning: ‘mp_err mp_set_int(mp_int*, long unsigned int)’ is deprecated: replaced by mp_set_ul [-Wdeprecated-declarations]  
  155 |       detail::check_tommath_result(mp_set_int(&m_data, 0u));  
  
```

---

## Comment 1

> Username: madhur4127  
> Created_at: 2020-01-12 05:26:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/181#issuecomment-573383524  

@jzmaddock, I think the version of `libtommath-dev` that Travis uses is probably old so that's why there are `not declared in this scope` errors.  
  
How do you deal with this kind of issue wrt the changes? Or just leave them as it is to maintain backward compatability?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-12 13:10:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/181#issuecomment-573413926  

There seem to be a variety of ubuntu tommath versions in use, and current tommath master has removed the deprecated stuff altogether, so yes we need to fix this.  
  
How about checking for the presence of MP_DIGIT_BIT, and when defined use the "new" interface, otherwise the old?

---

## Comment 3

> Username: madhur4127  
> Created_at: 2020-01-12 16:57:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/181#issuecomment-573434935  

> How about checking for the presence of MP_DIGIT_BIT, and when defined use the "new" interface, otherwise the old?  
  
Yup. Structurally do you have any clean C++ hack instead of a bunch of `#ifdef`s?   
  
Note the branching of `mp_set_int` to `mp_set_u32`, `mp_set_u64`, `mp_set_i32`, etc.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-01-12 18:43:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/181#issuecomment-573444730  

Ugh... that's going to be messy.  
  
How about when MP_DIGIT_BIT is defined change  
  
```  
   tommath_int& operator=(boost::ulong_long_type i)  
```  
  
To peel off 64 bit chunks, the definition of  
  
```  
      boost::ulong_long_type mask  = ((1uLL << std::numeric_limits<unsigned>::digits) - 1);  
```  
  
should corrected too, then call `mp_set_u64`  rather than `mp_set_int`.  
  
The signed version needs correcting too.  
  
Then use one big #if...#else...#endif block to switch between the 2 different tommath interfaces?  
  
Still not pretty, but the best I can suggest at present :(

---
