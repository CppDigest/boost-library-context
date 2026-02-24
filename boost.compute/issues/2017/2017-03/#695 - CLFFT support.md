# #695 - CLFFT support [Open]

> Username: mahdimcheik  
> Created at: 2017-03-21 15:32:17 UTC  
> Updated at: 2017-04-24 16:39:20 UTC  
> Url: https://github.com/boostorg/compute/issues/695  

Hi,   
Boost compute is great but need to support some free libraries like CLFFT and CLBLAS that are very usefull for scientific computings. It is one of the main reason that I still use cuda instead Boost compute(OpenCL).  
Thank you

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-03-21 17:28:58 UTC  
> Url: https://github.com/boostorg/compute/issues/695#issuecomment-288155805  

So, you would like to have C++ wrappers for `clblas*` functions and `clfft*` functions that works with Boost.Compute wrapper-classes for OpenCL objects. And I understand that being able to get those basic OpenCL objects (like `cl_mem`, `cl_context`, `cl_command_queue` etc. ) from Boost.Compute wrapper-classes is not satisfactory for your, yes?

---

## Comment 2

> Username: mahdimcheik  
> Created at: 2017-03-21 17:57:57 UTC  
> Url: https://github.com/boostorg/compute/issues/695#issuecomment-288165406  

Interoperability  between compute and opencl ( and clfft ... cblas) is esay to handle,  I am just suggesting that additionnal wrapper (boost compute style) for clfft clblas and others, would attract more people to use compute.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-03-21 18:03:55 UTC  
> Url: https://github.com/boostorg/compute/issues/695#issuecomment-288167375  

Yeah, I guess you're right. How much work would it require? Do you have any proposition how wrappers should look like?

---

## Comment 4

> Username: mahdimcheik  
> Created at: 2017-03-21 18:13:49 UTC  
> Url: https://github.com/boostorg/compute/issues/695#issuecomment-288170474  

I am not a good programmer, I know what i need to be able to do some simulations in physics domain, But I think that this project : https://github.com/dealias/clfftpp is nice, to bad there is no equivalent version for clBlas or lapack
