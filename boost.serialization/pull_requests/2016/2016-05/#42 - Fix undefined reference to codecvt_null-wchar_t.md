# #42 Fix undefined reference to codecvt_null<wchar_t> [Closed]

> Username: xantares  
> Created at: 2016-05-14 14:17:20 UTC  
> Updated at: 2018-01-02 20:55:54 UTC  
> Closed at: 2017-05-28 19:37:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/42  

See https://svn.boost.org/trac/boost/ticket/12205

---

## Comment 1

> Username: sav-ix  
> Created_at: 2016-05-22 03:04:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-220811750  

Hi.  
Patch work only for **Release+shared** builds. It doesn't fix build error for **Debug+shared** builds.  
Tested using Mingw-w64.

---

## Comment 2

> Username: xantares  
> Created_at: 2016-05-22 17:49:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-220845875  

@sav-ix, indeded, I also get undefined refs to singleton_module::is_locked() when linking libboost_serialization-mt-d.dll.a in debug mode, nothing indicates it's related though.

---

## Comment 3

> Username: starius  
> Created_at: 2016-10-23 17:19:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-255601089  

Fails for me with the following error:  
  
```  
bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/  
threading-multi/xml_wiarchive.o:xml_wiarchive.cpp:(.rdata$.refptr._ZTVN5boost7archive6detail18utf8_codecvt_facet  
E[.refptr._ZTVN5boost7archive6detail18utf8_codecvt_facetE]+0x0): undefined reference to `vtable for boost::archi  
ve::detail::utf8_codecvt_facet'  
collect2: error: ld returned 1 exit status  
    "x86_64-w64-mingw32.shared-g++"   "-Wl,--out-implib,bin.v2/libs/serialization/build/gcc-mingw-mxe/release/ab  
i-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_wserialization-mt.dll.a" -o "bi  
n.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/th  
reading-multi/libboost_wserialization-mt.dll" -Wl,-h -Wl,libboost_wserialization-mt.dll -shared -Wl,--start-grou  
p "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win  
32/threading-multi/basic_text_wiprimitive.o" "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binar  
y-format-pe/target-os-windows/threadapi-win32/threading-multi/basic_text_woprimitive.o" "bin.v2/libs/serializati  
on/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/text_wi  
archive.o" "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/thre  
adapi-win32/threading-multi/text_woarchive.o" "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/bina  
ry-format-pe/target-os-windows/threadapi-win32/threading-multi/xml_wgrammar.o" "bin.v2/libs/serialization/build/  
gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/xml_wiarchive.o"  
 "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win3  
2/threading-multi/xml_woarchive.o" "bin.v2/libs/serialization/build/gcc-mingw-mxe/release/abi-ms/binary-format-p  
e/target-os-windows/threadapi-win32/threading-multi/codecvt_null.o" "bin.v2/libs/serialization/build/gcc-mingw-m  
xe/release/abi-ms/binary-format-pe/target-os-windows/threadapi-win32/threading-multi/libboost_serialization-mt.d  
ll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -mthreads -m64  
```  
  
I am trying to update boost from 1.60 to 1.62 in [MXE](http://mxe.cc).  
Bug in MXE: https://github.com/mxe/mxe/pull/1549

---

## Comment 4

> Username: xantares  
> Created_at: 2016-10-24 10:05:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-255700008  

@starius I use this patch for 1.62 too, it works provided that you compile in release mode

---

## Comment 5

> Username: starius  
> Created_at: 2016-10-24 22:56:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-255889299  

I also build in release mode (variant=release), but get an error.  
Whole build command: https://git.io/vPx7a  
  
How does release/debug mode affect the failure?

---

## Comment 6

> Username: xantares  
> Created_at: 2016-10-25 08:57:50 UTC  
> Updated_at: 2016-10-25 09:00:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-255977623  

@starius, A wild guess is that in release mode the compiler tries to optimize more aggressively and inline symbols get optimized away (I don't really know what I'm saying).  
In archlinux we use gcc 6.2, with these `cxxflags="-std=c++11 -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4"` what are your flags, vanilla ?  
here's our script: https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=mingw-w64-boost

---

## Comment 7

> Username: starius  
> Created_at: 2016-10-25 18:23:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-256123848  

If it is true, then it is not a real fix, but a workaround. What is a real  
reason of the failure and how to solve it?  
  
25 окт. 2016 г. 9:58 пользователь "xantares" notifications@github.com  
написал:  
  
> @starius https://github.com/starius, A wild guess is that in release  
> mode the compiler tries to optimize more aggressively and inline symbols  
> get optimized away.  
> In archlinux we use gcc 6.2, with these cxxflags="-std=c++11 -O2 -g -pipe  
> -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4"  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/serialization/pull/42#issuecomment-255977623,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AA4KW5pbGzGmLXu4vnl8ul-0ybASbOVUks5q3cSPgaJpZM4IenSj  
> .

---

## Comment 8

> Username: xantares  
> Created_at: 2016-10-25 19:00:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-256142043  

: ) if only I knew

---

## Comment 9

> Username: Frederick888  
> Created_at: 2017-01-23 11:10:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-274459975  

I'm building from Debian and I have exactly the same issue as @starius . Any updates?

---

## Comment 10

> Username: xantares  
> Created_at: 2017-01-23 11:14:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-274460873  

nope, patch is still needed with boost 1.63

---

## Comment 11

> Username: Frederick888  
> Created_at: 2017-01-23 11:26:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-274463177  

@xantares I meant my compilation failed for the same reason. But I found that I could build with your patch using `link=static`.

---

## Comment 12

> Username: xantares  
> Created_at: 2017-01-23 12:38:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-274478044  

are you sure you need to patch when disabling shared libs ?

---

## Comment 13

> Username: Frederick888  
> Created_at: 2017-01-23 14:18:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-274498978  

@xantares No. I'm rushing to build a bunch of apps and didn't have time to carefully check each of them.

---

## Comment 14

> Username: starius  
> Created_at: 2017-02-03 01:49:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-277142732  

It failed for me only for shared targets. Static worked well.  
  
But the patch didn't help. I have updated gcc and mingw and have not retested if the patch works.

---

## Comment 15

> Username: sav-ix  
> Created_at: 2017-05-28 19:50:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-304536070  

Hello, Robert,  
Does closing this issue mean  
- it already fixed,  
OR  
- it won't be fixed?  
  
Thank you.

---

## Comment 16

> Username: robertramey  
> Created_at: 2017-05-28 20:56:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-304539754  

it means I'm pretty sure it's fixed.  I've checked it into the develop branch.  Merge to master still pending.  I passes on my machine and most of the machines in the Boost test matrix.  I've still got failures there but I don't think they are related to this.

---

## Comment 17

> Username: xantares  
> Created_at: 2017-05-29 17:36:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-304705663  

what commit fixed it ?

---

## Comment 18

> Username: robertramey  
> Created_at: 2017-05-29 17:44:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-304706706  

I don't remember it was some time ago - but no tests are failing due to this:  
  
http://www.boost.org/development/tests/master/developer/serialization.html

---

## Comment 19

> Username: sav-ix  
> Created_at: 2017-07-17 12:24:26 UTC  
> Updated_at: 2017-08-22 12:12:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-315740884  

Just tested [Boost-1.65.0_beta1_rc2](https://dl.bintray.com/boostorg/beta/1.65.0.beta.1/source/) build using mingw-w64 with <Debug|Release> Shared configurations and error reproduced in both cases.  
Robert, could you re-open this ticket, since error still persist?  
  
> I passes on my machine and most of the machines in the Boost test matrix  
  
AIUI, Boost test matrix tests *static* libraries only. Thus it didn't catch this error.  
Could you build Boost.Serialization using mingw-w64 with `link=shared` key?  
  
UPDATE: reproduced for [Boost-1.65.0](https://dl.bintray.com/boostorg/release/1.65.0/source/boost_1_65_0.tar.gz) builds too.

---

## Comment 20

> Username: rwols  
> Created_at: 2017-07-17 12:26:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-315741387  

Pretty off-topic, I don't know where else to put this, but why is there no issues page?

---

## Comment 21

> Username: xantares  
> Created_at: 2017-07-31 21:02:05 UTC  
> Updated_at: 2017-07-31 21:03:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-319195450  

@robertramey the issue is still present in boost 1.65 beta 1 rc2 that includes your latest commit, could you reopen ?  
@rwols seems they disabled the issues sections I guess we have to deal with https://svn.boost.org/trac/boost/ticket/12205

---

## Comment 22

> Username: sav-ix  
> Created_at: 2017-12-24 11:42:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-353779710  

Error not reproduced after moving to 1.66.0. Tested for builds using mingw-w64 with <Debug|Release> Shared configurations. Thank you for fix, Robert!

---

## Comment 23

> Username: xantares  
> Created_at: 2018-01-02 20:55:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/42#issuecomment-354873044  

Same here, thanks @robertramey.

---
