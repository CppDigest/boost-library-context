# #158 - No downsides to `BOOST_USE_VALGRIND` documented? [Closed]

> Username: alexweej  
> Created at: 2021-01-20 13:40:14 UTC  
> Updated at: 2021-06-01 15:40:22 UTC  
> Closed at: 2021-06-01 15:40:22 UTC  
> Url: https://github.com/boostorg/context/issues/158  

We've started using cooperative multitasking via Boost.Context but still need to use Valgrind. [The documentation for BOOST_USE_VALGRIND](https://www.boost.org/doc/libs/1_75_0/libs/context/doc/html/context/stack/valgrind.html) doesn't really mention any downsides of enabling this build and configuration option. I figure there must be some, otherwise it'd be on by default.  
  
What are the downsides? Is there runtime overhead? Is it just not particularly well tested?  
  
Thanks

---

## Comment 1

> Username: olk  
> Created at: 2021-06-01 15:40:22 UTC  
> Url: https://github.com/boostorg/context/issues/158#issuecomment-852228446  

runtime overhead because additional code must be compiled in  
usually you use valgrind aware code for inspecting bugs
