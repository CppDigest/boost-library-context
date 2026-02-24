# #195 - Failure to expand certain multi-line macros properly [Closed]

> Username: devshgraphicsprogramming  
> Created at: 2023-11-29 01:05:38 UTC  
> Updated at: 2024-01-03 05:05:16 UTC  
> Closed at: 2024-01-03 05:05:16 UTC  
> Url: https://github.com/boostorg/wave/issues/195  

Hi, I have the following piece of code  
  
```cpp  
#define alignof(expr) ::nbl::hlsl::alignment_of<__decltype(expr)>::value  
  
// shoudl really return a std::type_info like struct or something, but no `constexpr` and unsure whether its possible to have a `const static SomeStruct` makes it hard to do...  
#define typeid(expr) (::nbl::hlsl::impl::typeid_t<__decltype(expr)>::value)  
  
#define NBL_REGISTER_OBJ_TYPE(T, A) namespace nbl { namespace hlsl { \  
    namespace impl { template<> struct typeid_t<T> : integral_constant<uint32_t,__COUNTER__> {}; } \   
    template<> struct alignment_of<T> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<const T> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<typename impl::add_lvalue_reference<T>::type> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<typename impl::add_lvalue_reference<const T>::type> : integral_constant<uint32_t,A> {}; \  
}}  
  
// TODO: find out how to do it such that we don't get duplicate definition if we use two function identifiers with same signature  
#define NBL_REGISTER_FUN_TYPE(fn) namespace nbl { namespace hlsl { template<> struct typeid_t<__decltype(fn)> : integral_constant<uint32_t,__COUNTER__> {}; }}  
// TODO: ideally we'd like to call NBL_REGISTER_FUN_TYPE under the hood, but we can't right now. Also we have a bigger problem, the passing of the function identifier as the second template parameter doesn't work :(  
/*  
template<> \  
struct function_info<__decltype(fn),fn> \  
{ \  
    using type = __decltype(fn); \  
    static const uint32_t address = __COUNTER__; \  
}; \  
}}}}  
*/  
  
// builtins  
  
#define NBL_REGISTER_MATRICES(T, A) \  
    NBL_REGISTER_OBJ_TYPE(T, A) \  
    NBL_REGISTER_OBJ_TYPE(T ## x4, A) \  
    NBL_REGISTER_OBJ_TYPE(T ## x3, A) \  
    NBL_REGISTER_OBJ_TYPE(T ## x2, A) \  
  
#define NBL_REGISTER_TYPES_FOR_SCALAR(T) \  
    NBL_REGISTER_OBJ_TYPE(T, sizeof(T)) \  
    NBL_REGISTER_OBJ_TYPE(T ## 1, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 2, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 3, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 4, sizeof(T))  
  
NBL_REGISTER_TYPES_FOR_SCALAR(int16_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(int32_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(int64_t)  
  
NBL_REGISTER_TYPES_FOR_SCALAR(uint16_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(uint32_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(uint64_t)  
  
NBL_REGISTER_TYPES_FOR_SCALAR(bool)  
  
NBL_REGISTER_TYPES_FOR_SCALAR(float16_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(float32_t)  
NBL_REGISTER_TYPES_FOR_SCALAR(float64_t)  
  
#undef NBL_REGISTER_MATRICES  
#undef NBL_REGISTER_TYPES_FOR_SCALAR  
```  
  
Wave leaves a piece of `NBL_REGISTER_OBJ_TYPE` behind.  
  
The output should be: [good.txt](https://github.com/boostorg/wave/files/13494938/good.txt)  
  
I got this instead: [bad.txt](https://github.com/boostorg/wave/files/13494942/bad.txt)  
  
Changing the definition of `NBL_REGISTER_OBJ_TYPE` to:  
```cpp  
#define NBL_IMPL_SPECIALIZE_TYPE_ID(T) namespace impl { template<> struct typeid_t<T> : integral_constant<uint32_t,__COUNTER__> {}; }  
  
#define NBL_REGISTER_OBJ_TYPE(T,A) namespace nbl { namespace hlsl { NBL_IMPL_SPECIALIZE_TYPE_ID(T) \  
    template<> struct alignment_of<T> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<const T> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<typename impl::add_lvalue_reference<T>::type> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<typename impl::add_lvalue_reference<const T>::type> : integral_constant<uint32_t,A> {}; \  
}}  
```  
fixes the problem

---

## Comment 1

> Username: jefftrull  
> Created at: 2023-11-29 19:55:33 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832610269  

Thanks for the bug report! That's quite a diff :) Any chance of a more minimal repro?

---

## Comment 2

> Username: devshgraphicsprogramming  
> Created at: 2023-11-29 20:00:13 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832616452  

> Thanks for the bug report! That's quite a diff :) Any chance of a more minimal repro?  
  
its quite hard to provoke it, but I guess you could try with  
```cpp  
#define alignof(expr) ::nbl::hlsl::alignment_of<__decltype(expr)>::value  
  
// shoudl really return a std::type_info like struct or something, but no `constexpr` and unsure whether its possible to have a `const static SomeStruct` makes it hard to do...  
#define typeid(expr) (::nbl::hlsl::impl::typeid_t<__decltype(expr)>::value)  
  
#define NBL_REGISTER_OBJ_TYPE(T, A) namespace nbl { namespace hlsl { \  
    namespace impl { template<> struct typeid_t<T> : integral_constant<uint32_t,__COUNTER__> {}; } \   
    template<> struct alignment_of<T> : integral_constant<uint32_t,A> {}; \  
    template<> struct alignment_of<const T> : integral_constant<uint32_t,A> {}; \  
}}  
  
  
#define NBL_REGISTER_TYPES_FOR_SCALAR(T) \  
    NBL_REGISTER_OBJ_TYPE(T, sizeof(T)) \  
    NBL_REGISTER_OBJ_TYPE(T ## 1, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 2, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 3, sizeof(T)) \  
    NBL_REGISTER_MATRICES(T ## 4, sizeof(T))  
  
NBL_REGISTER_TYPES_FOR_SCALAR(int16_t)  
```  
  
This is all about the preprocessor loosing its marbles and leaving me a random  
```cpp  
    template<> struct alignment_of<T> : integral_constant<uint32_t,A> {};     template<> struct alignment_of<const T> : integral_constant<uint32_t,A> {};     template<> struct alignment_of<typename impl::add_lvalue_reference<T>::type> : integral_constant<uint32_t,A> {};     template<> struct alignment_of<typename impl::add_lvalue_reference<const T>::type> : integral_constant<uint32_t,A> {}; }}  
```  
where the multiline macro definition used to be.  
  
And then pasting `\ ` in to the output whenever I use it

---

## Comment 3

> Username: jefftrull  
> Created at: 2023-11-29 20:47:27 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832676669  

Thanks! And can you tell us how you ran this - did you use the "wave" tool? If so, what were the command line options? And if it's your own code using Wave as a library, can I get access to that?

---

## Comment 4

> Username: devshgraphicsprogramming  
> Created at: 2023-11-29 21:01:56 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832694723  

> Thanks! And can you tell us how you ran this - did you use the "wave" tool? If so, what were the command line options?   
  
Unfortunately no, I guess I could have an intern try to reproduce through that.  
  
The issue is that I needed to "hack" quite a lot to make Boost.Wave use my own virtual filesystem (to be able to include from archives etc that I have mounted)  
https://github.com/Devsh-Graphics-Programming/Nabla/blob/master/src/nbl/asset/utils/waveContext.h  
as the hooks, etc. were not flexible enough for that.  
  
> And if it's your own code using Wave as a library, can I get access to that?  
  
yes sure, check out example 05 in   
https://github.com/Devsh-Graphics-Programming/Nabla  
(make sure you do a non-recursive clone, via ssh, and use our cmake infrastructure to build).  
  
but undo the changes from this commit  
https://github.com/Devsh-Graphics-Programming/Nabla/commit/145fac521aba9c5f2d495be6aa29c457553f6d58  
  
There's also another bug that newlines after `#pragma` were missing.

---

## Comment 5

> Username: jefftrull  
> Created at: 2023-11-29 23:33:50 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832866002  

Re: the pragma newlines, we'd be very interested in seeing this bug. I wonder if #188 is related in any way - we made some changes for 1.84 there.

---

## Comment 6

> Username: jefftrull  
> Created at: 2023-11-29 23:58:34 UTC  
> Updated at: 2023-11-30 00:33:31 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1832884855  

OK actually I was able to reproduce using the wave driver. Try this option, which lists the defined macros:  
  
`wave -m - your_filename_here.cpp`  
  
This shows that only the first line of the macro definition makes it through. But why is that? It turns out there is an extra space following the second backslash - the backslash escapes the space, not the following newline :) So to Wave this is a two line macro definition ending in an unnecessarily escaped space character.  
  
Does fixing that resolve your issue?

---

## Comment 7

> Username: devshgraphicsprogramming  
> Created at: 2023-11-30 09:09:08 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1833364936  

> Re: the pragma newlines, we'd be very interested in seeing this bug. I wonder if #188 is related in any way - we made some changes for 1.84 there.  
  
it a different bug, instead of `#pragma\n` triggering a segfault, we have `#pragma region SOMETHING\n` being pasted into output without the terminating newline.

---

## Comment 8

> Username: devshgraphicsprogramming  
> Created at: 2023-11-30 09:18:47 UTC  
> Updated at: 2023-11-30 09:19:07 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1833379064  

> But why is that? It turns out there is an extra space following the second backslash - the backslash escapes the space, not the following newline :) So to Wave this is a two line macro definition ending in an unnecessarily escaped space character.  
  
> Does fixing that resolve your issue?  
  
Are you saying its a "bug" or a "feature"?  
  
Because I used to feed all these macros to Clang/DXC and the preprocessor there doesn't complain.  
  
Also why were the `/ ` pasted whenever the macro got used?

---

## Comment 9

> Username: hkaiser  
> Created at: 2023-11-30 13:14:34 UTC  
> Updated at: 2023-11-30 13:54:40 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1833765516  

> > But why is that? It turns out there is an extra space following the second backslash - the backslash escapes the space, not the following newline :) So to Wave this is a two line macro definition ending in an unnecessarily escaped space character.  
>   
> > Does fixing that resolve your issue?  
>   
> Are you saying its a "bug" or a "feature"?  
>   
> Because I used to feed all these macros to Clang/DXC and the preprocessor there doesn't complain.  
  
Apparently this has been changed recently (after C++20). Before C++23, the standard mandated that a `'\'` was to be treated as an end-of-line continuation only if it is the last character on a line.   
  
Now it says (https://eel.is/c++draft/lex.phases#1.2):  
  
> Each sequence of a backslash character (`'\'`) immediately followed by zero or more whitespace characters other than new-line followed by a new-line character is deleted, splicing physical source lines to form logical source lines[.](https://eel.is/c++draft/lex.phases#1.2.sentence-2)  
  
This needs fixing on our end requiring introducing a C++23 mode.

---

## Comment 10

> Username: jefftrull  
> Created at: 2023-11-30 17:21:41 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1834207723  

I'll add the C++23 behavior to our list - we have several such features to implement. @devshgraphicsprogramming would you be willing to file a bug on the pragma issue you are experiencing?

---

## Comment 11

> Username: jefftrull  
> Created at: 2023-11-30 17:34:52 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1834248738  

Re: the pasted `\`, as far as I can tell that happens only when the following character is a space. Is that incorrect? I'm not sure.

---

## Comment 12

> Username: devshgraphicsprogramming  
> Created at: 2023-11-30 17:46:11 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1834265935  

> Re: the pasted `\`, as far as I can tell that happens only when the following character is a space. Is that incorrect? I'm not sure.  
  
in bad.txt we have this:  
```cpp  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int16_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int32_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<int64_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint16_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint32_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<uint64_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<bool4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float16_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float32_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t1> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t2x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t2x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t2x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t3x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t3x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t3x2> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t4x4> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t4x3> : integral_constant<uint32_t,__COUNTER__> {}; } \     namespace nbl { namespace hlsl {     namespace impl { template<> struct typeid_t<float64_t4x2> : integral_constant<uint32_t,__COUNTER__> {}; } \  
```  
  
whenever the macro was used, there was only one `\` with a space after it.

---

## Comment 13

> Username: jefftrull  
> Created at: 2023-11-30 17:49:56 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1834271435  

That's right - the source of those `\` characters is the same thing each time - the line with a trailing space, i.e.  
  
`namespace impl { template<> struct typeid_t<T> : integral_constant<uint32_t,__COUNTER__> {}; } \ ` <<<-- always this one  
  
You shouldn't see them anymore if you remove the space. What I'm wondering is what the "correct" C++20 behavior is for this case.

---

## Comment 14

> Username: hkaiser  
> Created at: 2023-11-30 17:52:09 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1834274529  

> That's right - the source of those `\` characters is the same thing each time - the line with a trailing space, i.e.  
>   
> `namespace impl { template<> struct typeid_t<T> : integral_constant<uint32_t,__COUNTER__> {}; } \ ` <<<-- always this one  
>   
> You shouldn't see them anymore if you remove the space. What I'm wondering is what the "correct" C++20 behavior is for this case.  
  
C++20 still has the 'old' behavior (no whitespace allowed after the `'\'`).

---

## Comment 15

> Username: jefftrull  
> Created at: 2023-12-09 18:46:08 UTC  
> Url: https://github.com/boostorg/wave/issues/195#issuecomment-1848611897  

Since we have a feature request issue entered for this, and a workaround (removing the extra space), I think this should probably be closed. @devshgraphicsprogramming if you want to, please enter a separate issue for the pragma problem you brought up.
