# #41 - sprintf deprecated [Closed]

> Username: sdarwin  
> Created at: 2025-08-21 14:30:47 UTC  
> Updated at: 2025-08-21 16:51:59 UTC  
> Closed at: 2025-08-21 16:51:59 UTC  
> Url: https://github.com/boostorg/container_hash/issues/41  

`sprintf` is deprecated on macos clang.  
  
Notice these files. Or do these statements only execute conditionally, so it is not an issue?  
  
```  
libs/container_hash/benchmark/char_seq.cpp:                sprintf( buffer, "prefix_%llu_suffix", k );  
libs/container_hash/benchmark/char_seq.cpp:                sprintf( buffer, "{%u}", static_cast<unsigned>( k ) );  
libs/container_hash/benchmark/unordered.cpp:                sprintf( buffer, "prefix_%llu_suffix", k );  
libs/container_hash/benchmark/unordered.cpp:                sprintf( buffer, "{%u}", static_cast<unsigned>( k ) );  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2025-08-21 14:41:07 UTC  
> Url: https://github.com/boostorg/container_hash/issues/41#issuecomment-3210894369  

The benchmarks aren't compiled as part of the test suite. I probably need to add them to it.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-08-21 15:41:41 UTC  
> Url: https://github.com/boostorg/container_hash/issues/41#issuecomment-3211148418  

No, adding them to the test suite isn't going to work, they require C++17 and don't compile cleanly under the strict warnings required by test/Jamfile.
