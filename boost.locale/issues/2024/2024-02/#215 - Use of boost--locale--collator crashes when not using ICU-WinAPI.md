# #215 - Use of boost::locale::collator crashes when not using ICU/WinAPI [Closed]

> Username: Flamefire  
> Created at: 2024-02-06 18:23:16 UTC  
> Updated at: 2024-02-08 18:03:37 UTC  
> Closed at: 2024-02-08 18:03:37 UTC  
> Url: https://github.com/boostorg/locale/issues/215  

However, when I try to use it, this snippet crashes the application:   
```  
// Initialize boost::locale  
    boost::locale::generator gen;  
    std::locale loc = gen("");  
    const boost::locale::collator<char>& coll = std::use_facet<boost::locale::collator<char>>(loc);  
      
    auto result = coll.compare(boost::locale::collate_level::secondary, str1, str2);  
    if (result < 0) return -1;  
    if (result > 0) return 1;  
    return 0;  
```  
In the file: `collator.hpp` - see the screenshots from Xcode debugger.   
![Skjermbilde 2024-01-04 kl  10 56 40](https://github.com/boostorg/locale/assets/3278226/58115273-2262-491c-a109-48af29db38db)  
![Skjermbilde 2024-01-04 kl  10 57 32](https://github.com/boostorg/locale/assets/3278226/e67d9126-7a71-4eda-8242-e6077d2975f0)  
![Skjermbilde 2024-01-04 kl  10 57 20](https://github.com/boostorg/locale/assets/3278226/66d0c809-f926-4298-9d45-934906f87a0a)  
![Skjermbilde 2024-01-04 kl  10 57 01](https://github.com/boostorg/locale/assets/3278226/834d97d3-0ce8-48ca-9f89-7c083f001fc9)  
  
_Originally posted by @olekrisek in https://github.com/boostorg/locale/issues/210#issuecomment-1876819426_

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-02-06 18:26:24 UTC  
> Url: https://github.com/boostorg/locale/issues/215#issuecomment-1930522529  

It looks like the virtual function dispatch gets messed up in your use case as indicated by `__cxxabiv1::__si_class_type_info` in the call stack which doesn't look right.  
  
The actual type of `boost::locale::collator<char>`, i.e. of `*this` inside the `compare` call is likely one from `boost::locale::impl_std` or `boost::locale::impl_posix`. And those only derive from `std::collate` but not from `boost::locale::collate` which means they do not implement that `do_compare` function taking a `level` argument. And seemingly `std::use_facet` does not check the actual type but only the id (on OSX/libc++).     
In particular only the WinAPI and the ICU backends implement that facet and you don't even have either build which explains this.  
  
So in a semi-correct version your use of `std::use_facet<boost::locale::collator<char>>` should throw a `std::bad_cast`. At least until the Posix and Std backends implement that facet which I don't think is possible.
