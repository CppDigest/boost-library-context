# #470 extract_uint: Fixed IgnoreOverflowDigits=true problem at overflow [Merged]

> Username: Kojoley  
> Created at: 2019-02-21 22:53:17 UTC  
> Updated at: 2019-02-22 12:13:07 UTC  
> Merged at: 2019-02-22 12:13:03 UTC  
> Closed at: 2019-02-22 12:13:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/470  

The IgnoreOverflowDigits=true parser always succeeds and because of that it  
expects from the digits parser that a number passed as inout argument will be  
changed only on success.  
  
Changed negative_accumulator and X3 for consistency.  
  
Fixes #428

---
