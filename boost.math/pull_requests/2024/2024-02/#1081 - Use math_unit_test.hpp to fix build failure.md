# #1081 Use math_unit_test.hpp to fix build failure [Merged]

> Username: NAThompson  
> Created at: 2024-02-09 20:21:14 UTC  
> Updated at: 2024-02-09 23:32:39 UTC  
> Merged at: 2024-02-09 23:32:35 UTC  
> Closed at: 2024-02-09 23:32:35 UTC  
> Url: https://github.com/boostorg/math/pull/1081  

We often see the condition number test failing with  
  
```  
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/as.exe: ..\..\..\bin.v2\libs\math\test\condition_number_test.test\a7486d3baaf381a6d13710dc26b7b123\condition_number_test.o: too many sections (32989)  
C:\Users\RUNNER~1\AppData\Local\Temp\ccuI2rpi.s: Assembler messages:  
C:\Users\RUNNER~1\AppData\Local\Temp\ccuI2rpi.s: Fatal error: can't write 155 bytes to section .text of ..\..\..\bin.v2\libs\math\test\condition_number_test.test\a7486d3baaf381a6d13710dc26b7b123\condition_number_test.o: 'File too big'  
```  
  
Use math_unit_test.hpp to fix this.

---
