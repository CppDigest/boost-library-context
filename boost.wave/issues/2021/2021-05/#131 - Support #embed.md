# #131 - Support #embed [Open]

> Username: jefftrull  
> Created at: 2021-05-02 18:50:53 UTC  
> Updated at: 2023-10-20 14:14:03 UTC  
> Url: https://github.com/boostorg/wave/issues/131  

[This mechanism](https://thephd.github.io/_vendor/future_cxx/papers/C%20-%20embed.html) for creating arrays from binary files at compile time looks likely to land in C++23

---

## Comment 1

> Username: hkaiser  
> Created at: 2021-05-02 19:27:50 UTC  
> Url: https://github.com/boostorg/wave/issues/131#issuecomment-830859123  

Adding support for `#embed` would definitely be a Good Thing(tm), however it most likely wouldn't give the benefit of using the same by a 'real' compiler. Wave would have to translate the content of the file into a comma-separated sequence of bytes, while 'real' compilers could load things into memory directly without representing the content as a character sequence just to convert it back into the binary representation that will be embedded in the object file..

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-07-22 22:12:51 UTC  
> Url: https://github.com/boostorg/wave/issues/131#issuecomment-1192973084  

Just landed in C23, writeup here https://thephd.dev/finally-embed-in-c23

---

## Comment 3

> Username: jefftrull  
> Created at: 2023-10-10 00:04:31 UTC  
> Updated at: 2023-10-10 00:07:11 UTC  
> Url: https://github.com/boostorg/wave/issues/131#issuecomment-1754082446  

A PR for this has been submitted to Clang now https://github.com/llvm/llvm-project/pull/68620

---

## Comment 4

> Username: jefftrull  
> Created at: 2023-10-20 14:14:03 UTC  
> Url: https://github.com/boostorg/wave/issues/131#issuecomment-1772819576  

Some thoughts on implementation [here](https://thephd.dev/implementing-embed-c-and-c++)
