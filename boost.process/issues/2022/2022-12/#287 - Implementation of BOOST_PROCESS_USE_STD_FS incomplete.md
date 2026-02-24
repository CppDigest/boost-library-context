# #287 - Implementation of BOOST_PROCESS_USE_STD_FS incomplete [Closed]

> Username: JasonColeRoku  
> Created at: 2022-12-22 14:39:37 UTC  
> Updated at: 2023-02-06 11:09:40 UTC  
> Closed at: 2023-02-06 11:09:40 UTC  
> Url: https://github.com/boostorg/process/issues/287  

In https://github.com/boostorg/process/blob/boost-1.80.0/include/boost/process/detail/posix/search_path.hpp#L30  
  
```  
        boost::system::error_code ec;  
        bool file = boost::process::filesystem::is_regular_file(p, ec);  
```  
With `BOOST_PROCESS_USE_STD_FS` defined, compilation fails due to a mismatch of the error_code parameter:  
```  
no known conversion for argument 2 from 'boost::system::error_code' to 'std::error_code&'  
  257 |   is_regular_file(const path& __p, error_code& __ec) noexcept  
      |                                    ~~~~~~~~~~~~^~~~  
```
