# #107 - Unnecessary runtime dependency to Library induced by boost::gregorian::greg_month [Closed]

> Username: xgcssch  
> Created at: 2019-06-25 17:48:34 UTC  
> Updated at: 2019-06-27 02:27:45 UTC  
> Closed at: 2019-06-26 12:05:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/107  

### Environment  
Boost Version: 1.70  
Compiler: Visual Studio 2017 – 15.9.12  
OS: Windows 10  
  
### Steps to reproduce:  
  
1. Install boost-date-time via vcpkg  
2. Create Windows Console Project and integrate vcpkg packages  
3. Use the following testfile  
```cpp  
#include "pch.h"  
#include <boost/date_time/gregorian/greg_month.hpp>  
int main()  
{  
    boost::gregorian::greg_month m(1);  
    return 0;  
}  
```  
4.	Compile.program  
### Expected behavior:  
The resulting file should have no references to the boost dll.  
  
### Observed behaviour:  
The resulting file has a reference:  
  
```  
dumpbin /nologo /imports:boost_date_time-vc141-mt-gd-x32-1_70.dll DateTimeLinkDependency.exe  
  
Dump of file DateTimeLinkDependency.exe  
  
File Type: EXECUTABLE IMAGE  
  
  Section contains the following imports:  
  
    boost_date_time-vc141-mt-gd-x32-1_70.dll  
                41B0E0 Import Address Table  
                41B328 Import Name Table  
                     0 time date stamp  
                     0 Index of first forwarder reference  
  
                    2 ??0greg_month@gregorian@boost@@QAE@G@Z  
```  
### Temporary fix:  
Change in `boost\date_time\gregorian\greg_month.hpp`:  
  
From   
```  
class BOOST_DATE_TIME_DECL greg_month : public greg_month_rep {  
```  
to   
```  
class BOOST_SYMBOL_VISIBLE greg_month : public greg_month_rep {  
```  
after that:  
```  
Dump of file DateTimeLinkDependency.exe  
  
File Type: EXECUTABLE IMAGE  
  
  Section contains the following imports:  
  
  Summary  
  
        1000 .00cfg  
        1000 .data  
        1000 .idata  
        1000 .msvcjmc  
        4000 .rdata  
        1000 .reloc  
        1000 .rsrc  
        D000 .text  
       10000 .textbss  
```  
### Impact  
All our programs using date_time::gregorian::date must be accompanied by the date_time dll.  
  
The behavior seems a little bit similar to #34!?  
  
Best regards  
Sönke

---

## Comment 1

> Username: jeking3  
> Created at: 2019-06-26 11:43:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/107#issuecomment-505839427  

This will be resolved by #85, eliminating the link library all-together.

---

## Comment 2

> Username: xgcssch  
> Created at: 2019-06-26 12:05:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/107#issuecomment-505845926  

Ok!  
Thanks for maintaining this project!!  
Best regards  
Sönke

---

## Comment 3

> Username: jeking3  
> Created at: 2019-06-26 16:08:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/107#issuecomment-505941606  

I do what I can; but appreciate any assistance in addressing issues. :)

---

## Comment 4

> Username: JeffGarland  
> Created at: 2019-06-26 16:41:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/107#issuecomment-505954001  

I'm hoping to keep contributing again.  @jeking3 do we not have a branch with changes to finally solve #85 somewhere?  Or are we still waiting for an implementation?  I think post c++11 removing the library gets even easier to implement...

---

## Comment 5

> Username: jeking3  
> Created at: 2019-06-27 02:27:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/107#issuecomment-506131115  

Work hasn't started yet on #85.
