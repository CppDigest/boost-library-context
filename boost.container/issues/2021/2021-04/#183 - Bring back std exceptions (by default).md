# #183 - Bring back std exceptions (by default) [Open]

> Username: Flamefire  
> Created at: 2021-04-19 14:27:28 UTC  
> Updated at: 2021-05-03 15:38:57 UTC  
> Url: https://github.com/boostorg/container/issues/183  

https://github.com/boostorg/container/commit/d5a830434eb68b9fc65cfbc99572a96e4ee0f70a replace the usage of std:: exceptions by new, custom ones which resulted in quite a few critics, e.g. on reddit: https://www.reddit.com/r/cpp/comments/mskpfp/boost_c_libraries_v1760  
  
I have to agree and would suggest to revert that.  
  
I think that is an example of optimization guided by microbenchmarks which usually fail in practice. I guess the size savings from the preprocessed lines come from avoiding std::string (transitively) but binary size will increase, as stdexcept will very likely be included somewhere (already or later). So my point is that this optimizations very likely pessimizes usual usecases.  
  
Also there are implementation issues:  
- Needless `#ifdef BOOST_CONTAINER_USE_STD_EXCEPTIONS` checks in the `throw_*` functions  
- Likely visibility issues when throwing/catching such exceptions over DSO boundaries due to `-fvisibility=hidden` being the default (now) and no BOOST_SYMBOL_EXPORT or similar on the class  
- docs were forgotten to be updated: `<li>If BOOST_NO_EXCEPTIONS is NOT defined <code>std::logic_error(str)</code> is thrown.</li>`  
- Possibly silently breaking user code which needs to adapt to these changes  
  
So IMO the easiest is be to revert that. Or at least don't make it the default so it is opt-in for users who want this optimization.  
  
Note also that this may lead to ODR violations if one dependency defines it to use std exceptions and another does not. That is another reason to not do this.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-19 15:55:27 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-822580251  

First of all thanks for the report and implementation issues. I'll open a separate bug for those implementation issues.  
  
I take those Reddit posts with a grain of salt, as they were thrown just after reading the changelog. Reddit commenters are  
  
It was not an easy change and I meditated quite a bit about it trying to find alternatives to avoid include bloat. No boost build broke with the change, all my private libraries compiled without problem (other than a missing <string> include in some code that relied in indirect inclusion via Boost.Container, but I consider this a bug in that code)  
  
I certainly have projects that benefit for the change (simple classes that just need to use a container to store simple things), and users that already include <stdexcept> will end including very few additional lines (about 100 additional lines, when including <stdexcept> will bring thousands of lines)  
  
So the important question IMHO is how much code will break. I don't expect a lot of code will be broken, as experience shows very few people catch container exceptions (other than catching generic std::exception and calling "ex.what()"). I tried to minimize it deriving from std::exception and implemented the opt-out mechanism (I agree that the #define is not perfect but there is no other mechanism to avoid includes and other Boost libraries use #defines for features). Maybe a deprecation phase a release or two warning about the change would ease the transition, I take note on this.  
  
On the other hand, users have expressed they view Boost libraries as a "bloated". I certainly want to get rid of bloated includes for Boost.Container (dependencies on other Boost and std includes) and that will inevitably break some code. We have even compile/preprocess-time health surveys showing that Boost's vector is 3-4 times more "bloated" in lines of code (version 1.72) than standard vector (and it shouldn't):  
  
https://artificial-mind.net/projects/compile-health/  
  
I'm not against reverting the change, I just would like to evaluate the real impact. If a Boost 1.76.1 bug-fix point release is done, I'm willing to make std exceptions default again and start a deprecation phase.  
  
I'll let your report open so that we can bring additional information on the issue while Boost.Container users test the library.

---

## Comment 2

> Username: Flamefire  
> Created at: 2021-04-19 16:23:16 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-822601878  

Thanks for being open to discussion. Let me address some points:  
  
> I don't expect a lot of code will be broken, as experience shows very few people catch container exceptions  
  
I think people catch `out_of_range` or even the more generic `runtime_error` quite a way up the chain. That will be broken here  
  
> So the important question IMHO is how much code will break.  
  
I think the visibility is quite a big issue, although only on some systems. IIRC libstd++ uses the type name to catch types, but libc++ does not, so throwing one of the boost exceptions from a shared lib and catching it from outside will (likely) fail. But then again, only if people do catch specific exceptions and not the general std::exception   
  
> No boost build broke with the change, all my private libraries compiled without problem   
  
Who actually tests catching exceptions? ;-) And I did the "boost build"s include running tests or just compiling? If the latter I wouldn't expect problems, as syntactically it looks fine.  
  
> I agree that the #define is not perfect but there is no other mechanism to avoid includes and other Boost libraries use #defines for features  
  
And I don't like it for exactly the same reason. C++ has this huge footgun with ODR(-violations), and I recently got hit by something like this and it is a real PITA to debug this as it depends on so many factors. With the current situation, unless you have full control over all defines in all dependencies, you don't know if a Container-exception or a std-exception will be thrown. Even when you set the define for your TUs, the linker might choose another. I really think a few potential ms of compile speed savings are not worth risking this.  
  
> On the other hand, users have expressed they view Boost libraries as a "bloated"  
  
From my understanding this is exactly for the opposite reason as what you did here: Boost includes a lot of other Boost stuff which often already (e.g the C++11 stuff) is in the standard. For example Boost using Boost.TypeTraits over `<type_traits>`  leads to bloat because users very likely already include the latter somewhere. But here you added 100 additional lines AND types (visible in binary and symbols) where all C++ versions provide those already and users very likely get them already by including almost any other stdlib header. So I'd say the scenario where users don't have those already is the less common one, but that's only an (educated) guess.  
However I am surprised how heavy the stdexcept header is and that the vector header seemingly does not include it, although it throws range_error...  
  
Given that last thing makes me think, but in the end I'd take correctness and less code to write over compile time speed.  
  
Just my 5c, let's see what others may say and in the end it's of course your choice.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-05-03 02:50:12 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-830994753  

Throwing your own `bad_alloc` is ridiculous. `std::bad_alloc` is very often caught specifically by name, and is not even defined in `<stdexcept>`.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-05-03 03:05:43 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-830997226  

These throw functions are much better placed in a compiled library (which would avoid the stdexcept include as a side effect), but this would probably be an unacceptable regression for the people who are currently using the library header-only.  
  
The current approach avoids including stdexcept, but emits the exception vtables in every translation unit, which creates more work for the compiler and the linker, so the end result is probably a wash.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-05-03 03:16:21 UTC  
> Updated at: 2021-05-03 03:19:44 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-830999405  

See for instance the difference between the generated code in https://godbolt.org/z/4ThETbnM8 and https://godbolt.org/z/sE8P5s13h.  
  
When `BOOST_CONTAINER_USE_STD_EXCEPTIONS` is defined, according to CE's timings, the code actually compiles more quickly - 305 ms vs 352 ms for me. (Although those timings aren't very reliable.)

---

## Comment 6

> Username: pdimov  
> Created at: 2021-05-03 03:27:47 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831001482  

> and that the vector header seemingly does not include it, although it throws range_error...  
  
It probably calls `__throw_out_of_range`, which is defined in the compiled stdlib. This also prevents the throw from getting inlined, which is generally not what one wants.

---

## Comment 7

> Username: igaztanaga  
> Created at: 2021-05-03 08:48:38 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831119287  

throw_bad_alloc is really used in the library when detecting overflow conditions in allocate functions, because the "memory unavailable" throw condition will be performed by operator new. I've reviewed that libstdc++ does the same but libc++ uses __throw_length_error and MSVC STL uses _Throw_bad_array_new_length. So maybe throw_bad_alloc uses could be replaced with something like throw_out_of_range, but I'm not sure what a users expects. Existing practice does not seem consistent

---

## Comment 8

> Username: pdimov  
> Created at: 2021-05-03 14:46:39 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831308470  

My point was that _if_ you're going to throw `bad_alloc`, you should throw `std::bad_alloc` and not your own.  
  
If we're talking about e.g. `static_vector::push_back` exceeding capacity, I agree that `bad_alloc` is not quite right. `out_of_range` is wrong - it means an input argument is out of range.  
  
`length_error` means that the size would exceed `max_size()`, which is a good fit. `bad_array_new_length` means that `sizeof(T) * N` would overflow, which in the special case where `max_size()` is e.g. `SIZE_MAX / sizeof(T)` is kind of the same, but conceptually, `length_error` is more correct because `max_size()` can be arbitrary.

---

## Comment 9

> Username: Flamefire  
> Created at: 2021-05-03 14:53:35 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831312880  

I'd say `length_error` is an excellent fit:  
  
> Some implementations also throw std::length_error when push_back causes a reallocation that would exceed max_size, due to implicitly calling an equivalent of reserve(size()+1).   
  
https://en.cppreference.com/w/cpp/container/vector/push_back

---

## Comment 10

> Username: pdimov  
> Created at: 2021-05-03 15:07:18 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831321824  

`length_error` is explicitly documented as  
  
"If any operation would cause `size()` to exceed `max_­size()`, that operation throws an exception object of type `length_­error`."  
  
in http://eel.is/c++draft/string.require#1, but isn't consistently used by the other containers, although it probably should be.

---

## Comment 11

> Username: igaztanaga  
> Created at: 2021-05-03 15:38:57 UTC  
> Url: https://github.com/boostorg/container/issues/183#issuecomment-831347108  

> My point was that _if_ you're going to throw `bad_alloc`, you should throw `std::bad_alloc` and not your own.  
  
Yes,  I agree, I don't think I've expressed my point correctly. My point is that the library explicitly calls throw_bad_alloc only when overflow is detected. operator new, which is called in allocators to obtain memory, will throw std::bad_alloc. So maybe the library should get rid of the custom bad_alloc and throw other exception in the overflow detection case. So only std::bad_alloc would be thrown for out of memory conditions.
