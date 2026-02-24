# #2081 - Can not read file name with umlauts in boost::beast::http::file_body [Closed]

> Username: emmenlau  
> Created at: 2020-09-07 14:45:12 UTC  
> Updated at: 2024-06-06 05:34:20 UTC  
> Closed at: 2022-07-29 10:25:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2081  

Thanks again for the very cool library, first and foremost! We have a minor issue with umlauts (low priority).  
  
### Version of Beast  
  
```  
#define BOOST_BEAST_VERSION 290  
```  
  
### Steps necessary to reproduce the problem  
  
My code is based on the advanced_server_flex.cpp example, and my problem appears specifically around line  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L179:  
```cpp  
    // Attempt to open the file  
    beast::error_code ec;  
    http::file_body::value_type body;  
    body.open(path.c_str(), beast::file_mode::scan, ec);  
  
    // Handle the case where the file doesn't exist  
    if(ec == beast::errc::no_such_file_or_directory)  //< This is true for us when the file name has umlauts.  
```  
  
We used this code successfully on multiple platforms (MSVC, Ubuntu and MacOSX) for about a year now. However as soon as we started adding `clang-cl` to the list of compilers, the webserver can no longer read files that have Umlauts in the file name.  
  
We tested with a file named  
```  
üöälaut.txt  
```  
that is _not_ found on Windows when using clang-cl, whereas all other platforms/compilers work fine.  
  
### All relevant compiler information  
  
The error comes with Visual Studio 2019 x64 16.7.1 with the Microsoft-supplied `clang-cl` version 10.0.0. Using the Microsoft-Compiler and not clang-cl, everything works.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-07 14:49:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688372634  

Thanks for reporting this @emmenlau ,  
  
Before I start looking into this, do you have the impression that this is a URL parsing problem or a file access problem?

---

## Comment 2

> Username: emmenlau  
> Created at: 2020-09-07 15:02:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688379791  

> Thanks for reporting this @emmenlau ,  
>   
> Before I start looking into this, do you have the impression that this is a URL parsing problem or a file access problem?  
  
Thanks a lot @madmongo1 ! Admittedly I don't know.  
  
The file was created with boost::filesystem on all platforms, and can be correctly queried with boost::filesystem on all platforms. Also, Windows Explorer shows the file correctly. So the least I can guess, its not a problem with the file not existing or being inaccessible due to permissions.  
  
The server also prints the file name correctly when I print the std::string filename:  
```  
handle_request(): The resource 'C:/data/stable-tmp-MSVC-Haswell-7-x64-cl19.27.29111_clang10.0.0/Debug/public/üöälaut.txt' was not found.  
```  
  
Does that help? Let me know anything I can do to further narrow this down...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-07 15:29:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688394426  

probably having to do with `fopen` and utf-8 encoded paths or something like that

---

## Comment 4

> Username: emmenlau  
> Created at: 2020-09-07 15:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688399529  

> probably having to do with `fopen` and utf-8 encoded paths or something like that  
  
Yes that sounds about right. We encode paths as UTF-8, and now that you mention it, I remember that boost::filesystem required a default conversion for Windows to be set. Something along the lines of  
```  
boost::filesystem::path::imbue(   
    std::locale( std::locale(), new std::codecvt_utf8_utf16<wchar_t>() ) );  
```  
  
Its interesting however that this used to work with boost::beast and the MSVC compilers out of the box, whereas clang-cl seems to treat it differently.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-07 15:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688399931  

ugh, facets....

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-09-07 15:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688400632  

`beast::file` is _supposed_ to handle utf-8 encoded filenames, but I never implemented it.

---

## Comment 7

> Username: emmenlau  
> Created at: 2020-09-07 16:25:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688422756  

> `beast::file` is _supposed_ to handle utf-8 encoded filenames, but I never implemented it.  
  
Hehe, fair enough! Do you think this will be on the road map for the next 3-6 months? Or is it rather unlikely to happen? The issue has no urgency for us, but it would be nice to know that this will come at some point...

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-09-07 16:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688433866  

> Do you think this will be on the road map for the next 3-6 months?  
  
@madmongo1 ?

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-09-07 18:34:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688468055  

We have a user with an unimplemented use case. My pride will not allow me to leave this wrong unrighted.  
It’s going to the top of the development queue.

---

## Comment 10

> Username: emmenlau  
> Created at: 2020-09-07 21:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688513553  

> It’s going to the top of the development queue.  
  
Hahaha noooo! Its really not so urgent! :-) But guys, thanks again for the quick response!

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-09-08 15:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-688967641  

Hi @emmenlau, would you mind checking whether adding a manifest to your executable solves this?  
  
It will still need a code solution for older versions of windows, but I am interested to know whether this works:  
  
https://docs.microsoft.com/en-us/windows/uwp/design/globalizing/use-utf8-code-page

---

## Comment 12

> Username: emmenlau  
> Created at: 2020-09-09 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-689720289  

> Hi @emmenlau, would you mind checking whether adding a manifest to your executable solves this?  
>   
> It will still need a code solution for older versions of windows, but I am interested to know whether this works:  
>   
> https://docs.microsoft.com/en-us/windows/uwp/design/globalizing/use-utf8-code-page  
  
This is awesome, I was not aware of it! Sadly, we run all our builds and tests on Windows 7 so I can't test it (yet). But this is a very interesting new insight for me. Nice work from Microsoft.

---

## Comment 13

> Username: madmongo1  
> Created at: 2020-09-10 14:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-690316090  

Hi @emmenlau ,  
  
Are you able to give me the sequence of bytes you think you are getting for `"üöälaut.txt"` ?  
  
I have performed a conversion online to : `{ 0xc3, 0xbc, 0xc3, 0xb6, 0xc3, 0xa4, 0x6c, 0x61, 0x75, 0x74, 0x2e , 0x74, 0x78, 0x74 };`  
  
But this is not converting correctly to UTF-16 via `::MultiByteToWideChar`

---

## Comment 14

> Username: emmenlau  
> Created at: 2020-09-10 14:23:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-690323840  

Argh! I should be more helpful in this, but sadly I don't know how to get this. Originally the sequence is encoded in UTF-8, but this is not what you want, no? Then we use this UTF-8 string in boost::filesystem to create test files that boost::beast should serve. The client sends the UTF-8 string via a boost::beast-based HTTP connection to the server. I assume what we receive server-side is still UTF-8, and when I print it, it looks correct. So far, also everything works fine with any compiler I tried.  
  
The error seems to come when I pass this (probably UTF-8 encoded) string to `body.open(path.c_str(), beast::file_mode::scan, ec);`: While the file reading works with all other compilers, it fails with `clang-cl`, claiming the file would not exist. But I have no way of tracking how clang translates/encodes/parses the string, and also no way of knowing what exactly fails in opening the file.  
  
I can only say that in boost::filesystem, everything works when we `imbue` the automatic conversion with `std::codecvt_utf8_utf16<wchar_t>()`.  
  
Cutting a long story short, what can I do to be more helpful? Should I try to step into this with the debugger to see if I can find more details? Or does `body.open()` accept UTF-16 so I can try to perform a conversion manually?

---

## Comment 15

> Username: madmongo1  
> Created at: 2020-09-11 09:01:44 UTC  
> Updated at: 2020-09-11 09:03:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-690969371  

Thanks for responding @emmenlau   
  
What would really help me track this down is:  
  
1. a simple 10-line program that demonstrates this causing a problem on clang-cl but not on msvc or gcc.  
2. idiot-proof instructions on how to compile and link it :-)  
  
This way I can replicate without too much effort and focus my attention on what is going wrong. It's beginning to sound as if we're not detecting Windows properly when compiling on clang (note 1), but I'd like to be able to prove it.  
  
note 1: Because we do have code to detect that we're on Windows and convert UTF-8 to Windows Unicode internally.  
  
R

---

## Comment 16

> Username: emmenlau  
> Created at: 2020-10-05 15:10:35 UTC  
> Updated at: 2020-10-05 15:15:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-703696008  

Dear @madmongo1 ,  
  
sorry for the super late reply, I did not have time to continue with the issue. Now I've just created a relatively small test-case that inhibits the aforementioned problem. Its based on gtest, boost::filesystem to create a test file, and boost::beast to open the test file. The test works for me in our CI-system on Linux, macOS and the Microsoft Compiler. It only fails (as expected) when using ClangCl on MSVC with error message `handle_request(): The resource 'üöälaut.txt' was not found`.  
  
Can you try to reproduce the problem on your side? Or do you spot any obvious mistakes in the test? Feel free to throw out gtest if you need, its not required.  
  
Here the file for download:  
[BoostBeastClangCLUmlautsTest.txt](https://github.com/boostorg/beast/files/5328189/BoostBeastClangCLUmlautsTest.txt)

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-10-05 15:34:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-703710679  

Fantastic. Thank you.

---

## Comment 18

> Username: emmenlau  
> Created at: 2021-07-26 08:18:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-886484393  

Hi all, just a friendly ping on this issue with Ümlauts... :-)

---

## Comment 19

> Username: madmongo1  
> Created at: 2021-07-26 11:33:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-886622933  

Thanks. Will take a look.

---

## Comment 20

> Username: emmenlau  
> Created at: 2021-09-14 19:35:53 UTC  
> Updated at: 2021-09-14 19:42:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919455963  

Finally I can maybe shed some light on this issue. When using the clang-cl compiler, it is currently detected by `boost/config.hpp` as clang, not MSVC. Therefore the define `BOOST_MSVC` is missing. This brings along the problem that boost::beast does not use the `wchar` APIs for the Windows file interface.  
  
The relevant line is https://github.com/boostorg/config/blob/3fa057a8c33b81e6f0e7d1951b97389a2dffc2a5/include/boost/config/detail/select_compiler_config.hpp#L42  
  
I'm not really sure how to proceed there, as this seems to have significant impact on large parts of boost.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2021-09-14 19:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919461771  

@pdimov what do we do

---

## Comment 22

> Username: emmenlau  
> Created at: 2021-09-14 19:54:54 UTC  
> Updated at: 2021-09-14 19:55:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919467721  

Progressing a bit further with the investigation, maybe boost::config is correct to include the header `clang.hpp`. I come to this idea because `clang.hpp` has some support for clang-cl. It uses `_MSC_VER` in a few places. If boost::config is correct here, then maybe `BOOST_MSVC` is not the only way to detect compilation based on Visual Studio? Another possibility seems to be something like  
```cpp  
#if defined(BOOST_CLANG) && defined(_MSC_VER)  
```

---

## Comment 23

> Username: vinniefalco  
> Created at: 2021-09-14 20:58:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919508001  

or just `#ifdef _MSC_VER`

---

## Comment 24

> Username: pdimov  
> Created at: 2021-09-14 22:12:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919549665  

> @pdimov what do we do  
  
fix the code to not be broken

---

## Comment 25

> Username: pdimov  
> Created at: 2021-09-14 22:16:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919551460  

https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/include/boost/beast/core/file_win32.hpp#L16-L20  
  
This uses the Win32 file implementation only on MSVC, but there exist other compilers targeting the Windows platform. It should probably detect the platform and not the specific compiler, by using `_WIN32`, for instance.

---

## Comment 26

> Username: pdimov  
> Created at: 2021-09-14 22:18:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919552312  

`_MSC_VER` will also work for `clang-cl` and e.g. Borland, but it will not pick up Cygwin GCC and MinGW GCC.

---

## Comment 27

> Username: emmenlau  
> Created at: 2021-09-15 08:21:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919805718  

> https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/include/boost/beast/core/file_win32.hpp#L16-L20  
>   
> This uses the Win32 file implementation only on MSVC, but there exist other compilers targeting the Windows platform. It should probably detect the platform and not the specific compiler, by using `_WIN32`, for instance.  
  
Oh I did not even see `BOOST_BEAST_USE_WIN32_FILE` before. In the places I stepped through file operations are bound to `BOOST_MSVC` and not to `BOOST_BEAST_USE_WIN32_FILE`. So maybe the following should use `BOOST_BEAST_USE_WIN32_FILE` where currently `BOOST_MSVC` is used?  
  
https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/core/impl/file_stdio.ipp#L84-L91

---

## Comment 28

> Username: pdimov  
> Created at: 2021-09-15 10:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-919901408  

Yes, all these should also check for whether the Microsoft C library is used, and not the specific compiler. `_MSC_VER` is probably the right thing here.  
  
I'm not sure when the stdio implementation will be used under Windows though, instead of the Win32 one. When BOOST_BEAST_USE_WIN32_FILE is defined by the user to 0, perhaps.

---

## Comment 29

> Username: emmenlau  
> Created at: 2021-12-07 16:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-988090520  

I just wanted to kindly ping here to see if this is still on the horizon? I'm a bit scared now when using beast with clang, due to the possibly wrong defines that may break the code in arbitrary ways... but clang is such a great compiler front-end on MSVC nowadays...

---

## Comment 30

> Username: madmongo1  
> Created at: 2021-12-07 16:43:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-988094895  

No progress has been made I'm afraid. Do you have a set of defines that causes the correct behaviour with the clang front end?

---

## Comment 31

> Username: pdimov  
> Created at: 2021-12-07 18:08:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-988156167  

https://github.com/boostorg/beast/pull/2353 should fix the problem in the `file_stdio` implementation.

---

## Comment 32

> Username: pdimov  
> Created at: 2021-12-07 18:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-988161080  

https://github.com/boostorg/beast/pull/2354 will enable `file_win32` on Windows, instead of just on MSVC.

---

## Comment 33

> Username: emmenlau  
> Created at: 2021-12-08 13:17:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-988807153  

> Do you have a set of defines that causes the correct behaviour with the clang front end?  
  
No, sadly not. I'm actually quite scared to mess with the boost defines for clang-cl. I know that in many aspects, clang-cl emulates MSVC behavior. But in other aspects it behaves like clang. And possibly it can do both at the same time.  
  
Do you know if there is a "compiler support team" in boost that can look into cleanly integrating clang-cl first? I understand that currently clang-cl is seeing the clang header instead of the MSVC header, and this could be a problem in itself...

---

## Comment 34

> Username: sehe  
> Created at: 2022-05-10 04:46:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1121927684  

Late bit of feedback out of the field from a [related SO question](https://stackoverflow.com/questions/71907767/how-to-create-a-response-from-a-file-that-has-cjk-filename/72093070?noredirect=1#comment127531785_72093070):   
  
> Sorry, I already replaced the file body with a string body in my codes. To change the design, I may have to modify some other codes. Maybe I will try it in other projects in the future. Anyway, I believe a better approach is to have a path object, instead of a c-string, as the argument of the ctor of the file body, which is how Boost.Iostreams works, e.g. the ctor of `boost::iostreams::mapped_file`. If a `boost::filesystem::path` is the argument, calling `boost::nowide::nowide_filesystem()` in advance is enough to deal with the CJK problem.

---

## Comment 35

> Username: emmenlau  
> Created at: 2022-07-28 18:39:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1198505039  

Hey @pdimov , are there further updates on this? Would be great to have full umlaut-support in beast. Thanks a lot for the kind consideration!

---

## Comment 36

> Username: vinniefalco  
> Created at: 2022-07-28 18:47:40 UTC  
> Updated at: 2022-07-28 18:47:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1198511874  

I thought this was already fixed? The file algorithms should accept utf-8 encoded input.

---

## Comment 37

> Username: pdimov  
> Created at: 2022-07-28 19:14:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1198536052  

My two PRs seem to have been applied, so the issue should be fixed. Does it still not work?

---

## Comment 38

> Username: emmenlau  
> Created at: 2022-07-28 19:43:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1198559352  

Sorry, I missed that this is a full fix! I'll test boost 1.79.0 right now and let you know how it goes!

---

## Comment 39

> Username: emmenlau  
> Created at: 2022-07-29 10:25:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2081#issuecomment-1199116963  

Hooray, issue is fixed. Thanks, awesome!
