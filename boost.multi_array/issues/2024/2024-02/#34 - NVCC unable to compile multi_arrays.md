# #34 - NVCC unable to compile multi_arrays [Open]

> Username: VBarone99  
> Created at: 2024-02-02 18:11:49 UTC  
> Updated at: 2024-02-02 18:16:53 UTC  
> Url: https://github.com/boostorg/multi_array/issues/34  

I am trying to use multi_arrays in a CUDA GPU project and NVCC is unable to compile it.  
NVCC version is 12.3 (error also occurs with other versions)  
Boost version is 1.84.0 (error also occurs with other versions)  
  
I used the following command to compile:  
`nvcc -o C:/_repro/output.exe C:/_repro/main.cu -IC:/boost_1_84_0`  
  
I am using the following code (main.cu):  
```C++  
#include <boost/multi_array.hpp>  
  
int main()  
{  
	boost::multi_array<int, 3> my_arr;  
	return 0;  
}  
```  
  
I get the following errors:  
```  
C:/boost_1_84_0\boost/multi_array/base.hpp(408): error C2988: unrecognizable template declaration/definition  
C:/boost_1_84_0\boost/multi_array/base.hpp(408): error C2143: syntax error: missing ')' before '<'  
C:/boost_1_84_0\boost/multi_array/base.hpp(408): error C2143: syntax error: missing ';' before '<'  
C:/boost_1_84_0\boost/multi_array/base.hpp(408): error C2238: unexpected token(s) preceding ';'  
C:/boost_1_84_0\boost/multi_array/base.hpp(408): error C2059: syntax error: '<'  
C:/boost_1_84_0\boost/multi_array/base.hpp(414): error C2059: syntax error: ')'  
C:/boost_1_84_0\boost/multi_array/base.hpp(494): error C2143: syntax error: missing ';' before '}'  
C:/boost_1_84_0\boost/multi_array/base.hpp(494): error C2238: unexpected token(s) preceding ';'  
```  
  
Several more errors occur when I attempt to use further multi_array functionality such as extents and indexing, this is just a minimal example. Additionally, the code compiles just fine when you change the file extention to .cpp, but I assume this is just NVCC falling back on the default cl.exe compiler. I specifically need multi_arrays to compile within a .cu file along with CUDA device code.  
  
Any help is much appreciated, thank you.
