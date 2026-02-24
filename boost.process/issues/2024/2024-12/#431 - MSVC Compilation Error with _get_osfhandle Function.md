# #431 - [v2] MSVC Compilation Error with _get_osfhandle Function [Closed]

> Username: UMU618  
> Created at: 2024-12-01 14:08:17 UTC  
> Updated at: 2024-12-13 04:26:58 UTC  
> Closed at: 2024-12-13 04:26:58 UTC  
> Url: https://github.com/boostorg/process/issues/431  

```  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.26100.0\ucrt\corecrt_io.h(195,31): error C2375: 'boost::process::v2::detail::_get_osfhandle': redefinition; different linkage  
1>(compiling source file '/src/runner_manager.cpp')  
1>    src\vcpkg_installed\x64-windows\x64-windows\include\boost\process\v2\stdio.hpp(34,47):  
1>    see declaration of 'boost::process::v2::detail::_get_osfhandle'  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.26100.0\ucrt\corecrt_io.h(195,31): error C2733: '_get_osfhandle': you cannot overload a function with 'extern "C"' linkage  
1>(compiling source file '/src/runner_manager.cpp')  
1>    src\vcpkg_installed\x64-windows\x64-windows\include\boost\process\v2\stdio.hpp(34,47):  
1>    see declaration of 'boost::process::v2::detail::_get_osfhandle'  
```  
  
To fix, add a `BOOST_SYMBOL_IMPORT`:  
```cpp  
BOOST_PROCESS_V2_BEGIN_NAMESPACE  
namespace detail  
{  
#if defined(BOOST_PROCESS_V2_WINDOWS)  
  
extern "C" BOOST_SYMBOL_IMPORT intptr_t _get_osfhandle(int fd);  
  
struct handle_closer  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-02 00:19:10 UTC  
> Url: https://github.com/boostorg/process/issues/431#issuecomment-2510327006  

Thanks for reporting.  
  
This generally looks correct, but i'd need to make sure it won't breake stuff somewhere else (like cross compiling). And I might just remove the forward declaration and include `<io.h>`   
  
Are you including `<io.h>` before or after `process` ?

---

## Comment 2

> Username: UMU618  
> Created at: 2024-12-02 04:31:36 UTC  
> Url: https://github.com/boostorg/process/issues/431#issuecomment-2510556950  

There's no <io.h> included explictly.  
  
runner_manager.h:  
```cpp  
#pragma once  
  
#include <memory>  
  
#include <boost/asio.hpp>  
#include <boost/process/v2/popen.hpp>  
```  
  
runner_manager.cpp:  
```cpp  
#include "pch.h"  
  
#include "runner_manager.h"  
  
#include <boost/process/v2/windows/as_user_launcher.hpp>  
```  
  
pch.h:  
```cpp  
#pragma once  
  
#define WIN32_LEAN_AND_MEAN  
#include <windows.h>  
  
#include <array>  
#include <cstddef>  
#include <cstdint>  
#include <map>  
#include <memory>  
#include <mutex>  
#include <set>  
#include <string>  
#include <string_view>  
#include <vector>  
  
#include <boost/assert.hpp>  
#include <boost/scope/defer.hpp>  
  
#include <gsl/gsl>  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-12-03 00:00:10 UTC  
> Url: https://github.com/boostorg/process/issues/431#issuecomment-2513225751  

Thanks for the details.   
  
I'll probably just end up including io.h, since windows.h comes in via asio anyhow.
