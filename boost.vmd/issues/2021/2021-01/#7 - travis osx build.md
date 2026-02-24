# #7 - travis osx build [Closed]

> Username: sdarwin  
> Created at: 2021-01-25 20:33:43 UTC  
> Updated at: 2021-01-25 22:37:54 UTC  
> Closed at: 2021-01-25 22:37:54 UTC  
> Url: https://github.com/boostorg/vmd/issues/7  

Hi,  
  
Although the build appears to be passing, the OSX travis job actually has a number of error messages such as the following:  
  
```  
./libs/vmd/test/test_assert_fail.cxx:15:3: error: too few arguments provided to function-like macro invocation  
  BOOST_VMD_ASSERT(0)  
  ^  
```  
  
```  
In file included from libs/vmd/test/test_assert_fail.cpp:7:  
./libs/vmd/test/test_assert_fail.cxx:15:3: error: use of undeclared identifier 'BOOST_PP_ASSERT_ERROR'  
  BOOST_VMD_ASSERT(0)  
```  
  
Search for  
```  
error:  
```  
in the OSX job.  Do you expect to see those errors? I believe the job is failing, and "set -e" would demonstrate that.  Testing on a forked copy of the repo now.

---

## Comment 1

> Username: eldiener  
> Created at: 2021-01-25 20:46:48 UTC  
> Url: https://github.com/boostorg/vmd/issues/7#issuecomment-767102374  

A number of tests are supposed to fail, so when they do the test is a success. Boost Build has the ability to run a test expecting a failure, so only if such a test succeeds does it indicate something wrong.

---

## Comment 2

> Username: eldiener  
> Created at: 2021-01-25 21:44:46 UTC  
> Url: https://github.com/boostorg/vmd/issues/7#issuecomment-767132642  

On 1/25/2021 3:55 PM, Sam Darwin wrote:  
>  
>     A number of tests are supposed to fail  
>  
> Right, a number of tests are supposed to fail. However, look at the   
> contents of those particular error messages. They might not be in that   
> category?  
>  
What category ? The test clearly fails with an error message. This   
failure is expected and indicates an overall testing success. I am not   
sure what your concern is.  
  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/vmd/issues/7#issuecomment-767106386>, or   
> unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAXX3X3ZZHPJ5QZAG7OBJQTS3XLC5ANCNFSM4WSKIFMA>.  
>

---

## Comment 3

> Username: sdarwin  
> Created at: 2021-01-25 22:37:54 UTC  
> Url: https://github.com/boostorg/vmd/issues/7#issuecomment-767159150  

Thanks for your help.   I was jumping to conclusions.  The concern was this: The Linux tests passed on drone, but the OSX tests consistently failed, and there were the numerous errors (mentioned above).   Were they the reason?    
However, upon further testing...  the OSX build hangs with xcode 11.7 and xcode 11.3.   Going back to 9.4, which is what travis seems to be using, the drone test passes.   So, there might still be problems on newer xcode versions...  
Closing the issue.
