# #65 Fix degraded performance in filebuf [Merged]

> Username: Flamefire  
> Created at: 2020-02-06 11:49:20 UTC  
> Updated at: 2020-02-06 17:10:42 UTC  
> Merged at: 2020-02-06 17:10:38 UTC  
> Closed at: 2020-02-06 17:10:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/65  

The buffer size of 4 Bytes was way to small. Now the recommended BUFSIZ is used.  
  
Test with 5 repetitions using average speeds:  
  
Before:  
```  
================== Read performance ==================  
block size     stdio     std::fstream nowide::fstream  
      32   589.721 MB/s  661.222 MB/s  138.030 MB/s   
      64   933.697 MB/s  995.025 MB/s  142.438 MB/s   
     128  1235.132 MB/s 1453.555 MB/s  144.668 MB/s   
     256  1472.331 MB/s 1981.950 MB/s  149.009 MB/s   
     512  1543.075 MB/s 2482.728 MB/s  155.696 MB/s   
    1024  1781.915 MB/s 2994.891 MB/s  158.978 MB/s   
    2048  2065.877 MB/s 2918.191 MB/s  156.300 MB/s   
    4096  2154.502 MB/s 2850.447 MB/s  145.350 MB/s   
    8192  2895.307 MB/s 3441.936 MB/s  159.695 MB/s   
================== Write performance =================  
block size     stdio     std::fstream nowide::fstream  
      32   255.397 MB/s  311.555 MB/s   98.396 MB/s   
      64   262.655 MB/s  341.753 MB/s  109.568 MB/s   
     128   327.206 MB/s  363.954 MB/s  106.059 MB/s   
     256   297.703 MB/s  407.713 MB/s  111.545 MB/s   
     512   344.982 MB/s  420.278 MB/s  115.651 MB/s   
    1024   342.595 MB/s  126.246 MB/s  115.405 MB/s   
    2048   315.773 MB/s  220.828 MB/s  115.697 MB/s   
    4096   340.020 MB/s  371.936 MB/s  112.137 MB/s   
    8192   352.337 MB/s  415.784 MB/s  116.203 MB/s   
```  
  
After:  
```  
================== Read performance ==================  
block size     stdio     std::fstream nowide::fstream  
      32   602.541 MB/s  666.701 MB/s  723.807 MB/s   
      64   894.570 MB/s 1093.934 MB/s 1211.819 MB/s   
     128  1170.176 MB/s 1511.979 MB/s 1965.282 MB/s   
     256  1436.198 MB/s 1920.610 MB/s 2658.605 MB/s   
     512  1625.220 MB/s 2519.148 MB/s 3225.811 MB/s   
    1024  1713.372 MB/s 2915.468 MB/s 3263.229 MB/s   
    2048  2000.705 MB/s 3090.778 MB/s 3875.630 MB/s   
    4096  2287.251 MB/s 2938.587 MB/s 3746.475 MB/s   
    8192  3138.165 MB/s 4080.735 MB/s 3647.314 MB/s   
================== Write performance =================  
block size     stdio     std::fstream nowide::fstream  
      32   272.345 MB/s  266.129 MB/s  262.614 MB/s   
      64   303.842 MB/s  326.813 MB/s  341.367 MB/s   
     128   332.460 MB/s  352.662 MB/s  372.072 MB/s   
     256   377.738 MB/s  387.802 MB/s  384.182 MB/s   
     512   374.873 MB/s  409.041 MB/s  371.738 MB/s   
    1024   338.324 MB/s  129.468 MB/s  354.047 MB/s   
    2048   308.512 MB/s  224.728 MB/s  365.460 MB/s   
    4096   344.864 MB/s  369.935 MB/s  351.293 MB/s   
    8192   339.138 MB/s  423.910 MB/s  357.615 MB/s  
```

---
