# #646 - New warning with Clang-20 [Closed]

> Username: mborland  
> Created at: 2025-01-06 16:15:22 UTC  
> Updated at: 2025-04-25 14:24:19 UTC  
> Closed at: 2025-04-25 14:12:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/646  

```  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2356:  
../../../boost/multiprecision/cpp_int/literals.hpp:272:1: error: identifier '_cppui512' preceded by whitespace in a literal operator declaration is deprecated [-Werror,-Wdeprecated-literal-operator]  
../../../boost/multiprecision/cpp_int/literals.hpp:262:201: note: expanded from macro 'BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL'  
  262 |    constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<Bits, Bits, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> > operator"" BOOST_JOIN(_cppui, Bits)() \  
      |                                                                                                                                                                                                         ^  
../../../boost/config/helper_macros.hpp:33:26: note: expanded from macro 'BOOST_JOIN'  
   33 | #define BOOST_JOIN(X, Y) BOOST_DO_JOIN(X, Y)  
      |                          ^  
../../../boost/config/helper_macros.hpp:34:29: note: expanded from macro 'BOOST_DO_JOIN'  
   34 | #define BOOST_DO_JOIN(X, Y) BOOST_DO_JOIN2(X,Y)  
      |                             ^  
../../../boost/config/helper_macros.hpp:35:30: note: expanded from macro 'BOOST_DO_JOIN2'  
   35 | #define BOOST_DO_JOIN2(X, Y) X##Y  
      |                              ^  
<scratch space>:47:1: note: expanded from here  
   47 | _cppui512  
```  
  
The fix is pretty trivial. See: https://github.com/cppalliance/decimal/pull/771

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-04-25 13:13:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/646#issuecomment-2830399090  

See also #682

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-25 14:03:52 UTC  
> Updated at: 2025-04-25 14:10:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/646#issuecomment-2830529768  

> fix is pretty trivial.  
  
Hi Matt (@mborland) I'm not sure how _trivial_ this fix is (at least for me). How does my proposed correction seem to you?  
  
I was unsure about removing the nested `BOOST_JOIN`, but that made the most intuitive sense to me.

---

## Comment 3

> Username: mborland  
> Created at: 2025-04-25 14:12:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/646#issuecomment-2830551233  

This was actually fixed already by: https://github.com/boostorg/multiprecision/pull/665. I will close it

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-04-25 14:24:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/646#issuecomment-2830580810  

> This was actually fixed already by: #665. I will close it.  
  
Oh cool. I guess it did somehow _seem_ familiar.  
  
OK, so I will simply close the PR after it cycles and take no further action on this. I will not commit changes that are not needed.  
  
Thank you Matt!
