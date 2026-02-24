# #339 Disable -Wnon-virtual-dtor for classes derived from boost::system::error_category [Open]

> Username: nomis  
> Created at: 2020-04-05 09:07:24 UTC  
> Updated at: 2020-04-05 09:07:24 UTC  
> Url: https://github.com/boostorg/asio/pull/339  

Compiler warnings for non-virtual destructors are disabled for boost::system::error_category but the issue also affects all derived classes.  
```  
  error: base class ‘class boost::system::error_category’ has accessible non-virtual destructor [-Werror=non-virtual-dtor]  
   class netdb_category : public boost::system::error_category  
         ^~~~~~~~~~~~~~  
  error: ‘class boost::asio::error::detail::netdb_category’ has virtual functions and accessible non-virtual destructor [-Werror=non-virtual-dtor]  
  
  error: base class ‘class boost::system::error_category’ has accessible non-virtual destructor [-Werror=non-virtual-dtor]  
   class addrinfo_category : public boost::system::error_category  
         ^~~~~~~~~~~~~~~~~  
  error: ‘class boost::asio::error::detail::addrinfo_category’ has virtual functions and accessible non-virtual destructor [-Werror=non-virtual-dtor]  
  
  error: base class ‘class boost::system::error_category’ has accessible non-virtual destructor [-Werror=non-virtual-dtor]  
   class misc_category : public boost::system::error_category  
         ^~~~~~~~~~~~~  
  error: ‘class boost::asio::error::detail::misc_category’ has virtual functions and accessible non-virtual destructor [-Werror=non-virtual-dtor]  
```  
Disable -Wnon-virtual-dtor in the same way that Boost.System does.

---
