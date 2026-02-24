# #26 - Compilation errors with overloaded functions [Closed]

> Username: bstoeger  
> Created at: 2018-08-02 12:10:56 UTC  
> Updated at: 2019-05-10 10:20:54 UTC  
> Closed at: 2019-05-10 10:17:06 UTC  
> Url: https://github.com/boostorg/rational/issues/26  

Functions that have overloads for `char *` and `boost::rational<T>` fail compilation when called with a string-literal. Example:  
```  
#include <boost/rational.hpp>  
  
class Stream;  
Stream &operator<<(Stream &, const char *);  
Stream &operator<<(Stream &, const boost::rational<int> &);  
  
void func(Stream &s)  
{  
	s << "string";  
}  
```  
fails on gcc-7 with:  
```  
In file included from /usr/include/c++/7/string_view:40:0,  
                 from /usr/include/c++/7/bits/basic_string.h:48,  
                 from /usr/include/c++/7/string:52,  
                 from /usr/include/c++/7/bits/locale_classes.h:40,  
                 from /usr/include/c++/7/bits/ios_base.h:41,  
                 from /usr/include/c++/7/iomanip:40,  
                 from /usr/include/boost/rational.hpp:68,  
                 from test_boost.C:1:  
/usr/include/c++/7/limits: In instantiation of ‘struct std::numeric_limits<char [7]>’:  
/usr/include/boost/rational.hpp:119:7:   required from ‘const bool boost::rational_detail::is_compatible_integer<char [7], int>::value’  
/usr/include/boost/rational.hpp:156:21:   required by substitution of ‘template<class T> constexpr boost::rational<int>::rational(const T&, const typename boost::enable_if_c<boost::rational_detail::is_compatible_integer<T, int>::value, void>::type*) [with T = char [7]]’  
test_boost.C:9:7:   required from here  
/usr/include/c++/7/limits:317:7: error: function returning an array  
       min() _GLIBCXX_USE_NOEXCEPT { return _Tp(); }  
       ^~~  
```  
  
The reason was determined in a reddit-thread (https://www.reddit.com/r/cpp/comments/8z50il/should_boostrational_instantiate_templates_for/): `std::numeric_limits<>` fails for array-types and a string-literal is an array-of-char.  
  
I helped myself by disabling `rational_detail::is_compatible_integer<>` for non-integer types and introducing a constructor for non-conversions [`boost::rational<T>::rational(const T&)`].  
  
But it would be nice if this worked out-of-the box.  
  
Thanks.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-02 12:15:54 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-409905830  

Pull requests are always welcome for review.

---

## Comment 2

> Username: bstoeger  
> Created at: 2018-08-02 12:20:40 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-409907001  

I fear that I'm not well-versed enough in TMP to solve this properly.

---

## Comment 3

> Username: SpiderCh  
> Created at: 2019-03-20 07:17:14 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-474714608  

Ping

---

## Comment 4

> Username: bstoeger  
> Created at: 2019-05-10 10:06:46 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-491235781  

For reference:  
  
This is the patch/hack I use to make my code compile:  
```  
--- rational.hpp.old    2019-05-07 12:08:09.007969772 +0200  
+++ rational.hpp        2019-05-10 11:59:58.398452056 +0200  
@@ -113,9 +113,15 @@  
   
 namespace rational_detail{  
   
-   template <class FromInt, class ToInt>  
+   template <class FromInt, class ToInt, class Enable = void>  
    struct is_compatible_integer  
    {  
+      BOOST_STATIC_CONSTANT(bool, value = false);  
+   };  
+  
+   template <class FromInt, class ToInt>  
+   struct is_compatible_integer<FromInt, ToInt, typename boost::enable_if<boost::is_arithmetic<FromInt>>::type>  
+   {  
       BOOST_STATIC_CONSTANT(bool, value = ((std::numeric_limits<FromInt>::is_specialized && std::numeric_limits<FromInt>::is_integer  
          && (std::numeric_limits<FromInt>::digits <= std::numeric_limits<ToInt>::digits)  
          && (std::numeric_limits<FromInt>::radix == std::numeric_limits<ToInt>::radix)  
@@ -200,7 +206,7 @@  
     // conversion from T to IntType, they will throw a bad_rational  
     // if the conversion results in loss of precision or undefined behaviour.  
     //  
-    template <class T>  
+    template <class T, typename boost::enable_if<boost::is_integral<T>, int>::type = 0>  
     rational(const T& n, typename enable_if_c<  
        std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer  
        && !rational_detail::is_compatible_integer<T, IntType>::value  
```  
  
Hope that helps. Perhaps someone can use this as a starting point for a proper fix? Sadly, C++ TMP is pure gibberish to me.

---

## Comment 5

> Username: bstoeger  
> Created at: 2019-05-10 10:17:06 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-491238613  

Oh - I see this was fixed in 0ccc0230abedd59dc59b478349236cb2a284db6b. Closing.

---

## Comment 6

> Username: bstoeger  
> Created at: 2019-05-10 10:20:54 UTC  
> Url: https://github.com/boostorg/rational/issues/26#issuecomment-491239645  

PS: Many thanks @SpiderCh!
