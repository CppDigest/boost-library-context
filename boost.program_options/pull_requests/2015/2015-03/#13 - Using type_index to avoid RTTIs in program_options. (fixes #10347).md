# #13 Using type_index to avoid RTTIs in program_options. (fixes #10347) [Closed]

> Username: gongminmin  
> Created at: 2015-03-04 05:38:41 UTC  
> Updated at: 2015-05-04 01:20:13 UTC  
> Closed at: 2015-04-30 19:16:49 UTC  
> Url: https://github.com/boostorg/program_options/pull/13  



---

## Comment 1

> Username: vprus  
> Created_at: 2015-04-13 09:31:34 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-92291365  

Thanks for the patch! Sadly, the only thing I can say right now is that I'm not interested in non-RTTI usage and I can't really evaluate whether the change of method types in this patch will break existing clients or not.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-04-14 07:25:13 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-92674452  

TypeIndex takes care of RTTI on/off and provides additional compile and link time checks (asserting that user do not mix on/off flags for example).  
  
Patch is valid and must break nothing.

---

## Comment 3

> Username: vprus  
> Created_at: 2015-04-14 07:50:15 UTC  
> Updated_at: 2015-04-14 08:53:33 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-92680158  

The patch changes signatures of methods. What if existing client code uses them? It  
will no longer work.  
On Вт, 14 апр. 2015 at 10:25 Antony Polukhin notifications@github.com  
wrote:  
  
> TypeIndex takes care of RTTI on/off and provides additional compile and  
> link time checks (asserting that user do not mix on/off flags for example).  
>   
> Patch is valid and must break nothing.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/program_options/pull/13#issuecomment-92674452  
> .

---

## Comment 4

> Username: apolukhin  
> Created_at: 2015-04-14 19:56:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-93040695  

This signature does not change. `boost::typeindex::type_info` is a typedef for `std::type_info` for cases when `std::type_info` is available (RTTI on or MSVC compiler).  
  
When `std::type_info` and `typeid` are not available, `boost::typeindex::type_info` emulates RTTI.  
  
However the patch is not ideal, `test/options_description_test.cpp` still requires RTTI

---

## Comment 5

> Username: gongminmin  
> Created_at: 2015-04-14 23:59:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-93118299  

You mean the "dynamic_cast" in test/options_description_test.cpp? Could we make it static_cast and skip the BOOST_CHECK(b)?

---

## Comment 6

> Username: apolukhin  
> Created_at: 2015-04-15 05:16:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-93192566  

How about adding a new line after the `[ po-test exception_txt_test.cpp ]` into test/Jamfile.v2:  
  
```  
[ run options_description_test.cpp : : : <rtti>off <define>BOOST_NO_RTTI <define>BOOST_NO_TYPEID : options_description_no_rtti_test ]  
```  
  
This will build a program_options library without RTTI and run the `options_description_test.cpp` one more time.  
  
Dynamic cast can be put into `#ifndef BOOST_NO_RTTI` block.

---

## Comment 7

> Username: gongminmin  
> Created_at: 2015-04-15 05:43:31 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-93203779  

Good idea. I'll submit a patch ASAP.

---

## Comment 8

> Username: vprus  
> Created_at: 2015-04-27 18:54:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-96778872  

This patch produces compilation error for me:  
  
```  
options_description_test.cpp:34:50: error: invalid static_cast from type ‘boost::shared_ptr<const   boost::program_options::value_semantic>::element_type* {aka const  boost::program_options::value_semantic*}’ to type ‘const boost::program_options::typed_value_base*’  
     (desc.find("foo", false).semantic().get());  
```

---

## Comment 9

> Username: gongminmin  
> Created_at: 2015-04-28 05:57:02 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-96924459  

I think we have to keep that part of code inside #ifndef BOOST_NO_RTTI/#endif, unless there is a way to avoid the dynamic_cast for multiple inheritance. In my new commit the compiling errors are fixed. Sorry for the inconvenient.

---

## Comment 10

> Username: vprus  
> Created_at: 2015-04-28 07:07:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-96953303  

That seem to suggest the library is not quite usable without RTTI? In that, you can check whether typed_value_base holds a particular type, but there's no way to get at typed_value_base from value_semantics, so the only way to call typed_value_base::value_type is if you already have a class derived from typed_value_base, in which case you probably know it's type anyway?

---

## Comment 11

> Username: gongminmin  
> Created_at: 2015-04-29 05:11:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-97307158  

Yes, it looks like if a user need to call typed_value_base::value_type(), there is no way to avoid RTTI. I tried to add a typed_value_base\* parameter to options_description's constructor but that means it's not compatible to all existing user code.  
  
For my project, we don't call value_type() so we can live with other parts of program_options without RTTI. I think I have to pull out this request since it can't fully support all features.

---

## Comment 12

> Username: vprus  
> Created_at: 2015-04-29 06:36:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-97323513  

Maybe it would be possible to not defined value_type() at all if RTTI is off? Though that would make typed_value_base not useful at all - maybe that class can be only defined if RTTI is enabled?

---

## Comment 13

> Username: gongminmin  
> Created_at: 2015-04-30 04:21:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-97659216  

I refined my commit to disable typed_value_base when RTTI is off.

---

## Comment 14

> Username: vprus  
> Created_at: 2015-04-30 19:16:49 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-97934050  

Thanks, I've pushed this change. Please take a look at the follow-up change I've committed - bd1d0bd - is it all right?

---

## Comment 15

> Username: gongminmin  
> Created_at: 2015-05-01 04:12:40 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98039970  

Yes, it's correct. And if we are using std::type_info, we can even revert the #include <boost/type_index.hpp> to #include <typeinfo> in include/boost/program_options/value_semantic.hpp, and boost::typeindex::type_id<T> to typeid(T) test/options_description_test.cpp.

---

## Comment 16

> Username: vprus  
> Created_at: 2015-05-01 16:16:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98168707  

Thanks, done in 0756d35. Out of curiousity, what's your particular reason to build with RTTI off?

---

## Comment 17

> Username: gongminmin  
> Created_at: 2015-05-03 05:08:51 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98437647  

When compiling my project with clang for Windows (weird right?), RTTI causes some trouble in release mode. Since we don't actually use RTTI in release, we just turn it off. Then there are some compiling errors in value_semantic.hpp, so I'm looking for ways to make a compatible code for both RTTI-on and RTTI-off for program_options.

---

## Comment 18

> Username: gongminmin  
> Created_at: 2015-05-03 05:43:10 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98439838  

Thanks for your modification. There is one more place needs to be reverted. In include/boost/program_options/value_semantic.hpp, line 366. It can be "const std::type_info& value_type() const".

---

## Comment 19

> Username: vprus  
> Created_at: 2015-05-03 18:55:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98523047  

Indeed, done now.

---

## Comment 20

> Username: gongminmin  
> Created_at: 2015-05-04 01:20:13 UTC  
> Url: https://github.com/boostorg/program_options/pull/13#issuecomment-98564110  

Thanks!

---
