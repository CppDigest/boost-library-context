# #30 - Segfault when running bench [Closed]

> Username: madmongo1  
> Created at: 2020-01-08 09:19:00 UTC  
> Updated at: 2020-02-14 01:46:41 UTC  
> Closed at: 2020-02-14 01:46:41 UTC  
> Url: https://github.com/boostorg/json/issues/30  

Build Host: Fedora 31  
Target: Fedora 31 (Linux)  
Build System: CMake  
Toolchains: gcc-9 c++11, clang-9 c++14  
Branch: `develop`  
  
**Synopsys**  
Misuse of RapidJSON public interface results in internal assertion in RapidJSON library  
  
**Steps to Reproduce**  
  
`$ cmake -DCMAKE_BUILD_TYPE=Debug ...`  
build the project as normal  
`$ bench <source_root>/bench/data/canada.json`  
  
**Detail**  
  
The problem can be isolated to the benchmark enabled by   
`vi.emplace_back(new rapidjson_crt_impl);` in function `main` of `bench.cpp`  
  
The `parse` member function of the benchmark contains the following lines:  
  
```  
            CrtAllocator alloc;  
            GenericDocument<  
                UTF8<>, CrtAllocator> d(&alloc);  
            d.Clear();  <<<--- PROBLEM IS HERE  
```  
In RapidJSON, the implementation of `GenericDocument<>::Clear()` executes an assertion that the internal value representing the document is an `Array`:  
  
```  
    void Clear() {  
        RAPIDJSON_ASSERT(IsArray());   
        GenericValue* e = GetElementsPointer();  
        for (GenericValue* v = e; v != e + data_.a.size; ++v)  
            v->~GenericValue();  
        data_.a.size = 0;  
    }  
```  
  
In the above use pattern, the document is not an Array, as it has not been initialised at all. It is actually null.  
  
The assertion fails, resulting in either SEGFAULT of a debugger break.  
  
**Solution**  
  
remove the line `d.Clear();`  
  
**Side Effects of Solution**  
  
None. The document is re-created each time around the loop in any case.
