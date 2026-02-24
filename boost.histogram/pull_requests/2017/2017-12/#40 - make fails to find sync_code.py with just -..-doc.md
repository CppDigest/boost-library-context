# #40 make fails to find sync_code.py with just "../doc". [Merged]

> Username: Axel-Naumann  
> Created at: 2017-12-12 09:35:03 UTC  
> Updated at: 2017-12-12 10:34:27 UTC  
> Merged at: 2017-12-12 10:34:21 UTC  
> Closed at: 2017-12-12 10:34:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/40  

Fixes  
```  
python: can't open file '../doc/sync_code.py': [Errno 2] No such file or directory  
```  
when running `make` after `cmake -DBOOST_ROOT=/Users/axel/build/boost/inst/ ../src/build/`

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-12-12 10:26:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/40#issuecomment-351009803  

[![Coverage Status](https://coveralls.io/builds/14614139/badge)](https://coveralls.io/builds/14614139)  
  
Coverage remained the same at 97.885% when pulling **c865ae7ec43eea117daad0a63672df5edef802e3 on Axel-Naumann:cmake-srcdir-sync_codepy** into **c64b2356bd8d72e72026af94a8f5c5c93cf70073 on HDembinski:master**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2017-12-12 10:34:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/40#issuecomment-351011844  

Vielen Dank fuer den Fix!

---
