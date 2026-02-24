# #159 - Bug: function_traits doesn't work with non-default calling convention [Open]

> Username: impugachev  
> Created at: 2021-05-13 10:17:43 UTC  
> Updated at: 2021-05-13 10:22:47 UTC  
> Url: https://github.com/boostorg/type_traits/issues/159  

For example: (x86 compiler)  
`using Arg = boost::function_traits<int(__stdcall)(int)>::arg1_type;`  
  
Sample:  
https://godbolt.org/z/vPfz4Yjvh
