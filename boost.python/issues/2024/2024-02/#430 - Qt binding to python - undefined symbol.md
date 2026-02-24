# #430 - Qt binding to python - undefined symbol [Closed]

> Username: hanusek  
> Created at: 2024-02-29 11:29:35 UTC  
> Updated at: 2024-03-01 11:24:02 UTC  
> Closed at: 2024-03-01 11:23:53 UTC  
> Url: https://github.com/boostorg/python/issues/430  

Hello!  
I have a problem with binding C++/Qt code to Python.  
When I import my library in Python: `ImportError: my_lib_python_api.so: undefined symbol: _ZTV14FakeAuthThread`  
I notice it's happening when I put `Q_OBJECT` in my class (`FakeAuthThread`).  
How to fix it?  
  
My example lib (file: `PythonClient.cpp`)  
```c++  
class FakeAuthThread  
{  
    Q_OBJECT // it's problem  
  
public:  
    FakeAuthThread() = default;  
  
    void stop() {}  
  
    void run() {}  
};  
  
  
class AuthThreadWrapper  
{  
public:  
    AuthThreadWrapper(){};  
  
    void init()  
    {  
        _thread = new FakeAuthThread();  
    }  
  
private:  
    FakeAuthThread *_thread{nullptr};  
};  
  
  
BOOST_PYTHON_MODULE(my_lib_python_api)  
{  
    Py_Initialize();  
  
    class_<AuthThreadWrapper, boost::noncopyable>("AuthThreadWrapper")  
        .def("init", &AuthThreadWrapper::init);  
}  
```  
  
**CMakeLists.txt**  
```cmake  
 ..  
 set(CMAKE_INSTALL_RPATH_USE_LINK_PATH ON)  
 set(CMAKE_EXPORT_COMPILE_COMMANDS ON)  
 set(CMAKE_CXX_STANDARD 20)  
 set(CMAKE_CXX_STANDARD_REQUIRED ON)  
 set(CMAKE_POSITION_INDEPENDENT_CODE ON)  
 set(CMAKE_VERBOSE_MAKEFILE ON)  
 set(CMAKE_VERBOSE_BUILD ON)  
 set(CMAKE_COMPILE_WARNING_AS_ERROR ON)  
 set(CMAKE_ENABLE_EXPORTS ON)  
   
 find_package(Qt5 COMPONENTS REQUIRED Core Widgets)  
 find_package(Boost REQUIRED COMPONENTS python)  
 find_package(PythonLibs ${PY_VERSION} REQUIRED)  
 find_package(Python3 COMPONENTS Interpreter Development REQUIRED)  
 include_directories(${Python3_INCLUDE_DIRS})  
  
 add_library(my_lib_python_api SHARED python/PythonClient.cpp)  
 target_link_libraries(my_lib_python_api PUBLIC Boost::python Python3::Python Qt5::Core)  
 set_target_properties(my_lib_python_api PROPERTIES PREFIX "")  
```  
**ldd:**  
```  
  linux-vdso.so.1 (0x00007ffddc96c000)  
        libpython3.10.so.1.0 => /lib/x86_64-linux-gnu/libpython3.10.so.1.0 (0x00007f9472f78000)  
        libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f9472d4c000)  
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f9472c63000)  
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f9472c43000)  
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9472a1a000)  
        /lib64/ld-linux-x86-64.so.2 (0x00007f9474360000)  
        libexpat.so.1 => /lib/x86_64-linux-gnu/libexpat.so.1 (0x00007f94729e9000)  
        libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f94729cd000)  
undefined symbol: _ZTV14FakeAuthThread  (./my_lib_python_api.so)  
undefined symbol: _ZTI14FakeAuthThread  (./my_lib_python_api.so)  
```  
  
```  
 $ readelf -Ws --dyn-syms ./my_lib_python_api.so | grep "FakeAuthThread"  
   162: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND _ZTV14FakeAuthThread  
   270: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND _ZTI14FakeAuthThread  
 ```    
   
**Qt version:** 5.15.3  
**Boost version:** 1.77.0  
**Python3_VERSION:** 3.10.12  
**Python3_INCLUDE_DIRS:** /usr/include/python3.10  
**Python3_LIBRARIES:** /usr/lib/x86_64-linux-gnu/libpython3.10.so

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-02-29 16:34:41 UTC  
> Url: https://github.com/boostorg/python/issues/430#issuecomment-1971518580  

Hi Michal,  
  
I suspect the problem has nothing to do with Boost.Python, but rather with your CMake logic: As you are using the `QOBJECT` macro in your `FakeAuthThread` class, you need to process that code with Qt's `moc` to generate (and subsequently compile) additional code. See https://cmake.org/cmake/help/latest/prop_tgt/AUTOMOC.html

---

## Comment 2

> Username: hanusek  
> Created at: 2024-03-01 11:23:53 UTC  
> Updated at: 2024-03-01 11:24:02 UTC  
> Url: https://github.com/boostorg/python/issues/430#issuecomment-1973012723  

I added:  
```cmake  
  set(CMAKE_AUTOMOC ON)  
  set_target_properties(my_lib_python_api PROPERTIES AUTOMOC TRUE)  
```  
And it works.
