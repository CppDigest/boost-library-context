# #337 Adds Karatsuba sqrt. [Merged]

> Username: jzmaddock  
> Created at: 2021-06-18 19:06:24 UTC  
> Updated at: 2021-06-27 08:40:57 UTC  
> Merged at: 2021-06-27 08:40:57 UTC  
> Closed at: 2021-06-27 08:40:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/337  

Integration testing for https://github.com/boostorg/multiprecision/pull/328

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-06-19 11:27:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/337#issuecomment-864393578  

I just ran some other tests on indpendent projects using this branch. All tests successful at various digit counts including 24, 32, 48, 64, 96, 128, 256, 512, 1024, 2048, 8192, 12288, 20480, 32768, 65536. All tests running in multithreaded environment. Results OK.  
  
I also tested within the context of `cpp_bin_float` intensively at 10,000 decmial digits, corresponding to roughly 60,000 binary digits. All tests OK within multithreaded environment.  
  
I found some level 4 warnings at MSVC. Harmless, but should be removed.  
  
As for speed, particularly at higher digit counts, we are seeing improvements of --- not percent, but factors --- such as factors of 2, 3, 5, 10 improvement.  
  
Awesome!

---
