# #37 - Some tests are not running in CI on Appveyor (MSVC) [Closed]

> Username: janwilmans  
> Created at: 2019-03-25 14:27:44 UTC  
> Updated at: 2020-12-19 11:17:05 UTC  
> Closed at: 2020-12-19 11:17:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/37  

Not all tests from /test/flat are appearently running on the appveyor CI.  
from all the tests below, I see only 1 running /  being compiled er in the logs.  
  
```  
core.cpp  
destructuring_tie.cpp  // this is the only test that I see running in the log file on appveyor   
flat_for_each_field.cpp  
flat_motivating_example.cpp  
flat_motivating_example2.cpp  
flat_tuple_size.cpp  
flat_tuple_size_on_bitfields.cpp  
flat_tuple_size_on_non_aggregate.cpp  
  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-04-07 14:43:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/37#issuecomment-480596449  

Yes, MSVC unfortunately works with structured bindings only. Other template metaprogramming tricks fail right now.  
  
You can find the lines for explicitly disabling the tests here: https://github.com/apolukhin/magic_get/blob/6f35c872c88783a3e5da44f653ee22580472f208/test/Jamfile.v2#L18-L22  
  
The situation may have changed since last time I was testing on MSVC. Right now I have no access to the latest MSVC. So if it is possible to enable some tests (probably with workarounding some issues) I'd be happy to merge the patches.

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-09-04 13:09:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/37#issuecomment-687134240  

I've tried to reenable at least some of the MSVC test, but the compiler [keeps crashing](https://ci.appveyor.com/project/apolukhin/magic-get/builds/35031215/job/xrabu47b4h2qiw3h#L11315) with the following message:  
```  
..\..\..\boost/pfr/detail/fields_count.hpp(95): fatal error C1001: Internal compiler error.  
(compiler file 'd:\agent\_work\8\s\src\vctools\Compiler\CxxFE\sl\p1\cxx\parsetree\nodes.h', line 398)  
 To work around this problem, try simplifying or changing the program near the locations listed above.  
If possible please provide a repro here: https://developercommunity.visualstudio.com   
Please choose the Technical Support command on the Visual C++   
 Help menu, or open the Technical Support help file for more information  
    call "..\..\..\bin.v2\standalone\msvc\msvc-14.2\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2020-12-19 11:17:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/37#issuecomment-748459895  

There's no more `flat` version in the library and the testing logic was rewritten.  
  
Now MSVC runs more tests and I do enable more of those as soon as MSVC gets the required functionality
