# #1355 - Unable to Launch due to Launch Failed: Binary not found [Closed]

> Username: Ronje0993  
> Created at: 2018-12-07 05:33:41 UTC  
> Updated at: 2018-12-13 10:24:18 UTC  
> Closed at: 2018-12-13 10:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1355  

Hi,  
I am using beast to connect to a webserver "echo.websocket.org" as in the example [https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync/websocket_client_sync.cpp](url)  
  
I am have compiled and built the code with 0 errors in Eclipse. But I am unable to launch the code (**error: Launch Failed: Binary not found**).  
For compilation I had to do the following:  
  
Project > Properties > C/C++ Build > Settings  
  
1. Include the path to boost library in _GCC C++ Compiler >Includes_  
2. Check the (-fPIC) and (-pthread) check boxes in _GCC C++ Compiler >Miscellaneous_  
3. add _-std=c++11_ in _GCC C++ Compiler_  **Command** field.  
4. add _pthread_ and _boost_system_ in the _GCC C++ Linker >Libraries_ **Libraries(-l)**  
5. add lib folder in **Library search path (-L)** in _GCC C++ Linker >Libraries_  
6. check the Shared (-shared) check box on the top in  _GCC C++ Linker > Shared Library Settings_  
  
  
The Binary which is compiled by eclipse, I tried to run that with the terminal and got the error **Segmentation fault (core dumped)**  
  
I am using Boost libraries for the first time (so something that should be obvious might not be in my head) - Having said that :)
