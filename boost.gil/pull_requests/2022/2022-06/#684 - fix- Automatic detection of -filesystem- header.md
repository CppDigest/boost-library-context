# #684 fix: Automatic detection of <filesystem> header [Merged]

> Username: marco-langer  
> Created at: 2022-06-09 06:55:08 UTC  
> Updated at: 2022-06-25 21:25:20 UTC  
> Merged at: 2022-06-25 21:25:13 UTC  
> Closed at: 2022-06-25 21:25:13 UTC  
> Url: https://github.com/boostorg/gil/pull/684  

### Description  
  
PR #636 has added detection for the `<filesystem>` header, but now examples using the IO extensions can't be compiled with C++11 or C++14 anymore. This PR fixes this issue.  
  
### References  
  
Compiling one of the examples, e.g.  
  
```  
g++ x_gradient.cpp -std=c++11 -ljpeg  
```  
results in  
```  
In file included from ../../boost/libs/gil/include/boost/gil/io/path_spec.hpp:11,  
                 from ../../boost/libs/gil/include/boost/gil/io/get_read_device.hpp:13,  
                 from ../../boost/libs/gil/include/boost/gil/io/get_reader.hpp:11,  
                 from ../../boost/libs/gil/include/boost/gil/extension/io/jpeg/read.hpp:18,  
                 from ../../boost/libs/gil/include/boost/gil/extension/io/jpeg.hpp:11,  
                 from ../../boost/libs/gil/example/x_gradient.cpp:9:  
../../boost/libs/gil/include/boost/gil/io/detail/filesystem.hpp:55:29: error: ‘filesystem’ is not a namespace-name  
   55 | namespace filesystem = std::filesystem;  
....  
```  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2022-06-09 07:13:36 UTC  
> Url: https://github.com/boostorg/gil/pull/684#issuecomment-1150757287  

Nice, thanks!

---

## Comment 2

> Username: marco-langer  
> Created_at: 2022-06-09 07:20:51 UTC  
> Url: https://github.com/boostorg/gil/pull/684#issuecomment-1150763802  

I don't understand why it wasn't caught by the tests, are we using `BOOST_GIL_IO_USE_BOOST_FILESYSTEM` in the test runners?

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-06-09 07:51:27 UTC  
> Updated_at: 2022-06-09 07:51:54 UTC  
> Url: https://github.com/boostorg/gil/pull/684#issuecomment-1150791327  

@marco-langer   
  
Good question. I don't understand why tests did not catch it. I don't see `BOOST_GIL_IO_USE_BOOST_FILESYSTEM` defined anywhere.   
  
The GHA basically run `b2 test`:  
https://github.com/boostorg/gil/blob/9ecdb876b33e06725b3b81e9db3ad0d9f60c58f7/.github/workflows/ci.yml#L114  
  
which only runs minimal IO test called `simple_all_formats`:  
https://github.com/boostorg/gil/blob/9ecdb876b33e06725b3b81e9db3ad0d9f60c58f7/test/extension/io/Jamfile#L36-L44  
  
So, for C++11 and C++14 none of these `#define`-s should happen  
https://github.com/boostorg/gil/blob/9ecdb876b33e06725b3b81e9db3ad0d9f60c58f7/include/boost/gil/io/detail/filesystem.hpp#L14-L21  
  
and for `simple_all_formats` test build for C++11 and C++14, this case should happen:  
https://github.com/boostorg/gil/blob/9ecdb876b33e06725b3b81e9db3ad0d9f60c58f7/include/boost/gil/io/detail/filesystem.hpp#L36-L38

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2022-06-25 21:23:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/684#pullrequestreview-1019353345  

LGTM. Thanks!  
  
And the CI-s are green now

---
