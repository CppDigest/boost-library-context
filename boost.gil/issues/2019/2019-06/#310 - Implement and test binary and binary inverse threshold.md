# #310 - [gsoc2019] Implement and test binary and binary inverse threshold [Closed]

> Username: mloskot  
> Created at: 2019-06-15 21:44:02 UTC  
> Updated at: 2020-02-22 13:11:30 UTC  
> Closed at: 2019-06-18 19:42:44 UTC  
> Url: https://github.com/boostorg/gil/issues/310  

- Task copied from https://github.com/BoostGSoC19/gil-miral/issues/2  
- Implemented in https://github.com/BoostGSoC19/gil-miral/projects/1 but is waiting to be merged into the upstream at `boostorg/gil`  
  
----  
  
Two variant of Binary threshold Implemented  
  
1. **Simple Binary Threshold**  
  
If the pixel value is more than the threshold then the pixel value is set to maximum or else to 0  
  
2. **Inverse Binary Threshold**  
  
If the pixel value is more than the threshold then the pixel value is set to 0 or else to maximum
