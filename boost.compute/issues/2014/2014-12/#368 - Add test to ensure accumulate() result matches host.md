# #368 - Add test to ensure accumulate() result matches host [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 18:37:02 UTC  
> Updated at: 2014-12-31 00:35:58 UTC  
> Closed at: 2014-12-31 00:35:58 UTC  
> Url: https://github.com/boostorg/compute/issues/368  

Add a test-case to `test_accumulate.cpp` which ensures, for a number of different input ranges, that the result from `boost::compute::accumulate()` exactly matches that from `std::accumulate()`.  
  
For example, this test should always pass:  
  
```  
float data[] = { 1.1f, 2.3f, 3.4f, 4.5f };  
std::vector<float> host_vec(data, data + 4);  
boost::compute::vector<float> device_vec(data, data + 4, queue);  
  
BOOST_CHECK_EQUAL(  
    std::accumulate(host_vec.begin(), host_vec.end(), 0),  
    boost::compute::accumulate(device_vec.begin(), device_vec.end(), 0, queue)  
);  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-31 00:15:19 UTC  
> Url: https://github.com/boostorg/compute/issues/368#issuecomment-68413135  

Implemented in PR #400.
