# #220 - iOS build OK  But I null [Closed]

> Username: zgbilltalent  
> Created at: 2021-11-29 03:15:52 UTC  
> Updated at: 2021-11-30 13:20:54 UTC  
> Closed at: 2021-11-30 13:20:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220  

When I Use the Version of 1.77.0 in my app ，It will crash ，  
  
  
 boost::filesystem::recursive_directory_iterator endIter;  
    for (boost::filesystem::recursive_directory_iterator iter(p); iter != endIter; iter++) {  
      if (boost::filesystem::is_directory(*iter)) {  
          std::cout << "key:EEEEEE "  << std::endl;  
          std::cout << iter->path().string() << std::endl;  
          std::cout << "key:EEEEEE "  << std::endl;  
      } else {  
          std::cout << "is a file" << std::endl;  
          std::cout << "key:EEEEEE "  << std::endl;  
          std::cout << iter->path().string() << std::endl;  
          std::cout << "key:EEEEEE "  << std::endl;  
      }  
    }

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-29 11:31:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-981549577  

Can you provide more details? What's the backtrace? What does the filesystem tree look like?

---

## Comment 2

> Username: zgbilltalent  
> Created at: 2021-11-30 02:24:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982219901  

What should I do next? Re compile or something with my using way ?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-30 10:55:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982521263  

I have no idea. You still haven't provided any details. Try debugging the problem on your end.

---

## Comment 4

> Username: zgbilltalent  
> Created at: 2021-11-30 11:16:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982538031  

How Can I give you the code ?zip?

---

## Comment 5

> Username: zgbilltalent  
> Created at: 2021-11-30 11:24:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982544451  

![1638271381699](https://user-images.githubusercontent.com/5159607/144038736-0fad1a12-f0ad-4687-ad41-d782ea18904f.jpg)  
please Check it pic I upload

---

## Comment 6

> Username: zgbilltalent  
> Created at: 2021-11-30 11:24:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982545207  

What shuld I give it to you ？

---

## Comment 7

> Username: zgbilltalent  
> Created at: 2021-11-30 12:10:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982576585  

https://github.com/faithfracture/Apple-Boost-BuildScript this is the shell which I used to compile  
version 1.77.0

---

## Comment 8

> Username: Lastique  
> Created at: 2021-11-30 12:23:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982586283  

From the backtrace it looks like the directory iterator is located at an invalid location (or `this` is an invalid pointer, which is more likely). This looks like either a compiler bug (miscompiled code) or some sort of ABI mismatch between your code and Boost compiled libraries.  
  
In your Boost build scripts you're defining `BOOST_AC_USE_PTHREADS` and `BOOST_SP_USE_PTHREADS` [here](https://github.com/faithfracture/Apple-Boost-BuildScript/blob/7e30473420624ef98ff2713193a7a4d7aba6bfb3/boost.sh#L1587). Are you defining the same macros when you're compiling your own code? These macros affect ABI of atomic reference counters in smart pointers, including `intrusive_ptr` that is used in `directory_iterator` internally, and must be defined (or not defined) consistently when building Boost and your code.  
  
Note that you should not need to define these macros. Boost.SmartPtr should detect an use a better implementation for atomic reference counters.

---

## Comment 9

> Username: zgbilltalent  
> Created at: 2021-11-30 13:01:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982614232  

https://github.com/faithfracture/Apple-Boost-BuildScript/issues/50 I think this is the problem

---

## Comment 10

> Username: Lastique  
> Created at: 2021-11-30 13:20:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/220#issuecomment-982630343  

Closing as a duplicate of https://github.com/boostorg/filesystem/issues/147 then.
