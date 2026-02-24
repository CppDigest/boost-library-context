# #71 - Latest commit `01b4f62` breaks Interprocess, Unordered [Closed]

> Username: cmazakas  
> Created at: 2021-12-29 23:37:03 UTC  
> Updated at: 2022-01-03 16:07:34 UTC  
> Closed at: 2022-01-03 16:07:33 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71  

Link to commit: https://github.com/boostorg/intrusive/commit/01b4f6264c866f7d59240273aec9146456ec9537  
  
I'm maintaining Unordered and this broke our CI.   
  
Running locally using VS 2019 and clang-win 12.0.0:  
```console  
C:\Users\cmaza\cpp\boost-root\libs\unordered>b2 -aq -j3 test//scoped_allocator cxxstd=14 variant=release address-model=32 toolset=clang-win embed-manifest-via=linker  
```  
we see:  
```console  
====== BEGIN OUTPUT ======  
Compiler: Clang version 12.0.0  
Library: Dinkumware standard library version 650  
__cplusplus: 201402  
  
BOOST_UNORDERED_HAVE_PIECEWISE_CONSTRUCT: 1  
BOOST_UNORDERED_EMPLACE_LIMIT: 10  
BOOST_UNORDERED_CXX11_CONSTRUCTION: 1  
  
Running scoped_allocator  
  
EXIT STATUS: -1073741819  
====== END OUTPUT ======  
```  
  
I don't have a stacktrace at the moment but the easiest way of replicating this is to simply update all the submodules and then try running Unordered's and/or Interprocess' test suite.  
  
This issue does not happen when using commit https://github.com/boostorg/intrusive/commit/56291fafe4e2decbb8ea8d060711e0c5a13d56ff

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-30 08:59:25 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1002933264  

But according to the dependency report:  
  
https://pdimov.github.io/boostdep-report/develop/unordered.html  
  
unordered does not depend on intrusive, that doesn't make sene...

---

## Comment 2

> Username: cmazakas  
> Created at: 2021-12-30 15:11:53 UTC  
> Updated at: 2021-12-30 16:26:35 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1003070131  

I'm sorry, I should've been clearer. I filed this issue yesterday at the end of the day and I was tired.  
  
We have a `scoped_allocator_adaptor` test for Unordered that uses shared memory via Interprocess and Interprocess in turn depends on Intrusive. [Instantiating this allocator](https://github.com/boostorg/unordered/blob/81e7e4dd81caf3bdfa40149fcfb703b80e2345b2/test/unordered/scoped_allocator.cpp#L74-L75) is hard crashing for us in release mode with `toolset=clang-win` 12.0.0.  
  
I was trying to convey that for us, our scoped allocator test will pass for the commits previous to https://github.com/boostorg/intrusive/commit/01b4f6264c866f7d59240273aec9146456ec9537 along with the `b2` command that recreates the crashing test in Unordered's test suite.  
  
Basically, Unordered's test suite has a dependency on Interprocess which in turn depends upon Intrusive and we're noticing that post commit-https://github.com/boostorg/intrusive/commit/01b4f6264c866f7d59240273aec9146456ec9537, Interprocess is now crashing which in turn causes our test to crash.  
  
I'm attaching a screen shot that shows the stacktrace for the crash from the test  
![image](https://user-images.githubusercontent.com/5543573/147763869-1ee5e201-dbd5-47d5-9e7a-596230d88c0a.png)  
.

---

## Comment 3

> Username: cmazakas  
> Created at: 2021-12-30 21:55:20 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1003193215  

So, I did some investigating and I've determined the culprit behind this downstream crash.  
  
It _seems_ like it's the changes in the `hook_traits.hpp` file. Namely, removing the `const&` qualification in:  
```cpp  
BOOST_INTRUSIVE_FORCEINLINE static pointer to_value_ptr(const node_ptr & n)  
```  
is sufficient to cause my tests in Unordered to break.  
  
I have no idea why the `clang-win` toolset seems to choke so hard on this. I might do a deeper dive into Interprocess as well and see what it's also doing.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-12-30 23:45:36 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1003214412  

It's possible that this is a bug in the codegen of clang-cl when targeting the 32 bit MS ABI. I think I remember it having problems with pass by value in some cases, although I may be misremembering.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2021-12-31 12:35:41 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1003366363  

Since I've seen the error was on the "replace_node" usage, and the code was not as robust as I wanted for corner cases, I've simplified that code in the rbtree_algorithm, in the latest commit (https://github.com/boostorg/interprocess/commit/3992b523d01487588825a98aed138b31cd1b3b48)  
  
Let me know if this makes clang-cl happier.

---

## Comment 6

> Username: cmazakas  
> Created at: 2022-01-03 16:07:33 UTC  
> Url: https://github.com/boostorg/intrusive/issues/71#issuecomment-1004193786  

Awesome, can confirm that your changes fixed the clang-cl bug! I will be closing this issue now.  
  
Thank you for prompt response, I really appreciate it.
