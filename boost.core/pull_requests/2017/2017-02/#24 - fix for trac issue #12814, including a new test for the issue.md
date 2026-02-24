# #24 fix for trac issue #12814, including a new test for the issue [Merged]

> Username: HDembinski  
> Created at: 2017-02-05 19:07:17 UTC  
> Updated at: 2017-02-09 20:27:43 UTC  
> Merged at: 2017-02-09 17:37:07 UTC  
> Closed at: 2017-02-09 17:37:07 UTC  
> Url: https://github.com/boostorg/core/pull/24  



---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-02-05 19:13:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/24#pullrequestreview-20176104  

📁 test/lightweight_test_test.cpp

```diff
  67 | 
  68 |+     const char* s1 = "abc"; // make sure addresses are different
  69 |+     const char* s2 = "abc"; // make sure addresses are different
```

> Username: Lastique  
> Created_at: 2017-02-05 19:13:54 UTC  
> Updated_at: 2017-02-09 13:52:43 UTC  
> Url: https://github.com/boostorg/core/pull/24#discussion_r99496879  

This does not guarantee that `s1` and `s2` are not equal. The compiler may merge equal string literals.

> Username: HDembinski  
> Created_at: 2017-02-05 19:16:57 UTC  
> Updated_at: 2017-02-09 13:52:43 UTC  
> Url: https://github.com/boostorg/core/pull/24#discussion_r99496934  

I see what you mean. I am open for suggestions on how to make it better/more portable :)  
  
I tested this on gcc.godbolt.org and wandbox. It works ok with -O0.

> Username: HDembinski  
> Created_at: 2017-02-05 19:25:34 UTC  
> Updated_at: 2017-02-09 13:52:43 UTC  
> Url: https://github.com/boostorg/core/pull/24#discussion_r99497145  

I added another patch to fix this issue.


---

## Comment 2

> Username: glenfe  
> Created_at: 2017-02-05 19:29:35 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277542670  

Doesn't this assume that if someone is comparing two `const char*`s that they're comparing C-strings? What if they aren't?

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-02-05 19:33:30 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277542935  

I would have thought the more desirable thing is something like BOOST_TEST_CSTR_EQ (which could be implemented in terms of BOOST_TEST_EQ(string_ref(a), string_ref(b))

---

## Comment 4

> Username: HDembinski  
> Created_at: 2017-02-05 19:34:43 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277543018  

glenfe: AFAIK, this mimics the behavior of boost/test. I haven't checked the implementation of boost/test, but I noticed this bug only, because I switched from boost/test to boost/core/lightweight_test. When a user passes const char*, it is natural to assume that this is the desired behavior. If the user really wants to compare pointer addresses, he/she can cast to void*.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-02-05 19:44:38 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277543649  

A bit ambiguous... on one hand, we want `BOOST_TEST_EQ( x, y )` to be like `BOOST_TEST( x==y )`, but on another, the printing of the values of `x` and `y` will print the strings, not the pointers. Hmmm.  
  
To guarantee different addresses we need something like `"xabc"+1, "yabc"+1` by the way; `"xxxabc"` and `"abc"` are still foldable.

---

## Comment 6

> Username: glenfe  
> Created_at: 2017-02-05 19:49:11 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277543938  

Sure, but you could also suggest that if a user really wants to compare C-strings, they could just do BOOST_TEST_EQ(string_ref(a), string_ref(b)) which to me is more intuitive than assuming const char* is a C string in an equality function.  
  
Presumably nobody is using it to compare pointer values right now that would be broken by this change (i.e. the assumption is that this is safe because everyone is generally using BOOST_TEST for that).

---

## Comment 7

> Username: HDembinski  
> Created_at: 2017-02-05 19:58:57 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277544680  

pdimov: i see the point of avoiding ambiguity, but i think it is a rather artificial concern. you can argue what is consistent from a theoretical point of view of the interface, or what is the least-surprise for the user. i think this solution works quite nicely for a naive user. i mean, really, who is comparing const char* and expecting a check of equality of pointer addresses.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-02-05 20:03:44 UTC  
> Updated_at: 2017-02-05 20:05:44 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277544974  

Glen: We (that is, `ostream`) already print `char const*` as a C string, so one of the two needs fixing in either case. As it is, `BOOST_TEST_EQ` is not suited for `char const*`.  
  
Hans: anybody who is testing pointers of type `T*` for equality when `T` happens to be `char`.  
  
```  
char x, y;  
BOOST_TEST_NE( &x, &y );  
```  
  
for instance. Hopefully there won't be many such people if we adopt this change.

---

## Comment 9

> Username: Lastique  
> Created_at: 2017-02-05 20:04:18 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277545018  

>  
> Sure, but you could also suggest that if a user really wants to compare  
> C-strings, they could just do BOOST_TEST_EQ(string_ref(a), string_ref(b))  
> which to me is more intuitive than assuming const char* is a C string in an  
> equality function.  
>  
Using `string_view`/`string_ref` requires an additional dependency which  
might otherwise be unnecessary. If we want to be explicit, we should  
probably provide a separate set of macros for c-string comparison and make  
the current macros fail to compile if arguments are cv-`char*` (with the  
intention to force the user to explicitly cast the pointers to `void*`).  
  
I don't have a strong preference between the explicit approach and the  
compatibility with Boost.Test approach. Both have pros and cons.

---

## Comment 10

> Username: HDembinski  
> Created_at: 2017-02-05 20:11:14 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277545470  

Lastique: I agree, I was just going to write the same. I still think it is fair to have an overload for const char*, since it has this special meaning in C that everyone is aware of.

---

## Comment 11

> Username: glenfe  
> Created_at: 2017-02-05 20:25:58 UTC  
> Updated_at: 2017-02-05 20:27:03 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277546464  

Peter: Right, BOOST_TEST_EQ is unfit anyway because of the use of ostream. I'm not sure what the best design is, based on user expectations given that Boost.Test provides that convenience overload for const char*. (If it isn't intuitive, we could go the other way - specialize for const char*, still check pointer equality, but supply the two values as void* to the output stream)  
  
Hans: For example, https://github.com/boostorg/align/blob/develop/test/align_test.cpp (at one point in time)  compared two char* pointers (as pointers, not strings) (using BOOST_TEST).

---

## Comment 12

> Username: HDembinski  
> Created_at: 2017-02-05 20:29:22 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277546682  

glenfe: Granted, but I didn't suggest to add an overload for char*, only for const char*.

---

## Comment 13

> Username: HDembinski  
> Created_at: 2017-02-05 20:31:51 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277546839  

I would like it if comparisons of T* for any type other than T=void gave a compile time error.

---

## Review 14 [Commented]

> Username: glenfe  
> Created_at: 2017-02-05 21:03:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/24#pullrequestreview-20178616  

Do you also need to overload for const unsigned char* and const signed char*?

---

## Comment 15

> Username: HDembinski  
> Created_at: 2017-02-05 22:43:03 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277556243  

Glen: I just checked with wandbox, that the compiler recognizes overloads of const unsigned char*, const signed char*, and const char* as distinct. I am only suggesting string comparison for const char*. I would prefer if every other comparison of T* or const T* for T!=void raises a compile-time error.

---

## Comment 16

> Username: glenfe  
> Created_at: 2017-02-05 22:57:02 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277557200  

Hans: They are distinct, which is why I was wondering if we need those overloads too: e.g. Even with your change, someone using BOOST_TEST_EQ(x, y) for when x and y are const unsigned char* would result in the output stream trying to treat x and y like C strings yet the equality being determined based on comparison of pointer values, right?   
  
(i.e. If we're fixing this, we should probably apply your behavior consistently for those cases too.)

---

## Comment 17

> Username: pdimov  
> Created_at: 2017-02-05 23:04:31 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277557727  

I'd rather not compare `unsigned char*` as C strings. It's true that they are printed that way by `ostream`, but I don't use them as strings, and I don't think that many people do.  
  
What does Boost.Test do for `unsigned char*` and `signed char*`?

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-02-05 23:09:35 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277558055  

Comparing `char*` is a bit of a problem, too.

---

## Comment 19

> Username: HDembinski  
> Created_at: 2017-02-05 23:13:21 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277558268  

Peter: Ok, let's not make this too complicated. It seems we cannot find a good consensus, so I would switch gears.  
  
I am happy to drop this PR and make another one with an implementation for BOOST_TEST_EQ_C_STR, as suggested by Glen.

---

## Comment 20

> Username: HDembinski  
> Created_at: 2017-02-06 20:11:14 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277798791  

so how about this? makes more sense?

---

## Comment 21

> Username: glenfe  
> Created_at: 2017-02-07 13:25:40 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-277998714  

Hans, looks OK to me. Maybe the more maintainable approach instead of overloading `test_eq_impl` and `test_ne_impl` for `const char*` is to change the existing `test_eq_impl` and `test_ne_impl` to stream `test_output(u)` and `test_output(v)`. That way you can just simply provide:  
  
```  
template<class T> inline const T& test_output(const T& v) { return v; }  
inline const void* test_output(const char* v) { return v; }  
inline const void* test_output(const unsigned char* v) { return v; }  
inline const void* test_output(const signed char* v) { return v; }  
```  
  
Or alternatively just two overloads, where one just covers all pointer types.

---

## Comment 22

> Username: HDembinski  
> Created_at: 2017-02-07 15:09:28 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278027776  

Glen: Good point, your solution is very elegant. I also added two tests of expected failures.

---

## Comment 23

> Username: pdimov  
> Created_at: 2017-02-09 12:21:27 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278626298  

This looks good, thanks; could you please add `BOOST_TEST_CSTR_*` to the documentation as well?

---

## Comment 24

> Username: HDembinski  
> Created_at: 2017-02-09 12:50:21 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278631708  

Ok, I added documentation for the macros.  
  
I also had to fix the Jamfile to build the documentation on my system. It was not working with "import" instead of "using". I am still struggling with boost.build so I have no idea what's going on and why it ever worked without "using boostbook".

---

## Comment 25

> Username: pdimov  
> Created_at: 2017-02-09 13:14:30 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278636848  

Your Jamfile changes are wrong, please revert them. `using` goes in `user-config.jam`, not in the Jamfile.  
  
http://www.boost.org/doc/libs/1_63_0/doc/html/quickbook/install.html

---

## Comment 26

> Username: glenfe  
> Created_at: 2017-02-09 13:48:55 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278645021  

Hans, looks good to me (after you address Peter's documentation comment).  
  
Peter, I might be overthinking it, but perhaps the overload set needs to be:  
```  
template<class T> T& output(T& o) { return o; }  
template<class T> void* output(T* o) { return o; }  
template<class T> const void* output(const T* o) { return o; }  
template<class T> volatile void* output(volatile T* o) { return o; }  
template<class T> const volatile void* output(const volatile T* o) { return o; }  
```  
  
That handles plain char*, as well as basic_ostreams for other character types.

---

## Comment 27

> Username: HDembinski  
> Created_at: 2017-02-09 14:22:41 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278654866  

Ok, I reverted the changes to the jamfile, sorry about that.  
  
Thanks for teaching me and working with me on this patch I suppose you guys could have patched this in the right way in 10 min, but I hope it will pay out in the long run. I would like to continue to help out with boost patches in the future.

---

## Comment 28

> Username: pdimov  
> Created_at: 2017-02-09 16:45:20 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278699421  

Glen: Yes, I agree that this is more precise.  
  
```  
template<class T> void const volatile* output( T* t ) { return t; }  
```  
  
should be enough though - or would have been enough, if `std::ostream` could output pointers to `volatile`, which it cannot. So  
  
```  
template<class T> void const * output( T* t ) { return t; }  
```  
  
or, if we insist on handling `volatile` the proper way,  
  
```  
template<class T> void const * output( T* t ) { return const_cast<void const*>( static_cast<void const volatile*>( t ) ); }  
```  
  
or  
  
```  
template<class T> void const * output( T* t ) { return t; }  
template<class T> void const * output( T volatile* t ) { return const_cast<T*>( t ); }  
```  
  
This still doesn't quite work as it should for function pointers, although "as it should" is not very well defined because `ostream` can't output function pointers either so those get converted to `bool`. But this is mostly academic now.

---

## Comment 29

> Username: pdimov  
> Created_at: 2017-02-09 16:50:16 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278700930  

Bottom line, the patch is correct as it stands. :-) I'll merge it in a few minutes.

---

## Comment 30

> Username: HDembinski  
> Created_at: 2017-02-09 16:52:26 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278701617  

Cool 👍

---

## Comment 31

> Username: pdimov  
> Created_at: 2017-02-09 17:38:01 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278715151  

OK, merged. Let's hope nothing breaks as a result. :-)

---

## Comment 32

> Username: glenfe  
> Created_at: 2017-02-09 19:59:55 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278755145  

I suspect char* (non const) will still be output as a C string right now, which is undesirable. If that is the case, I think the simplest solution, that also works with function pointers, will involve something along the lines of:  
  
```cpp  
template<class CharT, class Traits, class T>  
inline std::basic_ostream<CharT, Traits>&  
test_output(std::basic_ostream<CharT, Traits>& os, T& value)  
{  
    return os << value;  
}  
template<class CharT, class Traits, class T>  
inline std::basic_ostream<CharT, Traits>&  
test_output(std::basic_ostream<CharT, Traits>& os, T* value)  
{  
    return os.operator<<(value);  
}  
```  
  
And then just using test_output(stream,value) instead of stream<<value.

---

## Comment 33

> Username: pdimov  
> Created_at: 2017-02-09 20:27:43 UTC  
> Url: https://github.com/boostorg/core/pull/24#issuecomment-278763153  

You're right about `char*` (`char[]` works, but `char*` doesn't.) The repetition of `BOOST_LIGHTWEIGHT_TEST_OSTREAM` is a bit ugly though, and pointers to volatile still don't get fixed, so I'll just add  
  
```  
inline const void* test_output_impl(char* v) { return v; }  
inline const void* test_output_impl(unsigned char* v) { return v; }  
inline const void* test_output_impl(signed char* v) { return v; }  
template<class T> inline const void* test_output_impl(T volatile* v) { return const_cast<T*>(v); }  
```  
to the overload set instead, which seems to cover everything properly.

---
