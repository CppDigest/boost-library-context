# #375 - `#undef _DEBUG` issues [Open]

> Username: ssrlive  
> Created at: 2021-10-21 11:57:53 UTC  
> Updated at: 2021-10-21 11:57:53 UTC  
> Url: https://github.com/boostorg/python/issues/375  

Hi guys,  
  
Can you remove this line, which can cause the boost-python always link to release version python.dll not python_d.dll   
  
https://github.com/boostorg/python/blob/500194edb7833d0627ce7a2595fec49d0aae2484/include/boost/python/detail/wrap_python.hpp#L45  
  
my **DEBUG** build like this  
  
![image](https://user-images.githubusercontent.com/30760636/138250480-bff5b787-25c1-41ba-b598-d9c9b1962b64.png)
