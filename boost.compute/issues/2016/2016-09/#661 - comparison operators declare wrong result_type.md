# #661 - comparison operators declare wrong result_type [Open]

> Username: Ulfgard  
> Created at: 2016-09-27 08:13:31 UTC  
> Updated at: 2017-03-22 17:54:29 UTC  
> Url: https://github.com/boostorg/compute/issues/661  

Hi,  
  
In functional/operators.hpp we have  
  
`BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(equal_to, "==", T, T)  
BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(not_equal_to, "!=", T, T)  
BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(greater, ">", T, T)  
BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(less, "<", T, T)  
BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(greater_equal, ">=", T, T)  
BOOST_COMPUTE_DECLARE_BINARY_OPERATOR(less_equal, "<=", T, T)`  
  
declaring a return type T of those functions. Note that the opencl specs says in 6.3 d) (among other things):  
  
> The relational operators greater than (>), less than (<), greater than or equal (>=), and less than or equal (<=) operate on scalar and vector types. If the source operands are a vector float, the result is a vector signed integer.  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-09-27 09:43:42 UTC  
> Url: https://github.com/boostorg/compute/issues/661#issuecomment-249817329  

Yes, technically you are completely right. However, I have to add that it should not result in any bug when using Boost.Compute since those operators are later included (inlined like any other function) in generated OpenCL kernel/s (for example in kernels used in `boost::compute::sort()` algorithm) and `result_type` is deduced, or rather specified, on the OpenCL side.   
  
``` cpp  
vector<float4_>input1, input2;  
// fill the inputs  
vector<int4_> output;  
transform(  
    input1.begin(), input1.end(), input2.begin(), output.begin(), greater<float_4>(), queue  
);  
// this will be essentially evaluated to  
// output[index] = ((input1[index]) > (input2[index]));  
// and there will be no conversion int4 -> float4 -> float2  
// one could assume would happen since result_type of greater<float_4>() is float4_  
```  
  
I don't know if making those operators objects (like `greater<float_4>()`) more "type-accurate" on C++ side would make any difference.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-09-27 09:46:13 UTC  
> Updated at: 2016-09-27 09:46:47 UTC  
> Url: https://github.com/boostorg/compute/issues/661#issuecomment-249817866  

On the other hand, it's better to have the right type, maybe someone will try to deduce something from the `result_type` of the function.. What do you think? It seems pretty easy to fix, I'll look into it.

---

## Comment 3

> Username: Ulfgard  
> Created at: 2016-09-27 11:41:16 UTC  
> Url: https://github.com/boostorg/compute/issues/661#issuecomment-249841020  

In my current application I am going to use it for type deduction (e.g. it could mess up when I want to count the number of elements in a vector that are larger than elements in another vector. This would certainly produce a warning when the result is a float).  
  
Note that the exact rules outlined by the standard are a bit more complicated, e.g. it is not always int but sometimes a smaller or larger integer type. While i would be very happy to get "some" integer type, the exact rules might be more tedious to implement.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-09-27 12:29:44 UTC  
> Url: https://github.com/boostorg/compute/issues/661#issuecomment-249850528  

In that case I'll fix it soon! I don't have time for this today, but I'll look into it tomorrow morning.

---

## Comment 5

> Username: Ulfgard  
> Created at: 2016-09-27 14:06:58 UTC  
> Url: https://github.com/boostorg/compute/issues/661#issuecomment-249875300  

don't worry i can likely only pick it up with the next boost releas eincluding it. So no stress!
