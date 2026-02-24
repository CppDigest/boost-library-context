# #86 Remove never used phx2_result [Merged]

> Username: Kojoley  
> Created at: 2019-11-27 20:44:21 UTC  
> Updated at: 2019-11-29 10:36:49 UTC  
> Merged at: 2019-11-29 02:30:53 UTC  
> Closed at: 2019-11-29 02:30:53 UTC  
> Url: https://github.com/boostorg/phoenix/pull/86  

Was added in 74722b8e467131a2285f5575fd98044b9eec99a2 with a message that it is not ready, yet the headers are included with every Phoenix usage, affecting compilation times. It was supposed to implement backward compatibility with Phoenix 2 result protocol, what now seems to have little to zero value, since the break happened about ten years ago.  
  
Deleted tests:  
  * `function_tests_phx2` duplicates `function_tests`  
  * `bind_function_object_tests_phx2` duplicates `bind_function_object_tests`  
  * `lambda_tests_phx2` duplicates `lambda_tests18`+`lambda_tests19`

---
