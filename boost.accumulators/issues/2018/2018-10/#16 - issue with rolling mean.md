# #16 - issue with rolling mean [Open]

> Username: ghost  
> Created at: 2018-10-11 15:04:10 UTC  
> Updated at: 2018-10-11 15:18:13 UTC  
> Url: https://github.com/boostorg/accumulators/issues/16  

Hi All,  
This is reiteration of the old stackoverflow question. Rolling mean seems not working correctly with unsigned integers. My system: Mac OS, High Sierra, the latest brew updates.  
  
// g++ -std=c++11 -o accum_test accum_test.cpp  
Sample file is attached.  
[accum_test.txt](https://github.com/boostorg/accumulators/files/2469540/accum_test.txt)  
  
  
  
OUTPUT:  
  
***** Signed integers *****  
9 actualMean: 9.0000000000000000  
8 actualMean: 8.5000000000000000  
7 actualMean: 8.0000000000000000  
5 actualMean: 7.2500000000000000  
6 actualMean: 7.0000000000000000  
4 actualMean: 6.0000000000000000  
3 actualMean: 5.0000000000000000  
2 actualMean: 4.0000000000000000  
1 actualMean: 3.2000000000000002  
0 actualMean: 2.0000000000000000  
0 actualMean: 1.2000000000000000  
0 actualMean: 0.6000000000000000  
0 actualMean: 0.2000000000000000  
0 actualMean: -0.0000000000000001  
0 actualMean: -0.0000000000000001  
0 actualMean: -0.0000000000000001  
  
***** Unsigned integers *****  
9 actualMean: 9.0000000000000000  
8 actualMean: 8.5000000000000000  
7 actualMean: 8.0000000000000000  
5 actualMean: 7.2500000000000000  
6 actualMean: 7.0000000000000000  
4 actualMean: 858993465.2000000476837158  
3 actualMean: 1717986923.4000000953674316  
2 actualMean: 2576980381.6000003814697266  
1 actualMean: 3435973840.0000004768371582  
0 actualMean: 4294967298.0000000000000000  
0 actualMean: 5153960756.3999996185302734  
0 actualMean: 6012954215.0000000000000000  
0 actualMean: 6871947673.8000001907348633  
0 actualMean: 7730941132.8000001907348633  
0 actualMean: 7730941132.8000001907348633  
0 actualMean: 7730941132.8000001907348633
