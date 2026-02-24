# #130 - boost 1.60 build issue with Intel C++ compiler for MacOS [Closed]

> Username: mibintc  
> Created at: 2016-05-31 18:23:20 UTC  
> Updated at: 2016-05-31 19:20:23 UTC  
> Closed at: 2016-05-31 19:02:25 UTC  
> Url: https://github.com/boostorg/build/issues/130  

When building boost 1.60 with Intel C++ 16.0 compiler for MacOS, users have encountered a 'failed Jamfile' issue which is due to incorrect arguments passed to assembler. This problem can be corrected by patching libs/context/build/Jamfile.v2 to add a rule for the Intel toolset (I work for Intel on the Intel C++ compiler).  Thanks and best regards, Melanie Blower  
  
Here's the symptom:  
Jamfile</Users/xx/software/boost_1_60_0/libs/context/build>.gas64 ../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/make_x86_64_sysv_macho_gas.o  
clang: error: unsupported option '--64'  
clang: error: no input files  
  
```  
cpp -x assembler-with-cpp "libs/context/src/asm/make_x86_64_sysv_macho_gas.S" | as --64 -o "../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/make_x86_64_sysv_macho_gas.o"  
```  
  
...failed Jamfile</Users/xx/software/boost_1_60_0/libs/context/build>.gas64 ../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/make_x86_64_sysv_macho_gas.o...  
Jamfile</Users/xx/software/boost_1_60_0/libs/context/build>.gas64 ../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/jump_x86_64_sysv_macho_gas.o  
clang: error: unsupported option '--64'  
clang: error: no input files  
  
```  
cpp -x assembler-with-cpp "libs/context/src/asm/jump_x86_64_sysv_macho_gas.S" | as --64 -o "../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/jump_x86_64_sysv_macho_gas.o"  
```  
  
...failed Jamfile</Users/xx/software/boost_1_60_0/libs/context/build>.gas64 ../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/asm/jump_x86_64_sysv_macho_gas.o...  
common.mkdir ../boost_build/boost/bin.v2/libs/context/build/intel-darwin-16.0/release/threading-multi/posix

---

## Comment 1

> Username: mibintc  
> Created at: 2016-05-31 18:40:39 UTC  
> Updated at: 2016-05-31 18:41:25 UTC  
> Url: https://github.com/boostorg/build/issues/130#issuecomment-222781222  

[build_issues_130-dif.txt](https://github.com/boostorg/build/files/291882/build_issues_130-dif.txt)  
  
The attached patch has passed boost regression testing with the Intel C++ compiler for MacOS and corrects the problem  
  
Thanks and regards, Melanie Blower

---

## Comment 2

> Username: swatanabe  
> Created at: 2016-05-31 19:01:22 UTC  
> Url: https://github.com/boostorg/build/issues/130#issuecomment-222787185  

AMDG  
  
On 05/31/2016 12:40 PM, Melanie Blower wrote:  
  
> [build_issues_130-dif.txt](https://github.com/boostorg/build/files/291882/build_issues_130-dif.txt)  
>   
> The attached patch has passed boost regression testing and corrects the problem  
  
This patch should go to Boost.Context.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: mibintc  
> Created at: 2016-05-31 19:03:10 UTC  
> Url: https://github.com/boostorg/build/issues/130#issuecomment-222787716  

I'll close this - i submitted an issue here: https://svn.boost.org/trac/boost/ticket/12242  
is that correct? please let me know if I'm wrong  
Thank you Steven.  
--Melanie

---

## Comment 4

> Username: grafikrobot  
> Created at: 2016-05-31 19:10:46 UTC  
> Url: https://github.com/boostorg/build/issues/130#issuecomment-222789796  

You will likely get a faster response if you submit a PR directly to the context library https://github.com/boostorg/context/pulls. Which you can do, in this case, by directly editing the file in GitHUB (assuming you have a github account).

---

## Comment 5

> Username: mibintc  
> Created at: 2016-05-31 19:20:23 UTC  
> Url: https://github.com/boostorg/build/issues/130#issuecomment-222792262  

Thanks for explaining how to do that.  I have created a PR. --Melanie  
  
From: Rene Rivera [mailto:notifications@github.com]   
Sent: Tuesday, May 31, 2016 3:11 PM  
To: boostorg/build  
Cc: Blower, Melanie; State change  
Subject: Re: [boostorg/build] boost 1.60 build issue with Intel C++ compiler for MacOS (#130)  
  
You will likely get a faster response if you submit a PR directly to the context library https://github.com/boostorg/context/pulls. Which you can do, in this case, by directly editing the file in GitHUB (assuming you have a github account).  
—  
You are receiving this because you modified the open/close state.  
Reply to this email directly, view it on GitHub, or mute the thread.
