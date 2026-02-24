# #135 - Visual Studio natvis needs updating for std::error_code [Closed]

> Username: vinniefalco  
> Created at: 2025-11-02 04:12:49 UTC  
> Updated at: 2025-11-02 18:04:16 UTC  
> Closed at: 2025-11-02 18:04:16 UTC  
> Url: https://github.com/boostorg/system/issues/135  

```  
Natvis: C:\Users\Vinnie\src\boost\libs\system\extra\boost_system.natvis(15,10): Error: no operator "==" matches these operands  
built-in operator==(<promoted arithmetic>, <promoted arithmetic>) does not match because argument #1 does not match parameter  
built-in operator==(<nullptr>, <nullptr>) does not match because argument #1 does not match parameter  
    Error while evaluating 'lc_flags_ == 1 && ((std::error_code*)d2_)->_Mycat->_Addr==1' in the context of type 'boost_url_unit_tests.exe!boost::system::error_code'.  
```  
  
The visualizer depends on the msvc implementation of `std::error_code`, which recently changed

---

## Comment 1

> Username: pdimov  
> Created at: 2025-11-02 10:00:54 UTC  
> Url: https://github.com/boostorg/system/issues/135#issuecomment-3477817476  

Since you wrote the original visualizer, maybe you could make a PR that fixes it?
