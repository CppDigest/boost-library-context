# #314 Remove no longer necessary command line options from build/Jamfile, u… [Merged]

> Username: pdimov  
> Created at: 2024-04-11 00:13:50 UTC  
> Updated at: 2024-06-08 17:52:15 UTC  
> Merged at: 2024-06-08 17:52:15 UTC  
> Closed at: 2024-06-08 17:52:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/314  

…til/test.jam  
  
`-fvisibility=hidden`, `-fvisibility-inlines-hidden` are now supplied automatically by b2, because the default for the `visibility` feature when building Boost is `hidden`.  
  
`-ftemplate-depth-255` (itself an obsolete spelling for `-ftemplate-depth=255`) is no longer necessary, because the default as of C++11 is 1024 (so 255 actually decreases it).  
  
In addition, none of these options are supported by Clang for Windows (clang-cl), so removing them avoids warnings when building with it.

---
