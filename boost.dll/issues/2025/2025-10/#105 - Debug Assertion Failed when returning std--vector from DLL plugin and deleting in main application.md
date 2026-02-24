# #105 - Debug Assertion Failed when returning std::vector from DLL plugin and deleting in main application [Closed]

> Username: orange-cpp  
> Created at: 2025-10-13 13:22:37 UTC  
> Updated at: 2025-10-25 13:12:49 UTC  
> Closed at: 2025-10-25 13:12:49 UTC  
> Url: https://github.com/boostorg/dll/issues/105  

In debug builds, I'm encountering a debug assertion failure when returning a std::vector from a DLL plugin and then deleting it in the main application. The assertion occurs in debug_heap.cpp with the expression __acrt_first_block == header.  
  
Error Message:  
```  
Debug Assertion Failed!  
Program: ...\myapp.exe  
File: minkernel\crts\ucrt\src\appcrt\heap\debug_heap.cpp  
Line: 996  
Expression: __acrt_first_block == header  
```  
**Reproduction Steps:**  
1. Create a DLL plugin that returns a std::vector<T>  
2. Load the plugin in main application using Boost.Dll  
3. Call the function that returns the vector  
4. Let the vector go out of scope or explicitly delete it  
5. Debug assertion triggers in Visual Studio debug build  
  
**Code Example:**  
```cpp  
// Plugin DLL  
class MyPlugin {  
public:  
    virtual std::vector<int> getData() {  
        return std::vector<int>{1, 2, 3, 4, 5};  
    }  
};  
  
// Main application  
auto plugin = boost::dll::import_symbol<MyPlugin>(plugin_path, "plugin");  
auto data = plugin->getData(); // Assertion when this vector is destroyed  
```  
**Root Cause:**  
This happens because the debug CRT in Visual Studio uses different heaps for the main executable and DLLs. When the std::vector is allocated in the DLL but deallocated in the main executable, the debug heap detects a cross-heap deletion, which is considered an error.  
  
  
What to do with this?

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-10-24 15:49:42 UTC  
> Url: https://github.com/boostorg/dll/issues/105#issuecomment-3443842342  

Looks like this case is covered by the first bullet at https://www.boost.org/doc/libs/latest/doc/html/boost_dll/missuses.html  
  
"Your plugin and program must use the same Standard C++ and C libraries"  
  
Probably that paragraph could be improved somehow to better describe the above case. Any suggestions?

---

## Comment 2

> Username: orange-cpp  
> Created at: 2025-10-25 13:12:49 UTC  
> Url: https://github.com/boostorg/dll/issues/105#issuecomment-3446689899  

> Looks like this case is covered by the first bullet at https://www.boost.org/doc/libs/latest/doc/html/boost_dll/missuses.html  
>   
> "Your plugin and program must use the same Standard C++ and C libraries"  
>   
> Probably that paragraph could be improved somehow to better describe the above case. Any suggestions?  
  
Ye, did some research. Issue was in CRT linkage. C++ std was the same but it was linked statically for both binary causing to have 2 heaps instead of one. I've switched to MultiThreadedDLL and problem was fixed.
