# #125 - Missing #include [Open]

> Username: reddwarf69  
> Created at: 2022-09-26 09:08:56 UTC  
> Updated at: 2022-09-26 09:08:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/125  

native.hpp uses base_type (https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/include/boost/safe_numerics/native.hpp#L25), but it does not #include safe_common.hpp, where it's defined (https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/include/boost/safe_numerics/safe_common.hpp#L21).
