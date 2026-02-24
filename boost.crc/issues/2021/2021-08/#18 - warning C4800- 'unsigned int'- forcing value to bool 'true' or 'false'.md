# #18 - warning C4800: 'unsigned int': forcing value to bool 'true' or 'false' [Closed]

> Username: SpareSimian  
> Created at: 2021-08-21 23:58:53 UTC  
> Updated at: 2022-04-16 12:29:58 UTC  
> Closed at: 2022-04-16 12:29:58 UTC  
> Url: https://github.com/boostorg/crc/issues/18  

Visual Studio (2010-2019) report this warning at line 569 in crc.hpp.  
  
            bool const quotient = remainder & high_bit_mask;  
  
Replace with:  
  
            bool const quotient = (remainder & high_bit_mask) != 0;  
  
See also https://lists.boost.org/Archives/boost/2019/04/245932.php

---

## Comment 1

> Username: SpareSimian  
> Created at: 2022-01-14 20:33:32 UTC  
> Url: https://github.com/boostorg/crc/issues/18#issuecomment-1013455188  

Two pull candidates:  
  
https://github.com/boostorg/crc/pull/2  
https://github.com/boostorg/crc/pull/7

---

## Comment 2

> Username: glenfe  
> Created at: 2022-04-16 12:29:58 UTC  
> Url: https://github.com/boostorg/crc/issues/18#issuecomment-1100653281  

Should be addressed for 1.80 by 96daae9fcdf965298077ed12a9304ea24713bb40
