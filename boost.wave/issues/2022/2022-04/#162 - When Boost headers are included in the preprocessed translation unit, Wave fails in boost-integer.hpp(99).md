# #162 - When Boost headers are included in the preprocessed translation unit, Wave fails in boost/integer.hpp(99) [Closed]

> Username: abakhirkin  
> Created at: 2022-04-12 20:49:48 UTC  
> Updated at: 2022-06-22 18:23:07 UTC  
> Closed at: 2022-06-22 18:23:07 UTC  
> Url: https://github.com/boostorg/wave/issues/162  

I'm trying to preprocess a translation unit that includes Boost headers (Boost Process in this example).  
```c++  
#include <boost/process.hpp>  
  
int main(int argc, char** argv) {  
    return 0;  
}  
```  
  
When trying to give this code to the `quick_start` sample (where necessary include paths and definitions were added), Wave reports the following failure:  
```  
/local/src/boost/boost/integer.hpp(99): error: ill formed preprocessor expression: 1 &&   ((1 && ((0x7fffffffffffffffLL * 2ULL + 1ULL) != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (1 && ((0x7fffffffffffffffLL * 2ULL + 1ULL) != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (0 && (0 != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (0 && (0 != (0x7fffffffffffffffL * 2UL + 1UL))))  
```  
The code at that line looks pretty innocent:  
```c++  
#if defined(BOOST_HAS_LONG_LONG) &&\  
   ((defined(ULLONG_MAX) && (ULLONG_MAX != ULONG_MAX)) ||\  
    (defined(ULONG_LONG_MAX) && (ULONG_LONG_MAX != ULONG_MAX)) ||\  
    (defined(ULONGLONG_MAX) && (ULONGLONG_MAX != ULONG_MAX)) ||\  
    (defined(_ULLONG_MAX) && (_ULLONG_MAX != ULONG_MAX)))  
  template <> struct exact_signed_base_helper<sizeof(boost::long_long_type)* CHAR_BIT> { typedef boost::long_long_type exact; };  
  template <> struct exact_unsigned_base_helper<sizeof(boost::ulong_long_type)* CHAR_BIT> { typedef boost::ulong_long_type exact; };  
#endif  
```  
Also, by itself the conditional preprocesses just fine. For example, the following code produces no errors (and the conditions seem to evaluate to `false`):  
```c++  
#if 1 &&   ((1 && ((0x7fffffffffffffffLL * 2ULL + 1ULL) != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (1 && ((0x7fffffffffffffffLL * 2ULL + 1ULL) != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (0 && (0 != (0x7fffffffffffffffL * 2UL + 1UL))) ||    (0 && (0 != (0x7fffffffffffffffL * 2UL + 1UL))))  
  template <> struct exact_signed_base_helper<sizeof(boost::long_long_type)* CHAR_BIT> { typedef boost::long_long_type exact; };  
  template <> struct exact_unsigned_base_helper<sizeof(boost::ulong_long_type)* CHAR_BIT> { typedef boost::ulong_long_type exact; };  
#endif  
  
#define BOOST_HAS_LONG_LONG 1  
  
#if defined(BOOST_HAS_LONG_LONG) &&\  
   ((defined(ULLONG_MAX) && (ULLONG_MAX != ULONG_MAX)) ||\  
    (defined(ULONG_LONG_MAX) && (ULONG_LONG_MAX != ULONG_MAX)) ||\  
    (defined(ULONGLONG_MAX) && (ULONGLONG_MAX != ULONG_MAX)) ||\  
    (defined(_ULLONG_MAX) && (_ULLONG_MAX != ULONG_MAX)))  
  template <> struct exact_signed_base_helper<sizeof(boost::long_long_type)* CHAR_BIT> { typedef boost::long_long_type exact; };  
  template <> struct exact_unsigned_base_helper<sizeof(boost::ulong_long_type)* CHAR_BIT> { typedef boost::ulong_long_type exact; };  
#endif  
  
int main() {  
}  
```  
  
As an experiment I tried defining `#define BOOST_WAVE_SUPPORT_LONGLONG_INTEGER_LITERALS 1` before including Wave headers, but that did not seem to have effect.  
  
I'm attaching [the modified version of quick_start.cpp](https://github.com/boostorg/wave/files/8476437/quick_start.txt) that reproduces the issue for me. The difference from the original example is that it sets the language standard to C++11 and adds include paths and definitions that I dumped from a context where I first encountered the issue, and those in turn were dumped from GCC 8.3 on Debian 10.

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-04-12 21:13:40 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1097224379  

That looks like a bug to me! Thanks for the excellent writeup. It may be a little while before I get to it though :(

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-06-16 06:07:54 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1157269671  

It looks like the problem is that Wave does not implement certain gcc builtins, in particular `__has_builtin`, `__has_include`, and `__has_cpp_attribute`. When I run this test case, Wave encounters this line:  
  
`#if __has_builtin(__builtin_is_constant_evaluated)`  
  
and, since it knows neither of those macros, interprets it as `0(0)`, which is an invalid expression.  
  
I'm not sure whether it makes sense to implement this in Wave. @hkaiser any thoughts?  
  
You may also be interested in bug #108.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-06-16 06:11:14 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1157271917  

I just noticed that we have the same error, but a different problem expression! I will try again using g++ version 8, as you have.

---

## Comment 4

> Username: jefftrull  
> Created at: 2022-06-16 06:17:05 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1157275745  

Unfortunately I'm unable to reproduce your error using my g++-8. I get an error in a system header instead:  
  
```  
/usr/include/linux/stddef.h(26): error: ill formed preprocessor directive: #define __struct_group(TAG, NAME, ATTRS, MEMBERS...)      union {                 struct { MEMBERS } ATTRS;               struct TAG { MEMBERS } ATTRS NAME;  }  
```

---

## Comment 5

> Username: jefftrull  
> Created at: 2022-06-16 06:57:27 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1157304023  

What is your boost version?

---

## Comment 6

> Username: jefftrull  
> Created at: 2022-06-16 19:24:23 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1158050025  

OK nevermind I can reproduce it! I have to work around the `stddef.h` issue (which should probably be the topic of a different bug report)

---

## Comment 7

> Username: abakhirkin  
> Created at: 2022-06-16 22:32:58 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1158204536  

I no longer have the older Debian 10 system with GCC 8, but I have more or less the same behaviour on Debian 11 with GCC 10. I use Boost from `develop` branch from git, and I updated and re-built it today (well, hopefully I did it correctly). I did need to update my reproduction example, so I'm attaching the new version of `quick_start.cpp`: [quick_start.txt](https://github.com/boostorg/wave/files/8923094/quick_start.txt). For me, it does not seem to trigger the issue with `__has_builtin` that you point out.  
  
Also I don't think I've seen the `stddef.h` issue or at least I don't remember seeing it. I did see the `0(0)`, but in my case I think it was because I added all GCC 8 definitions to a Wave context and those had something along the lines of `#define __has_include(something) __has_include__(something)`, where I assume `__has_include__` is not defined in Wave (GCC 10 definitions do not seem to have `__has_include`).  
  
I understand that making Wave pretend being GCC is not always going to work, but so far it seems to let me preprocess standard library headers.

---

## Comment 8

> Username: hkaiser  
> Created at: 2022-06-16 22:56:28 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1158240037  

> It looks like the problem is that Wave does not implement certain gcc builtins, in particular `__has_builtin`, `__has_include`, and `__has_cpp_attribute`. When I run this test case, Wave encounters this line:  
>   
> `#if __has_builtin(__builtin_is_constant_evaluated)`  
>   
> and, since it knows neither of those macros, interprets it as `0(0)`, which is an invalid expression.  
>   
> I'm not sure whether it makes sense to implement this in Wave. @hkaiser any thoughts?  
  
Don't we have some of those macros predefined already?

---

## Comment 9

> Username: jefftrull  
> Created at: 2022-06-16 23:14:20 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1158250605  

@hkaiser Sorry, you are right, we do implement `__has_include`! In fact _I_ implemented it, in 2020.  
  
@abakhirkin I can reproduce your error now and it seems to be a problem in the c++ expression grammar parsing. I'm digging in further. Thanks for the updated testcase.

---

## Comment 10

> Username: jefftrull  
> Created at: 2022-06-21 07:45:54 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1161382218  

It looks like when we introduced long integer literals we did not add them to the conditional expression (i.e. `#if`) grammar. I'm looking at fixes now.

---

## Comment 11

> Username: jefftrull  
> Created at: 2022-06-22 01:36:27 UTC  
> Url: https://github.com/boostorg/wave/issues/162#issuecomment-1162526067  

@abakhirkin let me know if the PR lets you make forward progress, thanks.
