# #41 - Some tests from boost libraries failed to run after boost submodule core updated from 2d99ac (master) to 32ac6c [Closed]

> Username: KarenHuang2016  
> Created at: 2018-09-17 09:39:48 UTC  
> Updated at: 2018-09-18 02:15:19 UTC  
> Closed at: 2018-09-17 11:44:56 UTC  
> Url: https://github.com/boostorg/core/issues/41  

**Environment:**   
VS 2017 + Windows Server 2016  
  
**Issue description:**  
We build and run test for boost. And we found some tests failed to run after boost submodule core updated from 2d99ac (master) to 32ac6c (https://github.com/boostorg/boost/commit/414fd2b5a34c45abcf47f72d22484372fd6716f7). It seems all the tests failed due to "main() should return report_errors()". Details is as below. Could you please take a look?  
  
1. type_index (see attached log_x86_test_113.log)   
...failed test 4 targets...  
  
2. thread (see attached log_x86_test_105.log)   
...failed test 6 targets...  
  
3. ratio (see attached log_x86_test_90.log)   
...failed test 1 targets...  
  
4. pool (see attached log_x86_test_77.log)   
...failed test 2 targets...  
  
5.  phoenix (see attached log_x86_test_74.log)   
...failed test 4 targets...  
  
6. fusion (see attached log_x86_test_41.log)   
...failed test 3 targets...  
  
7. exception (see attached log_x86_test_32.log)   
...failed test 2 targets...  
  
8. config (see attached log_x86_test_17.log)   
..failed test 1 target...   
  
[log_x86_test_17.log](https://github.com/boostorg/core/files/2388011/log_x86_test_17.log)  
[log_x86_test_32.log](https://github.com/boostorg/core/files/2388012/log_x86_test_32.log)  
[log_x86_test_41.log](https://github.com/boostorg/core/files/2388013/log_x86_test_41.log)  
[log_x86_test_74.log](https://github.com/boostorg/core/files/2388014/log_x86_test_74.log)  
[log_x86_test_77.log](https://github.com/boostorg/core/files/2388015/log_x86_test_77.log)  
[log_x86_test_90.log](https://github.com/boostorg/core/files/2388016/log_x86_test_90.log)  
[log_x86_test_105.log](https://github.com/boostorg/core/files/2388017/log_x86_test_105.log)  
[log_x86_test_113.log](https://github.com/boostorg/core/files/2388018/log_x86_test_113.log)

---

## Comment 1

> Username: glenfe  
> Created at: 2018-09-17 11:44:56 UTC  
> Updated at: 2018-09-17 11:50:36 UTC  
> Url: https://github.com/boostorg/core/issues/41#issuecomment-421981201  

All of the above tests have been updated in their develop branches now. They won't fail when they are merged to master. Thanks for reporting these.

---

## Comment 2

> Username: KarenHuang2016  
> Created at: 2018-09-18 02:15:19 UTC  
> Url: https://github.com/boostorg/core/issues/41#issuecomment-422230640  

@glenfe, got it. Thanks for clarifying this. :)
