# #320 - Adding call() function with variadic templates [Open]

> Username: adamcavendish  
> Created at: 2014-11-30 12:05:23 UTC  
> Updated at: 2017-03-21 04:14:54 UTC  
> Url: https://github.com/boostorg/compute/issues/320  

For C++11 users, maybe we can setup helper call() functions with variadic templates instead of set_arg() stuffs?  
  
For example:   
[Kernel.hpp](https://gitcafe.com/adamcavendish/OpenCL_Learning_2014/blob/master/include/Kernel/Kernel.hpp)  
[Kernel.tcc](https://gitcafe.com/adamcavendish/OpenCL_Learning_2014/blob/master/include/Kernel/Kernel.tcc)  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-01 05:04:36 UTC  
> Url: https://github.com/boostorg/compute/issues/320#issuecomment-65021230  

Something like that would be interesting to have. Currently there is the variadic `kernel::set_args()` function which can be used to set all of the arguments for a kernel with a single function call like:  
  
`kernel.set_args(input.size(), input.data(), output.size(), output.data());`
