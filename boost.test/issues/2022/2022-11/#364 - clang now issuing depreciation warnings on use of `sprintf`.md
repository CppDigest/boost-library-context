# #364 - clang now issuing depreciation warnings on use of `sprintf` [Closed]

> Username: mclow  
> Created at: 2022-11-30 00:33:28 UTC  
> Updated at: 2025-08-22 09:36:35 UTC  
> Closed at: 2025-08-22 09:36:35 UTC  
> Url: https://github.com/boostorg/test/issues/364  

```  
In file included from ./boost/test/output/compiler_log_formatter.hpp:19:  
./boost/test/utils/setcolor.hpp:92:31: warning: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Wdeprecated-declarations]  
        m_command_size = std::sprintf( m_control_command, "%c[%c;3%c;4%cm",  
                              ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/stdio.h:188:1: note: 'sprintf' has been explicitly marked deprecated here  
__deprecated_msg("This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead.")  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/sys/cdefs.h:215:48: note: expanded from macro '__deprecated_msg'  
        #define __deprecated_msg(_msg) __attribute__((__deprecated__(_msg)))  
                                                      ^  
```  
  
The good news is that the change is really easy  
```  
- m_command_size = std::sprintf(  m_control_command,                            "%c[%c;3%c;4%cm",  
+ m_command_size = std::snprintf( m_control_command, sizeof(m_control_command), "%c[%c;3%c;4%cm",  
```  
  
since `m_control_command` is a statically-sized array of `char`.

---

## Comment 1

> Username: sdarwin  
> Created at: 2025-08-21 14:26:45 UTC  
> Url: https://github.com/boostorg/test/issues/364#issuecomment-3210842401  

+1  
  
sprintf is deprecated on macos

---

## Comment 2

> Username: mborland  
> Created at: 2025-08-22 09:36:35 UTC  
> Url: https://github.com/boostorg/test/issues/364#issuecomment-3213738118  

Fixed by: https://github.com/boostorg/test/pull/366
