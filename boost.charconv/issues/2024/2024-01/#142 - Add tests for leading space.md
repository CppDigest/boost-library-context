# #142 - Add tests for leading space [Closed]

> Username: Flamefire  
> Created at: 2024-01-26 18:36:19 UTC  
> Updated at: 2024-01-29 08:49:48 UTC  
> Closed at: 2024-01-29 08:49:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/142  

A difference of `std::from_chars` to `std::strto*` (for both integer and float parsers) is  
  
>  leading whitespace is not ignored.   
  
I don't see that in the "invalid input" tests. I guess it makes sense to test that too.  
  
And the documentation should cover what the input strings are expected to be. I've found the various format descriptions for floats in an extra section, but none for int. It only states stuff in terms of "matching the pattern", but for ints I wasn't able to find which pattern is used.
