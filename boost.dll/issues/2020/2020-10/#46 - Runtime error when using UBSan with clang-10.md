# #46 - Runtime error when using UBSan with clang-10 [Closed]

> Username: maximaximal  
> Created at: 2020-10-06 14:24:22 UTC  
> Updated at: 2020-12-28 10:41:20 UTC  
> Closed at: 2020-12-28 10:41:20 UTC  
> Url: https://github.com/boostorg/dll/issues/46  

The enum manipulation code of `shared_library_load_mode` produces an `invalid-enum-load` error with clang-10's UBSan [in line 226 of shared_library_load_mode.hpp](https://github.com/boostorg/dll/blob/develop/include/boost/dll/shared_library_load_mode.hpp#L226). This seems like a true positive, as the enumeration really becomes an invalid value at this stage.  
  
This is the produced error output:  
  
```  
/usr/include/boost/dll/shared_library_load_mode.hpp:226:19: runtime error: load of value 4278190079, which is not a valid value for type 'boost::dll::load_mode::type'  
SUMMARY: UndefinedBehaviorSanitizer: invalid-enum-load /usr/include/boost/dll/shared_library_load_mode.hpp:226:19 in   
```  
  
A quick fix would be adding the invalid enum value that this manipulation code produces (4278190079) to the enum. [enum above](https://github.com/boostorg/dll/blob/develop/include/boost/dll/shared_library_load_mode.hpp#L40):  
  
```cpp  
#else  
    rtld_deepbind                         = RTLD_DEEPBIND,  
#endif  
  
    append_decorations                    = 0x00800000,  
    search_system_folders                 = (append_decorations << 1),  
  
    _ubsan_fix = 4278190079 // this line fixes the issue for the time being  
#endif  
};  
```  
  
Completely fixing this undefined behavior seems to require some changes in how the manipulation code is used... But with this fix, I can use UBSan again. I hope this report helps!

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-19 11:01:28 UTC  
> Url: https://github.com/boostorg/dll/issues/46#issuecomment-748458176  

Could you please provide a testcase, to reproduce the issue?

---

## Comment 2

> Username: maximaximal  
> Created at: 2020-12-28 10:41:20 UTC  
> Url: https://github.com/boostorg/dll/issues/46#issuecomment-751668828  

Yes, the issue occurred with the standard quickstart example:  
  
```cpp  
#include <boost/dll.hpp>  
  
int main() {  
    boost::dll::shared_library lib("/lib64/libc.so.6");  
}  
```  
  
Compiled using clang 11.0.0 (downloaded from [its releases page](https://releases.llvm.org/download.html), I used the SuSE Linux Enterprise Server 12SP4 x86_64 variant) using the following command:  
```  
/opt/llvm11/bin/clang++ boost-dll-bug.cpp -ldl -lboost_filesystem -lboost_system -fsanitize=undefined -o boost-dll-bug  
```  
The produced binary prints the following runtime error on OpenSUSE Leap 15.2, using boost version 1.66:  
```  
/usr/include/boost/dll/shared_library_load_mode.hpp:226:19: runtime error: load of value 4278190079, which is not a valid value for type 'boost::dll::load_mode::type'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/dll/shared_library_load_mode.hpp:226:19 in   
/usr/include/boost/dll/shared_library_load_mode.hpp:222:69: runtime error: load of value 4278190079, which is not a valid value for type 'boost::dll::load_mode::type'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/dll/shared_library_load_mode.hpp:222:69 in   
```  
  
When I initially reported this bug, it also occurred on Arch Linux with the then-newest boost version, which is why I suspected this to be new compared to #30. But after testing again with the updated boost version 1.75, the issue is gone. I couldn't find the change that fixed this bug, but I suspect something in boost 1.74 fixed it, as nothing else really happened in the meantime.  
  
It would be interesting to find out what caused this undefined behavior on Arch Linux, but as it is fixed, I can close this issue. Thank you for looking into this!
