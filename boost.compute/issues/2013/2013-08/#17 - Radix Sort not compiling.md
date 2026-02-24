# #17 - Radix Sort not compiling [Closed]

> Username: komocode  
> Created at: 2013-08-10 00:22:49 UTC  
> Updated at: 2014-04-20 17:27:01 UTC  
> Closed at: 2014-04-20 17:27:01 UTC  
> Url: https://github.com/boostorg/compute/issues/17  

Ran the example on the main github page, the radix sort fails to build. I'm getting a return value of -11 which is CL_BUILD_PROGRAM_FAILURE.  
Debug shows:  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
# define K2 (1 << K)  
# define RADIX_MASK ((((T)(1)) << K) - 1)  
# define SIGN_BIT ((sizeof(T) \* CHAR_BIT) - 1)  
  
inline uint radix(const T x, const uint low_bit)  
{  
# if defined(IS_FLOATING_POINT)  
  
```  
const T mask = -(x >> SIGN_BIT) | (((T)(1)) << SIGN_BIT);  
return ((x ^ mask) >> low_bit) & RADIX_MASK;  
```  
# elif defined(IS_SIGNED)  
  
```  
return ((x ^ (((T)(1)) << SIGN_BIT)) >> low_bit) & RADIX_MASK;  
```  
# else  
  
```  
return (x >> low_bit) & RADIX_MASK;  
```  
# endif  
  
}  
__kernel void count(__global const T _input,  
                    const uint input_size,  
                    __global uint *global_counts,  
                    __global uint *global_offsets,  
                    __local uint *local_counts,  
                    const uint low_bit)  
{  
    const uint gid = get_global_id(0);  
    const uint lid = get_local_id(0);  
    if(lid < K2){  
        local_counts[lid] = 0;  
    }  
    barrier(CLK_LOCAL_MEM_FENCE);  
    if(gid < input_size){  
        T value = input[gid];  
        uint bucket = radix(value, low_bit);  
        atomic_inc(local_counts + bucket);  
    }  
    barrier(CLK_LOCAL_MEM_FENCE);  
    if(lid < K2){  
        global_counts[K2_get_group_id(0) + lid] = local_counts[lid];  
        if(get_group_id(0) == (get_num_groups(0) - 1)){  
            global_offsets[lid] = local_counts[lid];  
        }  
    }  
}  
__kernel void scan(__global const uint *block_offsets,  
                   __global uint *global_offsets,  
                   const uint block_count)  
{  
    __global const uint *last_block_offsets =  
        block_offsets + K2 \* (block_count - 1);  
    uint sum = 0;  
    for(uint i = 0; i < K2; i++){  
        uint x = global_offsets[i] + last_block_offsets[i];  
        global_offsets[i] = sum;  
        sum += x;  
    }  
}  
__kernel void scatter(__global const T *input,  
                      const uint input_size,  
                      const uint low_bit,  
                      __global const uint *counts,  
                      __global const uint *global_offsets,  
                      __global T *output)  
{  
    const uint gid = get_global_id(0);  
    const uint lid = get_local_id(0);  
    T value;  
    uint bucket;  
    __local uint local_input[BLOCK_SIZE];  
    if(gid < input_size){  
        value = input[gid];  
        bucket = radix(value, low_bit);  
        local_input[lid] = bucket;  
    }  
    __local uint local_counts[(1 << K)];  
    if(lid < K2){  
        local_counts[lid] = counts[get_group_id(0) \* K2 + lid];  
    }  
    barrier(CLK_LOCAL_MEM_FENCE);  
    if(gid >= input_size){  
        return;  
    }  
    uint offset = global_offsets[bucket] + local_counts[bucket];  
    uint local_offset = 0;  
    for(uint i = 0; i < lid; i++){  
        if(local_input[i] == bucket)  
            local_offset++;  
    }  
    output[offset + local_offset] = value;  
}  
  
--- build log ---  
error: definition of macro 'K' conflicts with an identifier used in the precompiled header

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-08-12 23:46:21 UTC  
> Url: https://github.com/boostorg/compute/issues/17#issuecomment-22534241  

That's strange. Can you let me know what OpenCL implementation and device you're using?

---

## Comment 2

> Username: skycaptain  
> Created at: 2013-08-23 19:18:24 UTC  
> Url: https://github.com/boostorg/compute/issues/17#issuecomment-23185246  

I'm also getting this error. Running on OSx 10.8 and using the standard OpenCL-Implementation from Apple.  
Platform 'Apple'  
CPU Device: Intel(R) Core(TM) i7-2760QM CPU @ 2.40GHz  
GPU Device: ATI Radeon HD 6770M

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-09-08 02:24:36 UTC  
> Url: https://github.com/boostorg/compute/issues/17#issuecomment-24013727  

@komocode @skycaptain I've pushed a potential fix to the radix-sort-compilation-fix branch. The commit is here: 0b80442c0e721f5feccca81d68cacb4e19545c1f. I've just changed the name the definition from 'K' to 'K_BITS' which hopefully should avoid this issue. Can you guys try it out?

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-04-20 17:27:01 UTC  
> Url: https://github.com/boostorg/compute/issues/17#issuecomment-40899952  

Hmm, never heard back from either of you. Anyway, I've pushed the change to rename `K` to `K_BITS` in the radix-sort program (commit here: a78212fdde9254c18cabacd4388ca7106dbbcdbd). Hope that works for you and other Apple users. Please re-open this issue if you encounter this error again.
