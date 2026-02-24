# #184 - clang is now warning on use of `sprintf` [Closed]

> Username: mclow  
> Created at: 2022-11-30 00:37:54 UTC  
> Updated at: 2022-12-03 09:22:36 UTC  
> Closed at: 2022-12-03 09:22:36 UTC  
> Url: https://github.com/boostorg/regex/issues/184  

```  
libs/regex/build/../src/posix_api.cpp:182:27: warning: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Wdeprecated-declarations]  
            int r = (std::sprintf)(localbuf, "%d", i);  
                          ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/stdio.h:188:1: note: 'sprintf' has been explicitly marked deprecated here  
__deprecated_msg("This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead.")  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/sys/cdefs.h:215:48: note: expanded from macro '__deprecated_msg'  
        #define __deprecated_msg(_msg) __attribute__((__deprecated__(_msg)))  
                                                      ^  
libs/regex/build/../src/posix_api.cpp:194:21: warning: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Wdeprecated-declarations]  
      int r = (std::sprintf)(localbuf, "%d", 0);  
                    ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/stdio.h:188:1: note: 'sprintf' has been explicitly marked deprecated here  
__deprecated_msg("This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead.")  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/sys/cdefs.h:215:48: note: expanded from macro '__deprecated_msg'  
        #define __deprecated_msg(_msg) __attribute__((__deprecated__(_msg)))  
                                                      ^  
```

---

## Comment 1

> Username: mclow  
> Created at: 2022-11-30 00:38:13 UTC  
> Url: https://github.com/boostorg/regex/issues/184#issuecomment-1331498038  

Apple clang version 14.0.0 (clang-1400.0.29.202)
