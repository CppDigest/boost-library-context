# #144 - Compilation breaks in optimized mode [Closed]

> Username: zofer1  
> Created at: 2025-10-28 15:23:21 UTC  
> Updated at: 2025-10-29 18:18:39 UTC  
> Closed at: 2025-10-29 18:18:39 UTC  
> Url: https://github.com/boostorg/random/issues/144  

In file boost/random/mersenne_twister.hpp  
in case assert is is defined and empty, this code fails to compile:  
  
            if(tmp.i >= n) tmp.twist();  
            if(j == num_bits - 1)  
                assert((tmp.x[tmp.i] & 1) == 1);  
            else  
                assert((tmp.x[tmp.i] & 1) == 0);  
  
It is a simple fix to make it more organized for the compiler:  
  
            if(tmp.i >= n) tmp.twist();  
            if(j == num_bits - 1)  
            {  
                assert((tmp.x[tmp.i] & 1) == 1);  
            }  
            else  
            {  
                assert((tmp.x[tmp.i] & 1) == 0);  
           }  
With this it compiles nicely

---

## Comment 1

> Username: mborland  
> Created at: 2025-10-29 07:14:07 UTC  
> Url: https://github.com/boostorg/random/issues/144#issuecomment-3460139401  

I can roll the branch condition into the assert to get rid of the branches entirely. What toolchain gave you errors? This code is at least 15 years old. The empty branches should be optimized away along with the asserts
