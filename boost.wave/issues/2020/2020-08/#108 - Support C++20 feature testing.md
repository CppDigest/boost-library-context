# #108 - Support C++20 feature testing [Open]

> Username: jefftrull  
> Created at: 2020-08-05 20:22:44 UTC  
> Updated at: 2020-08-06 05:07:03 UTC  
> Url: https://github.com/boostorg/wave/issues/108  

C++20 introduces a special predefined macro `__has_cpp_attribute()` along with a set of predefined language feature macros, all described [here](https://en.cppreference.com/w/cpp/feature_test). I think that at minimum `__has_cpp_attribute()` should be defined in C++20 mode, even if it always evaluates to `true`.  
  
What's less clear to me is whether, and how, the return values should be user-programmable (e.g. for emulating a specific compiler version).

---

## Comment 1

> Username: hkaiser  
> Created at: 2020-08-05 21:17:27 UTC  
> Url: https://github.com/boostorg/wave/issues/108#issuecomment-669515676  

I'm not sure if this is something to be supported by Wave. There are arguments both ways.  
  
On one hand, the output Wave produces should be consumable by any other compiler. And that compiler should be able to handle the feature macros depending on its own implementations. For this it might be better to leave the feature macros that identify compiler (i.e. language) features alone and pass them through to the output untouched.   
  
On the other hand, feature macros that are explicitly defined by the standard library or the compiler could be handled by Wave as any other macro, which would require for `__has_cpp_attribute()` to be recognized by Wave in order to properly resolve preprocessor conditional expressions.  
  
In the end, in order to achieve accurate preprocessing, the process would have to be targetable to a particular platform and/or compiler (in the same way as necessary for code that is referring to predefined macros like `__clang__` and similar). This would require to be able to predefine those language attribute-tokens (like `carries_dependency`) through the command line.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-08-06 05:07:03 UTC  
> Url: https://github.com/boostorg/wave/issues/108#issuecomment-669697127  

What if there was a separate mechanism, via an API on the Context object, for adding attributes? Something similar to `add_macro_definition` - a separate table for use by `__has_cpp_attribute` that would allow people to emulate specific compilers...
