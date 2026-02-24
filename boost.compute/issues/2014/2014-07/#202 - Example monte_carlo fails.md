# #202 - Example monte_carlo fails [Closed]

> Username: f-koehler  
> Created at: 2014-07-29 15:39:48 UTC  
> Updated at: 2014-07-31 12:38:10 UTC  
> Closed at: 2014-07-31 12:38:10 UTC  
> Url: https://github.com/boostorg/compute/issues/202  

I did not look into the code yet but the example monte_carlo fails with the exception:  
  
```  
boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >  
```  
  
Line 59 in monte_carlo fails. It contains a `count_if` command:  
  
``` cpp  
size_t count = compute::count_if(start, end, is_in_unit_circle, queue);  
```  
  
Cheers, fabian

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-30 01:40:00 UTC  
> Url: https://github.com/boostorg/compute/issues/202#issuecomment-50563978  

Hmm, can you post the full output? It should include the error string which will help in tracking this down.

---

## Comment 2

> Username: f-koehler  
> Created at: 2014-07-30 06:36:29 UTC  
> Url: https://github.com/boostorg/compute/issues/202#issuecomment-50578888  

Here is my complete output. It's actually a program build failure  
  
```  
device: Pitcairn  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Build Program Failure  
zsh: abort      ./monte_carlo  
```

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-07-30 14:55:20 UTC  
> Url: https://github.com/boostorg/compute/issues/202#issuecomment-50625912  

Interesting. Can you try adding `#define BOOST_COMPUTE_DEBUG_KERNEL_COMPILATION` to the top of the file and then post the output?

---

## Comment 4

> Username: f-koehler  
> Created at: 2014-07-30 15:17:28 UTC  
> Url: https://github.com/boostorg/compute/issues/202#issuecomment-50629217  

```  
device: Pitcairn  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
  
inline bool is_in_unit_circle(uint2 point){ const float x = point.x / (float) UINT_MAX - 1; const float y = point.y / (float) UINT_MAX - 1; return (x*x + y*y) < 1.0f; }  
  
  
__kernel void initial_reduce(const uint count, __global ulong* output, __global uint2* _buf0)  
{  
const uint offset = get_group_id(0) * VPT * TPB;  
const uint lid = get_local_id(0);  
__local ulong scratch[TPB];  
ulong sum = 0;  
for(uint i = 0; i < VPT; i++){  
    if(offset + lid + i*TPB < count){  
        sum = sum + convert_ulong(is_in_unit_circle(_buf0[offset+lid+i*TPB]));  
    }  
}  
scratch[lid] = sum;  
for(int i = 1; i < TPB; i <<= 1){  
   barrier(CLK_LOCAL_MEM_FENCE);  
   uint mask = (i << 1) - 1;  
   if((lid & mask) == 0){  
       scratch[lid] += scratch[lid+i];  
   }  
}  
if(lid == 0){  
    output[get_group_id(0)] = scratch[0];  
}  
  
}  
  
--- build log ---  
"/tmp/OCLqbDTsw.cl", line 17: error: more than one instance of overloaded  
          function "convert_ulong" matches the argument list:  
            function "convert_ulong(char) C++"  
            function "convert_ulong(uchar) C++"  
            function "convert_ulong(short) C++"  
            function "convert_ulong(ushort) C++"  
            function "convert_ulong(int) C++"  
            function "convert_ulong(uint) C++"  
            function "convert_ulong(long) C++"  
            function "convert_ulong(ulong) C++"  
            function "convert_ulong(float) C++"  
            function "convert_ulong(double) C++"  
            argument types are: (bool)  
          sum = sum + convert_ulong(is_in_unit_circle(_buf0[offset+lid+i*TPB]));  
                      ^  
  
1 error detected in the compilation of "/tmp/OCLqbDTsw.cl".  
  
Frontend phase failed compilation.  
  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Build Program Failure  
zsh: abort      ./example/monte_carlo  
```

---

## Comment 5

> Username: f-koehler  
> Created at: 2014-07-31 12:38:10 UTC  
> Url: https://github.com/boostorg/compute/issues/202#issuecomment-50752274  

This is solved by [PR #206](https://github.com/kylelutz/compute/pull/206).
