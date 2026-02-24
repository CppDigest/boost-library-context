# #176 - Visual Studio 2017.3: "Unknown compiler version" and other issues [Closed]

> Username: Klaim  
> Created at: 2017-08-15 02:00:33 UTC  
> Updated at: 2017-08-15 13:13:30 UTC  
> Closed at: 2017-08-15 12:09:22 UTC  
> Url: https://github.com/boostorg/config/issues/176  

https://github.com/boostorg/config/blob/b57df2a3c415e3c946a8441c2753a43784dea5b6/include/boost/config/compiler/visualc.hpp#L325  
  
This check fails on msvc-141 version provided by VS2017.3  (released today).   
  
_MSC_VER is 1911 in this version.

---

## Comment 1

> Username: Klaim  
> Created at: 2017-08-15 02:26:07 UTC  
> Url: https://github.com/boostorg/config/issues/176#issuecomment-322360213  

There are other errors like usage of std::unary_function in C++17 (it was removed)  
  
See attached build script and logs.  
  
[jlamotte_boost_1_65_vs2017-3_build_logs.zip](https://github.com/boostorg/config/files/1223976/jlamotte_boost_1_65_vs2017-3_build_logs.zip)

---

## Comment 2

> Username: gongminmin  
> Created at: 2017-08-15 04:27:04 UTC  
> Url: https://github.com/boostorg/config/issues/176#issuecomment-322374505  

Ha, I just submitted a PR to fix this. https://github.com/boostorg/config/pull/177.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2017-08-15 12:09:22 UTC  
> Url: https://github.com/boostorg/config/issues/176#issuecomment-322449803  

Since we have a PR I'm closing this down.  
  
I'm afraid any changes are too late for the next release (which is in RC3 at present), there will however, be at least some fixes for the C++17 issues, but we're still working through those.... the PR's are all submitted, but not all libraries have merged them through to master yet.  
  
Obviously it goes without saying, that if you don't define BOOST_ASSERT_CONFIG on the command line then you won't see the error message, the config may be slightly sub-optimal (not sure yet what if any new features are in this release) but it should work just fine.

---

## Comment 4

> Username: Klaim  
> Created at: 2017-08-15 13:13:30 UTC  
> Url: https://github.com/boostorg/config/issues/176#issuecomment-322462335  

Yes no problem, it's just spamming (though it would have been easier to understand it the message clarified that it was from boost.config and which header).  
  
The rest of the issues are basically std::binary_function usage (which I'm simply removing in my copy of boost)
