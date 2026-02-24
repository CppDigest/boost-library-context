# #365 - generate_int_div_0 does not work on aarch64 [Open]

> Username: hdu-sdlzx  
> Created at: 2022-12-07 01:29:07 UTC  
> Updated at: 2022-12-07 01:29:07 UTC  
> Url: https://github.com/boostorg/test/issues/365  

We use 1/0 to generate a divide-by-zero exception and check if exec monitor works correctly, but this does not work on aarch64, maybe we should skip this test?  
  
https://github.com/boostorg/test/blob/develop/example/exec_mon_example.cpp  
https://stackoverflow.com/questions/57894010/division-by-zero-not-causing-runtime-exception-on-nvidia-jetson
