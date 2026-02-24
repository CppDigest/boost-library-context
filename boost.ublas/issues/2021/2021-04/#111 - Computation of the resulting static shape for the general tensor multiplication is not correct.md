# #111 - Computation of the resulting static shape for the general tensor multiplication is not correct [Open]

> Username: bassoy  
> Created at: 2021-04-29 06:50:27 UTC  
> Updated at: 2021-04-29 06:50:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/111  

Lambda function https://github.com/boostorg/ublas/blob/32795e124ba66192f4ee3a4aaea0b76ae38a340a/include/boost/numeric/ublas/tensor/functions.hpp#L359  
performs a wrong computation of the resulting static shape invoked in line  
https://github.com/boostorg/ublas/blob/32795e124ba66192f4ee3a4aaea0b76ae38a340a/examples/tensor/multiply_tensors_product_function.cpp#L312
