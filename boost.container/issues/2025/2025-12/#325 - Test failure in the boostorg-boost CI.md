# #325 - Test failure in the boostorg/boost CI [Closed]

> Username: pdimov  
> Created at: 2025-12-25 17:16:58 UTC  
> Updated at: 2025-12-26 17:49:57 UTC  
> Closed at: 2025-12-26 17:49:56 UTC  
> Url: https://github.com/boostorg/container/issues/325  

I'm seeing the following:  
```  
The following tests FAILED:  
	313 - boost_container_global_resource_test (Failed)  
```  
```  
 307/2541 Test  #313: boost_container_global_resource_test ..................................................***Failed    0.00 sec  
/home/runner/work/boost/boost/libs/container/test/global_resource_test.cpp(76): test '(allocation_count - memcount) == 1' failed in function 'void test_new_delete_resource()'  
1 error detected.  
```  
(https://github.com/boostorg/boost/actions/runs/20505631888/job/58919657917)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-12-26 17:49:56 UTC  
> Url: https://github.com/boostorg/container/issues/325#issuecomment-3693165504  

Looks like the regression was fixed with the latest commits:  
  
https://github.com/boostorg/boost/actions/runs/20524984202/job/58966615908#step:5:622  
  
Thanks for the report!
