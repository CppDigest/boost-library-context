# #514 Fix and improve binary_find [Merged]

> Username: jszuppe  
> Created at: 2015-09-19 17:38:06 UTC  
> Updated at: 2016-05-01 11:19:29 UTC  
> Merged at: 2015-09-20 23:58:05 UTC  
> Closed at: 2015-09-20 23:58:05 UTC  
> Url: https://github.com/boostorg/compute/pull/514  

This fixes binary_find algorithm. There was a bug which resulted in iterators going out of range and causing errors. Thanks to StreamComputing for reporting this.  
  
Unfortunately, binary_search test could not find those errors as the test data was too small and binary_find kernel was not used (while loop condition was false from the beginning). I fixed that in the first commit. You can check that tests fails on this commit (binary_find is fixed in the next one).  
  
I also improved performance of binary_find. The kernel was compiled in every while loop iteration, now it's compiled only once.  
  
```  
=== binary_find with compute === [ MASTER ]  
size,time (ms)  
2,0.151978  
4,0.224376  
8,0.224038  
16,0.223790  
32,0.224597  
64,0.233016  
128,0.225822  
256,0.333630  
512,0.320126  
1024,0.314016  
2048,0.336053  
4096,0.319131  
8192,0.338581  
16384,0.319498  
32768,0.471885  
65536,0.471218  
131072,0.500875  
262144,0.502241  
524288,0.471816  
1048576,0.504494  
2097152,0.505633  
4194304,0.625844  
8388608,0.649150  
16777216,0.652294  
33554432,0.612243  
  
=== binary_find with compute === [ NEW ]  
size,time (ms)  
2,0.188248  
4,0.200487  
8,0.188612  
16,0.197525  
32,0.188116  
64,0.186364  
128,0.189061  
256,0.315787  
512,0.311341  
1024,0.308369  
2048,0.310370  
4096,0.312330  
8192,0.313158  
16384,0.390791  
32768,0.394320  
65536,0.452234  
131072,0.393403  
262144,0.394118  
524288,0.392044  
1048576,0.298422   
2097152,0.392095  
4194304,0.460290  
8388608,0.460898  
16777216,0.461379  
33554432,0.460215  
```  
  
Do not pay attention to artefacts like `1048576,0.298422` (compared to `524288,0.392044`), they occur due to input data randomness - when after while loop in binary_find `search_first == search_last` and `find_if` is not executed.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-09-20 23:58:06 UTC  
> Url: https://github.com/boostorg/compute/pull/514#issuecomment-141846209  

Awesome! Merged!

---
