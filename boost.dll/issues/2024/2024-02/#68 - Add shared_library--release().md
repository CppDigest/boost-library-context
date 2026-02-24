# #68 - Add shared_library::release() [Closed]

> Username: manipuladordedados  
> Created at: 2024-02-16 23:58:03 UTC  
> Updated at: 2025-01-28 15:20:48 UTC  
> Closed at: 2025-01-16 16:08:29 UTC  
> Url: https://github.com/boostorg/dll/issues/68  

Some plugins are never safe to unload (e.g. if you're using Qt, Qt will start threads that are never joined and register thread-local variables in other threads, and it's unsafe to unload Qt code then). I'd like to use Boost.DLL for its portable API, but right now I need to use code like this to provoke an intentional leak:  
  
```cpp  
auto lib = new dll::shared_library;  
// ...  
```  
  
Here's code in the wild making use of this workaround: https://gitlab.com/emilua/qt6/-/blob/23baacf56b79d8355082df97bd1d0c2fa6c64ec1/src/qt_handle.cpp#L25  
  
I'd rather do something like this:  
  
```cpp  
dll::shared_library lib;  
// ...  
lib.release();  
```  
  
Can we have the release() function for shared_library?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-19 08:07:24 UTC  
> Updated at: 2024-02-19 08:07:52 UTC  
> Url: https://github.com/boostorg/dll/issues/68#issuecomment-1951902617  

`shared_library` follows the `std::shared_ptr` design. `std::shared_ptr` does not have a release() function.  
  
Have you tried sokething like this:  
```cpp  
std::vector<shared_library>& no_unload_registry() {  
    static std::vector<shared_library> reg;  
    return reg;  
}  
```  
When there's a need to load library that does not unload till the end of program, do `no_unload_registry().emplace_back(parameters_for_shared_library)`

---

## Comment 2

> Username: manipuladordedados  
> Created at: 2024-02-28 00:44:29 UTC  
> Url: https://github.com/boostorg/dll/issues/68#issuecomment-1967979158  

> shared_library follows the std::shared_ptr design. std::shared_ptr does not have a release() function.  
  
Maybe the solution here should be different then. shared_ptr does allow one to customize the destructor, and you can even use a no-op closure here. What about something similar? Maybe load_mode::dont_unload?

---

## Comment 3

> Username: manipuladordedados  
> Created at: 2025-01-16 20:54:40 UTC  
> Url: https://github.com/boostorg/dll/issues/68#issuecomment-2596869934  

shouldn't this decision be discussed in the mailing list?

---

## Comment 4

> Username: apolukhin  
> Created at: 2025-01-28 15:20:47 UTC  
> Url: https://github.com/boostorg/dll/issues/68#issuecomment-2619309332  

> shouldn't this decision be discussed in the mailing list?  
  
That won't help @manipuladordedados   
  
What would help: write to the Qt developers and ask for an ability to load a Qt via dlopen. Describe the issue with thread joins. until then, try the workaround from FAQ https://www.boost.org/doc/libs/develop/doc/html/boost_dll/f_a_q_.html
