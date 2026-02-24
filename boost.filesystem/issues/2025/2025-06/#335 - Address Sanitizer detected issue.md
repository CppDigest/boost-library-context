# #335 - Address Sanitizer detected issue [Closed]

> Username: cmorve-te  
> Created at: 2025-06-20 13:30:09 UTC  
> Updated at: 2025-06-21 01:02:57 UTC  
> Closed at: 2025-06-21 01:02:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/335  

When building with -fsanitize=address [-fsanitize=pointer-compare](https://gcc.gnu.org/onlinedocs/gcc-15.1.0/gcc/Instrumentation-Options.html#index-fsanitize_003dpointer-compare), and running with [detect_invalid_pointer_pairs=2](https://github.com/google/sanitizers/wiki/AddressSanitizerFlags#run-time-flags) (or detect_invalid_pointer_pairs=1), Address Sanitizer complains about https://github.com/boostorg/filesystem/blob/boost-1.88.0/src/path.cpp#L481  
  
Apparently it's unspecified behaviour. In 7.6.9 the standard says something about what pointer comparison are defined, and ends up saying "Otherwise, neither pointer is required to compare greater than the other.". And it doesn't say anything regarding pointers to different objects.  
  
[Apparently you can use std:less](https://stackoverflow.com/a/51699576) to check for overlapping in a specified way.
