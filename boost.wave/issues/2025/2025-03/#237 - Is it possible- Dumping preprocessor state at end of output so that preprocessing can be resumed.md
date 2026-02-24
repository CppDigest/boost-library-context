# #237 - Is it possible? Dumping preprocessor state at end of output so that preprocessing can be resumed? [Open]

> Username: devshgraphicsprogramming  
> Created at: 2025-03-10 11:05:32 UTC  
> Updated at: 2025-03-10 17:52:20 UTC  
> Url: https://github.com/boostorg/wave/issues/237  

## Context  
  
A bit of context to what we're doing.  
  
We have a header-only library of HLSL (shading language resembling C++), where we use Boost Preprocess (BOOST_PP) to get around some ecosystem issues (lack of variadic templates, injecting a bit of reflection, etc.) and most fundamentally the DirectX Shader Compiler being based off Clang 3.7 meaning the preprocessor implementation is really old (no __VA_OPT__ etc).  
  
The problem with shading languages is that nobody has really developed a linker, so every shader compilation is what one would call a "Unity Build".  
  
Now Boost.Wave isn't exactly a speed demon, and using BOOST_PP certainly doesn't help.  
  
We also spice it up even more, by exploiting the fact that HLSL is "almost syntax compatible" with C++ and we often write headers containing algorithms (lower bounds, upper bounds, FFTs) that compile both as C++ and HLSL.  
  
This Nabla HLSL STL is about 16000 LoC.  
  
Due to this insane combo, it loves to crash Intellisense because Intellisense's preprocessor isn't exactly C++20 compliant.  
  
## What we're trying to get  
  
We're trying to make the equivalent of PCH but for preprocessing.  
  
This is both to make the further HLSL compilation (including residual preprocessing) faster, but also to cut a break to language servers like Intellisense (especially important when code is shared with C++) and get an overall better source-browsing experience. For example see our nasty reflection-struct defining macro:  
```cpp  
NBL_HLSL_DEFINE_STRUCT((MyStruct),  
    ((a, float32_t))  
    ((b, int32_t))  
    ((c, int32_t2))  
);  
```  
  
Wouldn't it be nice to actually see the code that generates?  
  
## Question  
  
If it possible to dump all of Boost.Wave's context state in the form of `#define`s (and `#pragma`s if the state is more complex than can be represented with defines - but not that at least for headers we use guards) at the end of a file.  
  
And if yes, how?

---

## Comment 1

> Username: hkaiser  
> Created at: 2025-03-10 11:20:54 UTC  
> Updated at: 2025-03-10 11:23:47 UTC  
> Url: https://github.com/boostorg/wave/issues/237#issuecomment-2710251773  

The `wave::context` object can be serialized using Boost.Serialization (enabled with `-DBOOST_WAVE_SERIALIZATION=1`). Would that help?  
  
There is also the `--macronames` command line option for the wave tool (not sure if you're using that), see if that's what you need.  
  
Also, for the speed problem - try building Wave in non-thread-safe mode (`-DBOOST_WAVE_SUPPORT_THREADING=0`), that should make things better.

---

## Comment 2

> Username: devshgraphicsprogramming  
> Created at: 2025-03-10 13:31:03 UTC  
> Url: https://github.com/boostorg/wave/issues/237#issuecomment-2710606685  

I must admit I don't quite understand what `BOOST_WAVE_SUPPORT_THREADING` disabling does, would that cause two calls to Boost.Wave with two threads but different contexts and files to preprocess to be UB ?

---

## Comment 3

> Username: hkaiser  
> Created at: 2025-03-10 17:52:19 UTC  
> Url: https://github.com/boostorg/wave/issues/237#issuecomment-2711387780  

> I must admit I don't quite understand what `BOOST_WAVE_SUPPORT_THREADING` disabling does, would that cause two calls to Boost.Wave with two threads but different contexts and files to preprocess to be UB ?  
  
Calling two instances of `wave::context` from two respective (but different) threads is fine. DIsabling this makes concurrent calls into the same `wave::context` instance  unsafe.
