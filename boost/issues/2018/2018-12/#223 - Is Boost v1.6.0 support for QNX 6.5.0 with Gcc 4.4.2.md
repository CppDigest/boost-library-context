# #223 - Is Boost v1.6.0 support for QNX 6.5.0 with Gcc 4.4.2 [Closed]

> Username: vprasad  
> Created at: 2018-12-06 00:49:06 UTC  
> Updated at: 2018-12-06 01:20:39 UTC  
> Closed at: 2018-12-06 01:20:39 UTC  
> Url: https://github.com/boostorg/boost/issues/223  

Hi ,  
I wanted to compile boost for qnx 6.5.0 with gcc4.4.2.  
Is it compiling supported with the agove qnx and Gcc version. ?  
Do you have the pre build binaries for this.  
  
Thanks,

---

## Comment 1

> Username: mclow  
> Created at: 2018-12-06 01:20:39 UTC  
> Url: https://github.com/boostorg/boost/issues/223#issuecomment-444713052  

The best place to ask questions like this is on the boost-users mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost-users).  But in any case:  
  
* Boost 1.6.0 was released many, many years ago (we're about to release 1.69.0).  
* For current releases, the only pre-built binaries we ship are for Windows.  
* The list of supported compilers for the 1.68.0 (the last release, see https://www.boost.org/users/history/version_1_68_0.html) list the supported compilers. GCC 4.4.2 is *not* on that list, though 4.4.0 and 4.4.7 are. (We only support the compilers that we have people who are willing to test against)
