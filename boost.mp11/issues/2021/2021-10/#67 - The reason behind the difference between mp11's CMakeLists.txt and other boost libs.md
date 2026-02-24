# #67 - The reason behind the difference between mp11's CMakeLists.txt and other boost libs [Closed]

> Username: fatal-err  
> Created at: 2021-10-07 16:47:35 UTC  
> Updated at: 2021-10-28 18:41:09 UTC  
> Closed at: 2021-10-28 18:41:09 UTC  
> Url: https://github.com/boostorg/mp11/issues/67  

In my journey of writing a cmake script to download and make available the boost library which you can see [here](https://github.com/the-moisrex/webpp/blob/dev/cmake/packages/boost.cmake), I saw that mp11's CMakeLists.txt doesn't follow the other boost libraries' pattern and I wonder why?  
  
I know boost/cmake is not ready yet, but am I on the right path here?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-10-07 18:55:53 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938067258  

Mp11's `CMakeLists.txt` file predates the Boost-wide initiative of adding CMake support, so it supports standalone uses. It should be compatible with the Boost CMake superproject, although I'll be replacing the `CMakeLists.txt` file with a "correct" one in a future release.  
  
Does it create problems for you at the moment?

---

## Comment 2

> Username: fatal-err  
> Created at: 2021-10-07 19:24:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938088247  

I specialized it to fix the issue, but the issue was that the boost/cmake's `boost_install_target` function which is inside the `BoostInstall.cmake` file of boost/cmake, doesn't work on mp11.  
  
My project is not production ready so it's not urgent, at least not for me.  
  
Thanks for the details.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-10-07 21:21:58 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938165150  

But that's what Mp11 uses, here: https://github.com/boostorg/mp11/blob/28a9ff62f1ca29da4733e1386400fa3f5d03a74c/CMakeLists.txt#L60

---

## Comment 4

> Username: fatal-err  
> Created at: 2021-10-08 04:21:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938331304  

Well that's the thing; other boost libs' CMakeLists.txt don't seem to be using any of the `boost/cmake`'s functions thus when I use them myself, it's being done twice and that's the issue.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-10-08 04:29:49 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938334631  

OK, but what's the actual problem you're getting? `boost_install` is already being called twice on Mp11 when using the Boost superproject, but it handles this case (or is supposed to, anyway.)

---

## Comment 6

> Username: pdimov  
> Created at: 2021-10-08 04:32:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938335481  

Wait, ignore that, I think I know what's the problem. I'll look into fixing that. :-)

---

## Comment 7

> Username: pdimov  
> Created at: 2021-10-08 13:47:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-938657504  

I think this commit: https://github.com/boostorg/mp11/commit/c4e2259c8fd1d973a06670918b5fccc1f1f64c64 will fix your issue.  
  
For the dependency list in your script, you can use [the dependency report](https://pdimov.github.io/boostdep-report/) for [Asio](https://pdimov.github.io/boostdep-report/boost-1.77.0/asio.html) or [Boostdep](https://boost.org/tools/boostdep) with the `--brief` option (`boostdep --brief asio`).

---

## Comment 8

> Username: fatal-err  
> Created at: 2021-10-09 06:25:49 UTC  
> Url: https://github.com/boostorg/mp11/issues/67#issuecomment-939238060  

Yes it did fix the issue. Thanks for all the help.
