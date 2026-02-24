# #522 Fix find_extrema_with_atomics and _with_reduce [Merged]

> Username: jszuppe  
> Created at: 2015-10-03 16:05:01 UTC  
> Updated at: 2015-10-23 19:26:51 UTC  
> Merged at: 2015-10-23 07:19:54 UTC  
> Closed at: 2015-10-23 07:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/522  

This fixes the bug with `find_extrema_with_atomics` and `find_extrema_with_reduce` not always returning iterator to the first extremum (`serial_find_extrema` works correctly).  
  
In the first commit I've modified tests to show this problem. You can checkout to it and see that tests will fail. In the second and the third algorithms are modified to take value index in input data into account if compared values are equal.    
  
Additionally, in order to increase the performance of `find_extrema_with_reduce` I used zero-copy (pinned) memory.   
  
Performance tests (on GPU AMD Radeon HD7770 1GB) for  `find_extrema_with_reduce`:  
  
```  
less<input_type>:  
  
=== max_element with compute === [MASTER]  
size,time (ms)  
< serial version is used for smaller inputs   
512,0.221056  
1024,0.212490  
2048,0.195597  
4096,0.207009  
8192,0.220071  
16384,0.224109  
32768,0.227049  
65536,0.223422  
131072,0.237844  
262144,0.241777  
524288,0.268716  
1048576,0.284547  
2097152,0.358343  
4194304,0.471007  
8388608,0.758049  
16777216,1.293140  
33554432,2.318780  
  
=== max_element with compute === [PROPOSED]  
size,time (ms)  
< serial version is used for smaller inputs   
1024,0.215992  
2048,0.220766  
4096,0.221152  
8192,0.218342  
16384,0.203549  
32768,0.205648  
65536,0.211763  
131072,0.224671  
262144,0.247374  
524288,0.260491  
1048576,0.294694  
2097152,0.330984  
4194304,0.473001  
8388608,0.751524  
16777216,1.275850  
33554432,2.294550  
  
  
Custom comparison function identical to less<input_type>:  
  
=== max_element with compute === [MASTER]  
size,time (ms)  
< serial version is used for smaller inputs   
512,0.315379  
1024,0.311133  
2048,0.302115  
4096,0.313854  
8192,0.305789  
16384,0.309231  
32768,0.305270  
65536,0.300702  
131072,0.300408  
262144,0.316186  
524288,0.305018  
1048576,0.316081  
2097152,0.382308  
4194304,0.532169  
8388608,0.799498  
16777216,1.333740  
33554432,2.342250  
  
=== max_element with compute === [PROPOSED]  
size,time (ms)  
< serial version is used for smaller inputs   
512,0.337405  
1024,0.350510  
2048,0.339984  
4096,0.352044  
8192,0.326696  
16384,0.350975  
32768,0.314508  
65536,0.321856  
131072,0.326935  
262144,0.327582  
524288,0.326498  
1048576,0.321487  
2097152,0.371808  
4194304,0.523552  
8388608,0.785135  
16777216,1.292890  
33554432,2.313090  
```  
  
Performance tests (on GPU AMD Radeon HD7770 1GB) for  `find_extrema_with_atomics`:  
  
```  
=== max_element with compute ===  [MASTER]  
size,time (ms)  
< serial version is used for smaller inputs   
512,0.168068  
1024,0.170611  
2048,0.170171  
4096,0.153722  
8192,0.154775  
16384,0.163295  
32768,0.164398  
65536,0.182772  
131072,0.170064  
262144,0.177287  
524288,0.199055  
1048576,0.231150  
2097152,0.303678  
4194304,0.482624  
8388608,0.766648  
16777216,1.371060  
33554432,2.527810  
  
=== max_element with compute === [PROPOSED]  
size,time (ms)  
< serial version is used for smaller inputs   
512,0.164736  
1024,0.184760  
2048,0.184672  
4096,0.186038  
8192,0.169532  
16384,0.181060  
32768,0.180219  
65536,0.183056  
131072,0.202473  
262144,0.198088  
524288,0.209572  
1048576,0.249295  
2097152,0.333152  
4194304,0.457681  
8388608,0.799320  
16777216,1.386730  
33554432,2.524820  
```

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-10-04 22:34:58 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-145395027  

Don't merge this yet.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-10-05 10:32:07 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-145490317  

ok, ready.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-10-20 20:10:14 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-149687298  

@kylelutz Any comments? :)

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-10-21 04:54:46 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-149782830  

Oops, this slipped under my radar. Sorry for the delay.  
  
On my desktop (AMD GPU), I'm getting the following error when running `test_extrema` with this branch:  
  
```  
test_extrema: ../include/boost/compute/iterator/buffer_iterator.hpp:193: boost::compute::detail::buffer_iterator_index_expr<T, Expr> boost::compute::buffer_iterator<T>::operator[](const Expr&) const [with Expr = boost::compute::detail::meta_kernel_variable<unsigned int>; T = unsigned int]: Assertion `m_buffer.get()' failed.  
unknown location(0): fatal error in "int_min_max": signal: SIGABRT (application abort requested)  
test_extrema: ../include/boost/compute/iterator/buffer_iterator.hpp:193: boost::compute::detail::buffer_iterator_index_expr<T, Expr> boost::compute::buffer_iterator<T>::operator[](const Expr&) const [with Expr = boost::compute::detail::meta_kernel_variable<unsigned int>; T = unsigned int]: Assertion `m_buffer.get()' failed.  
unknown location(0): fatal error in "iota_min_max": signal: SIGABRT (application abort requested)  
../test/test_extrema.cpp(77): last checkpoint  
  
*** 2 failures detected in test suite "TestExtrema"  
```  
  
Any ideas? This is during a debug build which is why Travis-CI didn't seem to catch it.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2015-10-21 07:23:28 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-149803080  

Oh, I don't know how I missed that error. It is fixed now.  
  
The problem was that I declared `buffer_iterator<uint> dummy` (find_extrema_with_reduce.hpp, line 240) and used it as `input_idx` argument in `find_extrema_with_reduce(InputIterator input vector<uint_>::iterator input_idx, ...)`, but its buffer was a null and that was causing an error during kernel compilation. I fixed it by assigning some other correct iterator of the same type to `dummy'.  
  
It can also be fixed if I use if statements instead of macro `BOOST_COMPUTE_USE_INPUT_IDX`  in `find_extrema_reduce` kernel, but it would be completely unreadable.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2015-10-23 07:19:52 UTC  
> Url: https://github.com/boostorg/compute/pull/522#issuecomment-150495722  

Looks good, thanks for fixing that!

---
