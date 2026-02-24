# #9 Added metafunction for testing a property tag [Merged]

> Username: eldiener  
> Created at: 2020-01-18 16:54:00 UTC  
> Updated at: 2020-01-25 08:03:32 UTC  
> Merged at: 2020-01-25 08:03:32 UTC  
> Closed at: 2020-01-25 08:03:32 UTC  
> Url: https://github.com/boostorg/function_types/pull/9  

I have a need for testing whether a possibly compound property tag has a built-in individual property tag in its composition. Although the function_types library has a template for creating compound property tags, it has never had a documented metafunction for testing whether an single property tag is part of the composition of the compound property tag. So I created such a metafunction, along with a set of convenient individual metafunctioins for the built-in property tags, created some tests, and added some documentation for these new metafunctions. These additions do not affect the current function_types library functionality in any way, but may be helpful to other programmers who work with property tags in function_types.

---
