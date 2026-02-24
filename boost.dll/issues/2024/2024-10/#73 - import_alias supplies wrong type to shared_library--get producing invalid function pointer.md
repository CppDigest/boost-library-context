# #73 - import_alias supplies wrong type to shared_library::get producing invalid function pointer. [Closed]

> Username: nkolotov  
> Created at: 2024-10-15 16:32:40 UTC  
> Updated at: 2024-10-15 22:14:28 UTC  
> Closed at: 2024-10-15 22:14:28 UTC  
> Url: https://github.com/boostorg/dll/issues/73  

Assuming that we have a dll exporting `void foo (void)`, an attempt to import and call it using `import_alias` may crash with access violation or cause other problems. This basic example similar to the one present at `import_alias` comment crashes:  
```  
#include <boost/dll/import.hpp>  
  
int main()  
{  
    ::boost::dll::import_alias<void (void)>("TestDll.dll", "foo")();  
}  
```  
[`import_alias` supplies `void * (void)` as type of `shared_library::get`](https://github.com/boostorg/dll/blob/477583869ec304899170728e32ce9f0c5c597af2/include/boost/dll/import.hpp#L228)  
```  
template <class T>  
BOOST_DLL_IMPORT_RESULT_TYPE import_alias(const boost::dll::fs::path& lib, const char* name,  
    load_mode::type mode = load_mode::default_mode)  
{  
    typedef typename boost::dll::detail::import_type<T>::base_type type;  
  
    boost::shared_ptr<boost::dll::shared_library> p = boost::make_shared<boost::dll::shared_library>(lib, mode);  
    return type(p, p->get<T*>(name));  
}  
```  
which causes it to [invoke `aggressive_ptr_cast<void (**)(void)>(get_void(symbol_name))`](https://github.com/boostorg/dll/blob/477583869ec304899170728e32ce9f0c5c597af2/include/boost/dll/shared_library.hpp#L371) incorrectly casting symbol pointer.  
```  
template <typename T>  
    inline typename boost::disable_if_c<boost::is_member_pointer<T>::value || boost::is_reference<T>::value, T&>::type get(const char* symbol_name) const {  
        return *boost::dll::detail::aggressive_ptr_cast<T*>(  
            get_void(symbol_name)  
        );  
    }  
```  
  
[related SO question](https://stackoverflow.com/questions/79090291)
