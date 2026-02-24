# #35 Correct all stream operators [Closed]

> Username: glenfe  
> Created at: 2017-03-11 19:59:13 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2017-04-08 21:28:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35  

* Don't define these inside namespace std.  
* Don't use function template specialization. Just regular overloading is sufficient.  
* Use generic basic_ostream and basic_istream instead of ostream and istream.  
* Move the common functionality into one header and reduce duplication.  
* Remove friend declarations: Can be implemented without granting friendship.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-03-11 23:38:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#issuecomment-285909112  

Right.  But when I do this  
  
  
// stream operators  
template<  
     class CharT,  
     class Traits,  
     class T,  
     T Min,  
     T Max,  
     class P, // promotion polic  
     class E  // exception policy  
 >  
inline std::basic_ostream<CharT, Traits> & operator<<(  
     std::basic_ostream<CharT, Traits> & os,  
     const safe_base<T, Min, Max, P, E> & t  
){  
     return os << (  
         (std::is_same<T, signed char>::value  
         || std::is_same<T, unsigned char>::value  
         ) ?  
             static_cast<int>(t.m_t)  
         :  
             t.m_t  
     );  
}  
  
  
and build a test which uses os << safe<int>  
  
I get an link error:  
  
Undefined symbols for architecture x86_64:  
   "std::__1::basic_ostream<char, std::__1::char_traits<char> >&   
boost::numeric::operator<<<int, 0, 131070, boost::numeric::native,   
boost::numeric::throw_exception>(std::__1::basic_ostream<char,   
std::__1::char_traits<char> >&, boost::numeric::safe_base<int, 0,   
131070, boost::numeric::native, boost::numeric::throw_exception>   
const&)", referenced from:  
       bool test_add<unsigned short, unsigned short>(unsigned short,   
unsigned short, char const*, char const*, char) in test_add_native.o  
   "std::__1::basic_ostream<char, std::__1::char_traits<char> >&   
boost::numeric::operator<<<int, 0, 510, boost::numeric::native,   
boost::numeric::throw_exception>(std::__1::basic_ostream<char,   
std::__1::char_traits<char> >&, boost::numeric::safe_base<int, 0, 510,   
boost::numeric::native, boost::numeric::throw_exception> const&)",   
referenced from:  
       bool test_add<unsigned char, unsigned char>(unsigned char,   
unsigned char, char const*, char const*, char) in test_add_native.o  
  
....  
  
Since you're up on this stuff, maybe you know the missing magic here.  
  
On 3/11/17 11:59 AM, Glen Fernandes wrote:  
> ------------------------------------------------------------------------  
>  
>  
>         You can view, comment on, or merge this pull request online at:  
>  
> https://github.com/robertramey/safe_numerics/pull/35  
>  
>  
>         Commit Summary  
>  
>   * Correct stream output operators for interval  
>  
>  
>         File Changes  
>  
>   * *M* include/interval.hpp  
>     <https://github.com/robertramey/safe_numerics/pull/35/files#diff-0>  
>     (27)  
>  
>  
>         Patch Links:  
>  
>   * https://github.com/robertramey/safe_numerics/pull/35.patch  
>   * https://github.com/robertramey/safe_numerics/pull/35.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/pull/35>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3EIyAFocVk-31dxA7aYPNIYtFfFCks5rkv0RgaJpZM4MaUO_>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-03-12 04:04:40 UTC  
> Updated_at: 2017-03-12 05:35:21 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#issuecomment-285920740  

Robert, see the updated pull request, which also includes a test case. No linker errors like the ones you encountered. Tested with g++ 6.3.1.

---

## Review 3 [Commented]

> Username: arvidn  
> Created_at: 2017-03-12 04:40:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/safe_numerics/pull/35#pullrequestreview-26437105  

📁 include/io.hpp

```diff
  50 |+     int result;
  51 |+     is >> result;
  52 |+     value = result;
```

> Username: arvidn  
> Created_at: 2017-03-12 04:40:18 UTC  
> Updated_at: 2017-03-16 11:42:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#discussion_r105548695  

aren't you risking silent truncation here? (and the overload below)  
I would expect this to trigger ``-Wconversion``

> Username: glenfe  
> Created_at: 2017-03-12 05:27:33 UTC  
> Updated_at: 2017-03-16 11:42:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#discussion_r105549308  

Good point; that new abstraction isn't the best idea for input. I updated the latter for parity with what he had before.


---

## Comment 4

> Username: akrzemi1  
> Created_at: 2017-03-16 08:08:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#issuecomment-286985275  

The specializations are needed only for `char` types in order to avoid character representation. Maybe instead of specializing/overloading the interface functions just use function `as_integer` in the implementation, and provide overloads for `as_integer` that do the right conversions for display purposes:  
  
```c++  
// form Steven Watanabe's test case:  
  
template<class T>  
auto as_integer(T t) { return t; }  
  
auto as_integer(char ch) { return (int)ch; }  
auto as_integer(signed char ch) { return (int)ch; }  
auto as_integer(unsigned char ch) { return (int)ch; }  
  
```

---

## Comment 5

> Username: glenfe  
> Created_at: 2017-03-16 11:31:32 UTC  
> Updated_at: 2017-03-16 13:49:42 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#issuecomment-287030635  

Andrzej, agreed (and updated).  
  
I provided both  `template<class T> const T& output(const T&);` and `template<class T> T output(const T&&);` even though that isn't required here, being a little more generic doesn't hurt.

---

## Comment 6

> Username: robertramey  
> Created_at: 2017-04-08 21:28:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/35#issuecomment-292746919  

fixed - thanks for your help and efforts

---
