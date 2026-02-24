# #1029 - How to build unit test? [Closed]

> Username: PayenHuang  
> Created at: 2025-04-16 13:29:59 UTC  
> Updated at: 2025-04-17 01:23:41 UTC  
> Closed at: 2025-04-17 01:23:39 UTC  
> Url: https://github.com/boostorg/boost/issues/1029  

For beginner, How to compile all executable  unit test of each module  by cmake or b2 to debug and learning？ Thanks a lot!

---

## Comment 1

> Username: mclow  
> Created at: 2025-04-16 13:44:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1029#issuecomment-2809641685  

https://www.boost.org/development/running_regression_tests.html says:  
  
```  
To run a library's regression tests, run Boost's [b2](https://www.boost.org/build/) utility from the <boost-root>/libs/<library>/test directory. To run a single test, specify its name (as found in <boost-root>/libs/<library>/test/Jamfile.v2) on the command line.  
  
To run every library's regression tests, run b2 from <boost-root>/status directory.  
```

---

## Comment 2

> Username: PayenHuang  
> Created at: 2025-04-17 01:23:39 UTC  
> Url: https://github.com/boostorg/boost/issues/1029#issuecomment-2811419877  

Hi @mclow ：  
   Thank you very much!
