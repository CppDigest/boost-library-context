# #160 Improve filbuf performance [Merged]

> Username: Flamefire  
> Created at: 2022-06-11 11:15:17 UTC  
> Updated at: 2022-06-12 20:37:53 UTC  
> Merged at: 2022-06-12 20:37:49 UTC  
> Closed at: 2022-06-12 20:37:49 UTC  
> Url: https://github.com/boostorg/nowide/pull/160  

Implements the `xsgetn` / `xsputn` member function to do faster bulk I/O. This dramatically improves performance especially for text streams (that use unbuffered reads), see #31  
  
Performance numbers below. Small reads on binary streams are very slightly slower due to additional checks as it still falls back to "regular" read (via `underflow`) as that is still faster due to buffering, i.e. that avoids some `fread` calls.  
  
```  
================== Read performance (binary) ==================  
block size     stdio     std::fstream nowide::fstream (develop - PR)  
      32   876.260 MB/s  703.782 MB/s  714.873 MB/s  693.814 MB/s  
      64  1118.084 MB/s  928.763 MB/s  921.519 MB/s  889.736 MB/s  
     128  1289.297 MB/s 1149.077 MB/s 1075.776 MB/s 1065.222 MB/s  
     256  1384.214 MB/s 1300.754 MB/s 1222.154 MB/s 1209.963 MB/s  
     512  1433.466 MB/s 1403.791 MB/s 1289.383 MB/s 1280.141 MB/s  
    1024  1477.499 MB/s 1449.607 MB/s 1318.908 MB/s 1412.028 MB/s  
    2048  1486.888 MB/s 1470.983 MB/s 1344.479 MB/s 1462.279 MB/s  
    4096  1551.583 MB/s 1463.683 MB/s 1349.682 MB/s 1520.525 MB/s  
    8192  2521.471 MB/s 1463.282 MB/s 1358.387 MB/s 2463.760 MB/s  
================== Write performance (binary) =================  
block size     stdio     std::fstream nowide::fstream (develop - PR)  
      32   368.177 MB/s  321.370 MB/s  298.016 MB/s  308.568 MB/s  
      64   448.402 MB/s  398.231 MB/s  367.699 MB/s  371.018 MB/s  
     128   484.865 MB/s  448.573 MB/s  446.601 MB/s  428.367 MB/s  
     256   526.169 MB/s  538.484 MB/s  515.202 MB/s  513.881 MB/s  
     512   527.579 MB/s  509.083 MB/s  557.241 MB/s  496.469 MB/s  
    1024   567.403 MB/s  548.288 MB/s  578.350 MB/s  594.638 MB/s  
    2048   579.841 MB/s  561.949 MB/s  592.756 MB/s  587.821 MB/s  
    4096   592.644 MB/s  600.876 MB/s  624.009 MB/s  660.160 MB/s  
    8192   953.333 MB/s 1027.934 MB/s  650.560 MB/s 1022.936 MB/s  
================== Read performance (text) ====================  
block size     stdio     std::fstream nowide::fstream (develop - PR)  
      32   604.922 MB/s  518.859 MB/s   55.393 MB/s  399.057 MB/s  
      64   706.850 MB/s  628.889 MB/s   56.679 MB/s  596.283 MB/s  
     128   777.519 MB/s  724.074 MB/s   57.506 MB/s  692.107 MB/s  
     256   811.692 MB/s  787.361 MB/s   57.915 MB/s  762.263 MB/s  
     512   830.136 MB/s  824.330 MB/s   58.273 MB/s  802.172 MB/s  
    1024   845.882 MB/s  834.972 MB/s   58.236 MB/s  823.795 MB/s  
    2048   850.206 MB/s  845.812 MB/s   58.164 MB/s  836.866 MB/s  
    4096   867.617 MB/s  842.327 MB/s   58.390 MB/s  856.965 MB/s  
    8192  1106.598 MB/s  844.903 MB/s   58.180 MB/s 1091.494 MB/s  
================== Write performance (text) ===================  
block size     stdio     std::fstream nowide::fstream (develop - PR)  
      32   319.291 MB/s  209.892 MB/s  212.279 MB/s  242.772 MB/s  
      64   380.288 MB/s  311.503 MB/s  278.233 MB/s  297.558 MB/s  
     128   388.957 MB/s  357.903 MB/s  276.981 MB/s  325.288 MB/s  
     256   424.167 MB/s  403.972 MB/s  337.287 MB/s  373.840 MB/s  
     512   426.039 MB/s  409.020 MB/s  327.755 MB/s  392.816 MB/s  
    1024   460.611 MB/s  434.510 MB/s  363.193 MB/s  437.961 MB/s  
    2048   460.644 MB/s  409.591 MB/s  380.279 MB/s  400.706 MB/s  
    4096   448.355 MB/s  433.666 MB/s  408.713 MB/s  412.959 MB/s  
    8192   527.050 MB/s  469.335 MB/s  396.331 MB/s  491.971 MB/s  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-11 13:50:45 UTC  
> Updated_at: 2022-06-12 20:01:58 UTC  
> Url: https://github.com/boostorg/nowide/pull/160#issuecomment-1152931464  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/160?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#160](https://codecov.io/gh/boostorg/nowide/pull/160?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f3746eb) into [develop](https://codecov.io/gh/boostorg/nowide/commit/8693fbc605385b5d73b54df862e114852e36b267?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8693fbc) will **increase** coverage by `0.02%`.  
> The diff coverage is `98.99%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #160      +/-   ##  
===========================================  
+ Coverage    99.44%   99.46%   +0.02%       
===========================================  
  Files           33       33                
  Lines         3232     3383     +151       
===========================================  
+ Hits          3214     3365     +151       
  Misses          18       18                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `95.62% <97.10%> (+0.79%)` | :arrow_up: |  
| [test/test\_filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZpbGVidWYuY3Bw) | `100.00% <100.00%> (ø)` | |

---
