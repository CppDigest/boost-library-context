# #27 - Issue with NVCC [Closed]

> Username: raffienficiaud  
> Created at: 2019-12-12 21:19:29 UTC  
> Updated at: 2021-02-15 20:55:58 UTC  
> Closed at: 2020-12-14 16:18:53 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27  

Hi,  
  
I have the following ticket: https://github.com/boostorg/test/issues/220 . In particular there is this section  
  
```  
If I change line 83 of $BOOST_ROOT/include/boost/preprocessor/config/config.hpp from  
  
if defined __GCCXML__ || defined __CUDACC__ || defined __PATHSCALE__ || defined __DMC__ || defined __CODEGEARC__ || defined __BORLANDC__ || defined __MWERKS__ || ( defined __SUNPRO_CC && __SUNPRO_CC < 0x5120 ) || defined __HP_aCC && !defined __EDG__ || defined __MRC__ || defined __SC__ || (defined(__PGI) && !defined(__EDG__))  
  
to  
  
if defined __GCCXML__ || defined __PATHSCALE__ || defined __DMC__ || defined __CODEGEARC__ || defined __BORLANDC__ || defined __MWERKS__ || ( defined __SUNPRO_CC && __SUNPRO_CC < 0x5120 ) || defined __HP_aCC && !defined __EDG__ || defined __MRC__ || defined __SC__ || (defined(__PGI) && !defined(__EDG__))  
  
then the program works fine.  
```  
  
Is it correct to assume that the fix is on the preprocessor library? the users are reporting that the fix in c436de6 does not solve the issue.  
  
Thanks!

---

## Comment 1

> Username: eldiener  
> Created at: 2019-12-12 23:16:09 UTC  
> Updated at: 2019-12-12 23:18:28 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-565229571  

The fix you cite was specifically for clang compiling CUDA code rather than for NVCC, or any other compiler, compiling CUDA code. I can remove the CUDA check and let the compiler itself determine whether variadic macros are supported or not in Boost PP. However I remember doing this in the past and it created havoc. The problem is that there is no way I know of testing other compilers with CUDA as a number of those compilers have no b2 toolset asociated with them and, even when they do have b2 toolsets, testing CUDA has always been incredibly problematical for me. Some compilers don't work with this release of CUDA while other compilers don't work with some other release of CUDA. Some compilers can run CUDA code with the compilers while other compilers are called from some CUDA implementation themselves. Explanations by compiler vendors about their interaction with CUDA are often wrong or incomplete. Personally I find the whole CUDA thing a mess, and I have never seen any direction in the Boost users mailing list of how to actually setup tests using b2 for compilers and CUDA. If I can not test Boost PP with b2 with some compiler configuration I have taken the most conservative path in the Boost PP configuration code, which in the case of variadic macros is to just turn them off. I did make the change for clang compiling CUDA code, but even there I had to open a bug report with clang, which has just been ignored by them. Sorry to sound ticked off but the whole CUDA thing is just a great annoyance to me.

---

## Comment 2

> Username: eldiener  
> Created at: 2019-12-12 23:36:47 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-565235431  

If the failure in Boost Test was because Boost Test uses variadic macros with Boost PP you can automatically turn on variadic macros in Boost PP by:  
  
`#define BOOST_PP_VARIADICS 1`  
  
before including Boost PP header files. If the failure in Boost Test was not because Boost Test uses variadic macros can you isolate the failure there to a particular Boost PP macro that is failing with NVCC ?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-12-12 23:44:18 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-565237356  

Hi,  
  
I understand the frustration. There is an approach like the one you cite with `BOOST_PP_VARIADICS` such as https://github.com/boostorg/test/blob/develop/include/boost/test/detail/config.hpp#L131 . I guess in this case I can just assume that `nvcc` compilers can run variadic macros and check with the users.  
  
Thanks for the hint, maybe something to bring in in the ML.

---

## Comment 4

> Username: eldiener  
> Created at: 2019-12-14 18:36:37 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-565741367  

There is a previous issue that says that NVCC when compiling CUDA does not work when variadic macros are bring used. See https://github.com/boostorg/preprocessor/issues/15. I really wish I could test NVCC, but there is no toolset for it for bjam. If you do decide to bring this up in the ML, please ask if a toolset for NVCC can be created for Boost Build, else it is hopeless to try to test NVCC. I did make the change in 'develop' so that only NVCC compiling CUDA has variadic macros turned off by default, while if other compilers compile CUDA I let the compiler itself determne the default variadic macro setting.

---

## Comment 5

> Username: eldiener  
> Created at: 2020-09-19 00:28:01 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-695137212  

I have changed the library to assume variadic macro sypport for any compiler using Boost PP. I will be uploading this change to the 'develop' branch as soon as I have finished testing it.

---

## Comment 6

> Username: eldiener  
> Created at: 2020-12-14 16:18:53 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-744548744  

The change for assuming variadic macro support is now in Boost 1.75, so I will close this issue.

---

## Comment 7

> Username: mobereng  
> Created at: 2021-02-15 18:25:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-779387898  

Hi @eldiener I'm currently having a similar issue related with nvcc compilation  
  
![image](https://user-images.githubusercontent.com/69315094/107981912-734c6d00-6f99-11eb-814e-0704d6e8559d.png)  
  
Not sure if you know how to deal with it  
  
CUDA Version: 11.2  
Boost Version: 1.75.0  
Windows Environment

---

## Comment 8

> Username: eldiener  
> Created at: 2021-02-15 18:37:05 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-779392187  

Looks like the same problem. The fix will go in the next release, but if you want to patch the Boost source for 1.75 you can go to 'include/boost/preprocessor/config/config.hpp' , line 86, search for `|| (defined __NVCC__ && defined __CUDACC__)` and remove it.  
  
This all comes about because NVCC has decided, even when compiling CUDA files, to emulate the VC++ default non-standard preprocessor. VC++ has a new C++ standard preprocessor as of VS2019, but evidently NVCC, as well as some other compilers, are still emulating the non-standard one.

---

## Comment 9

> Username: mobereng  
> Created at: 2021-02-15 20:55:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/27#issuecomment-779443890  

Hi @eldiener thanks for the help, solved with the latest development branch version. Ref [`Issue 37`](https://github.com/boostorg/preprocessor/issues/37)
