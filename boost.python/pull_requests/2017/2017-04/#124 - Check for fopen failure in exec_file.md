# #124 Check for fopen failure in exec_file [Open]

> Username: edgare  
> Created at: 2017-04-11 20:31:25 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/124  

At least in python 3.4.6 passing a NULL file pointer to PyRun_File  
results in reading uninitialized memory.

---
