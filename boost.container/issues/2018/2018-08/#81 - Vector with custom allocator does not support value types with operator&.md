# #81 - Vector with custom allocator does not support value types with operator& [Closed]

> Username: gix  
> Created at: 2018-08-15 15:56:59 UTC  
> Updated at: 2019-08-18 09:35:05 UTC  
> Closed at: 2018-11-11 01:05:07 UTC  
> Url: https://github.com/boostorg/container/issues/81  

Commit 42c6be5887b34b67be4d54041acab12157d080f9 broke use of `vector` with custom allocator and types that define `operator&`.  
  
[Repro on godbolt](https://godbolt.org/g/SDWLV1):  
  
    #include <boost/container/vector.hpp>  
      
    struct X;  
      
    template<typename T>  
    struct XRef  
    {  
        explicit XRef(T* ptr) noexcept : ptr(ptr) {}  
        operator T*() const noexcept { return ptr; }  
        T* ptr;  
    };  
      
    struct X  
    {  
        XRef<X const> operator&() const noexcept { return XRef<X const>(this); }  
        XRef<X>       operator&()       noexcept { return XRef<X>(this); }  
    };  
      
    int main()  
    {  
        boost::container::vector<X> x(5);  
        return 0;  
    }  
  
It comes down to `construct` and `destroy` calls using `&v` instead of `addressof(v)`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-11 01:05:07 UTC  
> Url: https://github.com/boostorg/container/issues/81#issuecomment-437634850  

The following commit fixed the issue some weeks ago:  
   
https://github.com/boostorg/container/commit/f28fe5d33ef3f4ce6959fb1958dfaa336b04d204  
  
Many thanks for the report.

---

## Comment 2

> Username: gix  
> Created at: 2019-08-18 09:35:05 UTC  
> Url: https://github.com/boostorg/container/issues/81#issuecomment-522306319  

This is still not fully fixed. See repro using latest boost 1.70: https://godbolt.org/z/WYJoPv
