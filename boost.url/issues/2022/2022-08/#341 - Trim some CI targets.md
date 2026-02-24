# #341 - Trim some CI targets [Closed]

> Username: vinniefalco  
> Created at: 2022-08-05 02:17:37 UTC  
> Updated at: 2022-08-05 21:47:05 UTC  
> Closed at: 2022-08-05 21:47:05 UTC  
> Url: https://github.com/boostorg/url/issues/341  

In the interest of moving things along, we should consider not to test C++11 with _every_ compiler version. Instead, we say that we will test (for example):  
  
* C++11 up to gcc-7  
* C++11 up to clang-8  
  
and then maybe  
  
* C++14 up to gcc-9  
* C++14 up to clang-10  
  
Now I just made up these version cutoffs, we might try to find a balance with making the jobs even in terms of how much work they do, so that a couple of jobs dont take a whole lot longer than the others.  
  
Whatever we do, we must document it in comments at the top of the drone file.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-05 17:49:16 UTC  
> Url: https://github.com/boostorg/url/issues/341#issuecomment-1206701489  

Yes. That's would be good. There is an exponential number of tests and many are not very useful. In a way, this is just a fractional factorial experiment. I'll have a look into that.
