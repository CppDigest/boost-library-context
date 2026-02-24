# #260 - Is 1_69_0 auto_link.hpp missing BOOST_LIB_ARCH_AND_MODEL_OPT for the BOOST_AUTO_LINK_TAGGED option? [Closed]

> Username: richardscholes  
> Created at: 2019-01-30 00:31:48 UTC  
> Updated at: 2019-04-01 17:58:37 UTC  
> Closed at: 2019-04-01 17:58:37 UTC  
> Url: https://github.com/boostorg/config/issues/260  

After building Boost version 1.69.0 using --layout=tagged my Windows dll names now include the architecture (e.g. boost_atomic-mt-gd-x64.dll), however the auto-link feature does not include this when forming the lib name to link against? LIB_DIAGNOSTIC message is 'Linking to boost_atomic-mt-gd.lib' and hence the linker fails to link.  
  
Should auto_link.hpp be changed from:  
Line 407:  
```  
#ifdef BOOST_AUTO_LINK_TAGGED  
#  pragma comment(lib, BOOST_LIB_PREFIX BOOST_STRINGIZE(BOOST_LIB_NAME) BOOST_LIB_THREAD_OPT BOOST_LIB_RT_OPT ".lib")  
#  ifdef BOOST_LIB_DIAGNOSTIC  
#     pragma message ("Linking to lib file: " BOOST_LIB_PREFIX BOOST_STRINGIZE(BOOST_LIB_NAME) BOOST_LIB_THREAD_OPT BOOST_LIB_RT_OPT ".lib")  
#  endif  
  
```  
To:  
  
```  
#ifdef BOOST_AUTO_LINK_TAGGED  
#  pragma comment(lib, BOOST_LIB_PREFIX BOOST_STRINGIZE(BOOST_LIB_NAME) BOOST_LIB_THREAD_OPT BOOST_LIB_RT_OPT BOOST_LIB_ARCH_AND_MODEL_OPT ".lib")  
#  ifdef BOOST_LIB_DIAGNOSTIC  
#     pragma message ("Linking to lib file: " BOOST_LIB_PREFIX BOOST_STRINGIZE(BOOST_LIB_NAME) BOOST_LIB_THREAD_OPT BOOST_LIB_RT_OPT BOOST_LIB_ARCH_AND_MODEL_OPT ".lib")  
#  endif  
```

---

## Comment 1

> Username: rvanbakel  
> Created at: 2019-02-22 01:37:23 UTC  
> Url: https://github.com/boostorg/config/issues/260#issuecomment-466238633  

We are seeing the same issue here. We are sticking to boost v1.68 for now as it doesn't have the same issue.
