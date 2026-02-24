# #775 - Random numbers generation [Open]

> Username: orralus  
> Created at: 2018-04-29 14:59:28 UTC  
> Updated at: 2018-05-09 11:44:02 UTC  
> Url: https://github.com/boostorg/compute/issues/775  

I try to generate vector of random number multiple times and it seems to generate the same vector  
  
for example   
  
...  
    auto seed = std::chrono::high_resolution_clock::now().time_since_epoch().count();  
    compute::default_random_engine engine(queue,seed);  
    compute::uniform_real_distribution<float> GPUdistribution(0.0, 1.0);  
     for (int t=0; t<T;t++) {  
     ...  
    GPUdistribution.generate(vec2.begin(), vec2.end(),engine,queue);  
    ...  
   }  
  
Is it necessary to destroy and regenerate engine with new seed at each iteration to get proper results?  
Thanks

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-04-29 15:43:43 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-385260362  

Can you try using `boost::compute::threefry_engine` instead of `default_random_engine`?

---

## Comment 2

> Username: orralus  
> Created at: 2018-04-29 15:59:38 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-385261422  

Same as in   
https://github.com/boostorg/compute/issues/745  
  
 GPUdistribution.generate(vec2.begin(), vec2.end(), engine ,queue);   
does not compile

---

## Comment 3

> Username: Ulfgard  
> Created at: 2018-04-29 16:54:18 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-385265070  

Which version of boost are you using?

---

## Comment 4

> Username: orralus  
> Created at: 2018-04-30 05:46:46 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-385317428  

1.61

---

## Comment 5

> Username: Ulfgard  
> Created at: 2018-04-30 07:16:26 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-385326850  

Can you try the newest boost? I think this got fixed in #747

---

## Comment 6

> Username: orralus  
> Created at: 2018-05-07 08:01:53 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-386989847  

Upgraded to 1.67 and problem is persistent

---

## Comment 7

> Username: orralus  
> Created at: 2018-05-08 11:49:55 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-387376884  

Apparently the threefry.hpp here is not the same as the one which comes with 1.67

---

## Comment 8

> Username: jszuppe  
> Created at: 2018-05-08 12:17:09 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-387382927  

That's possible. I don't know when @kylelutz merged those changes into `master`.

---

## Comment 9

> Username: orralus  
> Created at: 2018-05-09 11:37:38 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-387710698  

I think it settles the randomness issue but apparently it fills the device memory from some reason after several iterations.  
  
"terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Memory Object Allocation Failure"  
  
does threefry allocate memory somewhere?   
I think it is because it builds new kernels and program in every generation (in the private function load_program).

---

## Comment 10

> Username: Ulfgard  
> Created at: 2018-05-09 11:44:02 UTC  
> Url: https://github.com/boostorg/compute/issues/775#issuecomment-387712126  

all program generation is cached and the cache size is limited (i think 32 or 64 programs). load_program is also called only in the constructor.
