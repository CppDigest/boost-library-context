# #441 - missing lib boost_numpy312-vc143-mt-x64-1_85.lib [Open]

> Username: f0451  
> Created at: 2024-06-14 08:24:37 UTC  
> Updated at: 2024-06-17 13:31:58 UTC  
> Url: https://github.com/boostorg/python/issues/441  

error LNK1104: cannot open file boost_numpy312-vc143-mt-x64-1_85.lib  
When installing boost with boostrap + b2, this library was not created anywhere. Also when installing via NuGet this library is not installed.   
I installed numpy using pip install numpy.

---

## Comment 1

> Username: f0451  
> Created at: 2024-06-15 09:07:15 UTC  
> Updated at: 2024-06-17 13:31:58 UTC  
> Url: https://github.com/boostorg/python/issues/441#issuecomment-2169230160  

upd. I installed this one. hopefully it'll help someone.  
  
1. dowload  boostand install in convenient folder for you. For example C:\\boost_1_85_0.  
  
2. install msys and execute the following commands in msys terminal:   
```  
pacman -S mingw-w64-ucrt-x86_64-gcc  
pacman -S mingw-w64-ucrt-x86_64-gdb  
```  
3. open cmd in the C:\\boost_1_85_0 folder and run the command:  
```  
bootstrap.bat gcc  
```  
I don't know why, but if you run this file through vc143 or any other msvc version, the following steps will not create the necessary libraries.  
4. install python, numpy and Visual Studio  
```  
pip install numpy=1.26.0  
```  
5. create `user-config.jam` in one of the following folders:  
```  
'C:\Users\user'  
'C:\boost_1_85_0\tools\build\src\build' 'C:\boost_1_85_0\tools\build\src\contrib' 'C:\boost_1_85_0\tools\build\src\engine' 'C:\boost_1_85_0\tools\build\src\options' 'C:\boost_1_85_0\tools\build\src\tools' 'C:\boost_1_85_0\tools\build\src\util'   
```  
put the path to your python in this file  
```  
using python   
   : 3.12  
   : C:\\Users\\user\\AppData\\Local\\Programs\\Python\\Python312\\python.exe  
   : C:\\Users\\user\\AppData\\Local\\Programs\\Python\\Python312\\include   
   : C:\\Users\\user\\AppData\\Local\\Programs\\Python\\Python312\\libs      
   ;  
```  
6. after after all that work, execute from boost folder in cmd the following command:  
  
```  
b2 --with-python --prefix=C:\\boost_build --debug-configuration -d0 --threading=multi --toolset=msvc-14.3 --build-type=complete install -j4  
```  
  
--with-python -- only boost.python install. Remove it if you need the whole set  
--prefix -- destination folder where the include and lib folders will be installed  
--debug-configuration -d0 -- useful commands for tracking progress   
--threading=multi -j4 -- increases execution speed by utilizing 4 processor cores  
--toolset=msvc-14.3 -- compiler choice. For some reason, if you use the gcc compiler, .a libraries will be created and nothing worked with them.  
--build-type=complete -- the most important flag. it's the one that sets the .dll files  
  
7. If you have done everything correctly, you should see the following in the terminal:  
```  
 -- default adress-model: 64-bit  
 -- default architecture: x86  
```  
Other manuals populate these fields either automatically or by using flags when setting b2. But if you ran bootstrap.bat with msvc, none of these methods will work  
8. Then everyone connects libraries differently, let me give you an example of how I did it.  
in visual studio project open project properties->C/C++->General-> Additional Include Directories. Add paths there:  
```  
C:\Users\user\AppData\Local\Programs\Python\Python312\include  
C:\boost_build\include\boost_1_85_0  
```  
project properties->General-> Configuration Type  
```  
.dll  
```  
project properties->Advanced-> Target file extension  
```  
.pyd  
```  
project properties->Linker->General-> Additional Library Directories  
```  
C:\boost_build\lib  
C:\Users\user\AppData\Local\Programs\Python\Python312\libs  
```  
project properties->Linker->Input-> Additional dependencies  
```  
python312.lib  
```  
9. Place the `boost_python312-vc143-mt-x64-1_85.dll` and `boost_numpy312-vc143-mt-x64-1_85` files in the Release output folder from C:\boost_build\lib
