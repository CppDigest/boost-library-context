# #209 Fix boostorg/core#208: Improve null_deleter documentation [Closed]

> Username: vinniefalco  
> Created at: 2025-12-08 19:06:12 UTC  
> Updated at: 2025-12-09 14:46:22 UTC  
> Closed at: 2025-12-09 09:54:32 UTC  
> Url: https://github.com/boostorg/core/pull/209  

Add example showing use of null_deleter with static objects. The documentation now includes two examples:  
1. Using null_deleter with external objects (e.g., std::cout)  
2. Using null_deleter with static objects  
  
Closes #208

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-12-08 20:46:00 UTC  
> Url: https://github.com/boostorg/core/pull/209#issuecomment-3628935279  

Again, I don't see how it improves the documentation as it doesn't add anything substantial. `std::cout` already covers the use case where the pointed object doesn't need to be deallocated.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2025-12-09 01:25:24 UTC  
> Url: https://github.com/boostorg/core/pull/209#issuecomment-3629782084  

It may seem obvious to you that the two examples are substantially identical yet for a beginner, seeing the variable defined explicitly and then used with the aliasing constructor may provide a level of clarity that you already enjoy thanks to your considerable experience.  
  
That said, there is even room for a third example. One that demonstrates that `null_deleter` is not even necessary in C++11 as one could simply pass an empty lambda: `[](T*){ }` and avoid the need to include anything at all.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-12-09 09:54:32 UTC  
> Updated_at: 2025-12-09 14:46:22 UTC  
> Url: https://github.com/boostorg/core/pull/209#issuecomment-3631335304  

> It may seem obvious to you that the two examples are substantially identical yet for a beginner, seeing the variable defined explicitly and then used with the aliasing constructor may provide a level of clarity that you already enjoy thanks to your considerable experience.  
  
I don't see what confusion the variable declaration is supposed to clarify.  
  
> That said, there is even room for a third example. One that demonstrates that `null_deleter` is not even necessary in C++11 as one could simply pass an empty lambda: `[](T*){ }` and avoid the need to include anything at all.  
  
First, that example would be irrelevant to `null_deleter` as it would not illustrate its usage. Second, that example would not be applicable when the type of the deleter need to be specified explicitly, e.g. in `std::unique_ptr`.  
  
I don't mind having more examples in the docs, but the examples have to illustrate different usages of the tool, or its different features. Having multiple essentially the same examples is not improving the documentation, it is adding bloat which deters the reader by the sheer amount of reading. The documentation should be succinct yet informative.  
  
You could replace the existing example with a better one to illustrate its usage. However, I don't think your proposed example is better either as it contains underspecified elements such as the `impl` class, is more abstract, and at the same time it is larger than the original one. So I think, keeping the current example is best, as it shows the real use case of the tool (notably, this was the actual reason why I wrote the deleter in Boost.Log).  
  
Thanks for the issue and the PR. I did add a note in the text that the deleter can be used when the object doesn't need to be deleted. That was already of implied by the rest of the text, but maybe mentioning this explicitly is better.

---
