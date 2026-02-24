# #316 - boost::system::error_code not included in pipe_in/pipe_out when BOOST_PROCESS_USE_STD_FS is defined [Closed]

> Username: qqiangwu  
> Created at: 2023-05-28 04:55:03 UTC  
> Updated at: 2023-06-28 12:24:15 UTC  
> Closed at: 2023-06-28 12:24:15 UTC  
> Url: https://github.com/boostorg/process/issues/316  

version: boost 1.81.0  
compiler: msvc 193 and apple-clang 13  
  
<img width="1630" alt="image" src="https://github.com/boostorg/process/assets/2892107/c8ef0b22-1d13-4ebd-b7b8-9ce72aa4549f">  
  
when BOOST_PROCESS_USE_STD_FS is not defined, it's okay.
