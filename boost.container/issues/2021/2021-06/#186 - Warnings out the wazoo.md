# #186 - Warnings out the wazoo [Closed]

> Username: vinniefalco  
> Created at: 2021-06-26 17:28:36 UTC  
> Updated at: 2021-08-08 23:01:08 UTC  
> Closed at: 2021-08-08 23:01:08 UTC  
> Url: https://github.com/boostorg/container/issues/186  

Building on Visual Studio from the Boost.JSON solution, generated with cmake:  
```  
3>clone_current_exception_non_intrusive.cpp  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(1878,38): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(3103,3): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(3182,3): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4078,51): warning C4127: conditional expression is constant  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4082,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4136,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4148,39): warning C4127: conditional expression is constant  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4152,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4155,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4320,11): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4331,11): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4567,8): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4604,57): warning C4127: conditional expression is constant  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4668,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4695,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4778,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4905,11): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4953,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5027,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5081,3): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5094,8): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5129,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5205,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5207,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5243,12): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5245,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5332,8): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5334,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5453,8): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5462,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5501,8): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5538,57): warning C4127: conditional expression is constant  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5602,5): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5622,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5705,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5755,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5757,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5794,12): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5796,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5867,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5869,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(974,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(982,3): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1107,14): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1109,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1120,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1146,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1168,14): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1172,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1196,14): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1198,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1218,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1248,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1290,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1294,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1314,9): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1316,7): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1381,12): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1428,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1460,17): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1463,4): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1469,10): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_ext_2_8_6.c(1475,4): warning C4057: 'function': 'volatile LONG *' differs in indirection to slightly different base types from 'int *'  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5716): warning C4702: unreachable code  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5811): warning C4702: unreachable code  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5824): warning C4702: unreachable code  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5834): warning C4702: unreachable code  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(5499): warning C4702: unreachable code  
2>C:\Users\vinnie\src\boost\libs\container\src\dlmalloc_2_8_6.c(4534): warning C4702: unreachable code  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-08 22:58:47 UTC  
> Url: https://github.com/boostorg/container/issues/186#issuecomment-894868039  

Thanks for the report. dlmalloc_2_8_6.c is external code that I don't want to modify and those warnings are harmless, I will disable warnings when including the file.
