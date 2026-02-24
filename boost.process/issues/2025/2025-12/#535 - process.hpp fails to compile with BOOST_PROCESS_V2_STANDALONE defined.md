# #535 - process.hpp fails to compile with BOOST_PROCESS_V2_STANDALONE defined [Open]

> Username: jwhitworth-vicon  
> Created at: 2025-12-19 15:30:05 UTC  
> Updated at: 2025-12-19 17:26:46 UTC  
> Url: https://github.com/boostorg/process/issues/535  

When compiling code including the `boost\process\v2\process.hpp` header with the defines `BOOST_PROCESS_STANDALONE`, `BOOST_PROCESS_V2_STANDALONE` and `BOOST_PROCESS_USE_STD_FS`, there are compiler errors with Visual Studio 2022 17.14:  
  
```  
1>e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(167,57): error C2653: 'is_convertible': is not a class or namespace name  
1>(compiling source file 'UtilsSystem.pch.cpp')  
1>    e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(167,57):  
1>    the template instantiation context (the oldest one first) is  
1>        e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(38,8):  
1>        while compiling class template 'process_v2::basic_process'  
1>e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(167,59): error C2065: 'value': undeclared identifier  
1>(compiling source file 'UtilsSystem.pch.cpp')  
1>e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(167,59): error C2975: '_Test': invalid template argument for 'std::enable_if', expected compile-time constant expression  
1>(compiling source file 'UtilsSystem.pch.cpp')  
1>    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xtr1common(47,28):  
1>    see declaration of '_Test'  
1>e:\thirdparty\Boost\boost-1.88.0-windows-vc143-x64\include\boost-1_88\boost\process\v2\process.hpp(167,75): error C2039: 'type': is not a member of 'std::enable_if<false,void *>'  
1>(compiling source file 'UtilsSystem.pch.cpp')  
1>    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.44.35207\include\xtr1common(48,8):  
1>    see declaration of 'std::enable_if<false,void *>'  
```  
  
This is because one of the `basic_process` constructors uses `is_convertible` without fully qualifying it with the `std::` namespace like the other earlier constructors do.  
  
https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/include/boost/process/v2/process.hpp#L168  
  
I am not sure if it is one of the defines or the combination of those defines. It is also not clear which defines I actually need if I want to use standalone asio and filesystem from the standard library.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-12-19 15:54:38 UTC  
> Url: https://github.com/boostorg/process/issues/535#issuecomment-3675577158  

Thanks for reporting. The standalone mode has been a bit neglected by me, I'll fix it up.  
  
How are you getting the library into your project?

---

## Comment 2

> Username: jwhitworth-vicon  
> Created at: 2025-12-19 15:58:22 UTC  
> Url: https://github.com/boostorg/process/issues/535#issuecomment-3675589772  

If you fix that there are then further compilation errors:  
  
1. https://github.com/boostorg/process/blob/develop/include/boost/process/v2/windows/default_launcher.hpp#L253 has a spurious `v2::` prefix that needs removing.  
2. The `BOOST_PROCESS_V2_ASSIGN_LAST_ERROR` macro defined https://github.com/boostorg/process/blob/develop/include/boost/process/v2/detail/config.hpp#L104 doesn't provide the correct number of arguments to `std::error_code::assign`.  
  
I can submit a pull request if that is easier?  
  
@klemens-morgenstern, we're including it via boost but we're using standalone asio (so some other libraries don't have a boost dependency) so I was trying to use standalone asio with boost::process and discovered these defines when looking through the headers. I guess It's a bit of a non-standard configuration.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-12-19 16:15:12 UTC  
> Url: https://github.com/boostorg/process/issues/535#issuecomment-3675644580  

I'd really appreciate a PR. That will probably be much faster than me fixing this.  
  
The v2 started out as standalone and got ported into boost. I kept the flag, but as you can tell, it's neither documented not tested by CI. Mainly because I didn't think there'd be any interest.  
  
For your use-case, would the option of a process standalone with boost.filesystem make sense?

---

## Comment 4

> Username: jwhitworth-vicon  
> Created at: 2025-12-19 16:25:47 UTC  
> Url: https://github.com/boostorg/process/issues/535#issuecomment-3675677483  

No, we're using `std::filesystem` as we're on C++17.  
  
I think the set of things that we would like to toggle would be:  
1. `std::filesystem` instead of `boost::filesystem`.  
2. `asio` instead of `boost::asio`.  
3. `std::error_*` instead of `boost::error_*`.  
  
It doesn't matter to us if `process` is in its own namespace or in the `boost` namespace. I appreciate that is more work though.

---

## Comment 5

> Username: jwhitworth-vicon  
> Created at: 2025-12-19 17:26:46 UTC  
> Url: https://github.com/boostorg/process/issues/535#issuecomment-3675920075  

Submitted a pull request. I haven't had a chance to try replacing all our usages of `boost::process` so there's a chance there may be additional errors but I'm about to go on holiday for the Christmas break so won't be able to do any more until the new year.
