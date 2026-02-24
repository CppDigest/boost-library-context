# #56 - Order of output using suite::log [Closed]

> Username: vinniefalco  
> Created at: 2016-08-19 17:06:22 UTC  
> Updated at: 2016-08-26 17:43:08 UTC  
> Closed at: 2016-08-26 17:43:08 UTC  
> Url: https://github.com/boostorg/beast/issues/56  

if you use `suite::log` before calling `pass` or `fail` (directly or indirectly) the output is out of order. e.g.:  
  
```  
nudb.test.callgrind  
0 inserts  
nudb.test.recover  
10 inserts  
```  
  
should read  
  
```  
nudb.test.callgrind  
nudb.test.recover  
0 inserts  
10 inserts  
```  
  
The easy fix is to output the suite preamble when writing to the log for the first time, if the preamble has not already been output.  
  
A workaround is to call  
  
```  
        testcase("");  
```  
  
from overrides of `suite::run` as needed.
