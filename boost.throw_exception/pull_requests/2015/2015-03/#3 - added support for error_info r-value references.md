# #3 added support for error_info r-value references [Closed]

> Username: rnburn  
> Created at: 2015-03-21 05:46:44 UTC  
> Updated at: 2018-09-27 12:25:00 UTC  
> Closed at: 2018-09-27 12:25:00 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3  

adds support to transport exception data using move semantics  
  
Example:  
  
```  
boost::exception e;  
typedef boost::error_info<struct tag,Data> ErrorInfo;  
Data data;  
e << ErrorInfo(std::move(data));  
throw e;  
```

---

## Comment 1

> Username: rnburn  
> Created_at: 2015-03-21 06:39:56 UTC  
> Updated_at: 2015-03-22 03:38:35 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-84266576  

1) can I include boost/config.hpp?   
  
2) Those overloads are necessary to prevent the general r-value reference version of set_info from capturing the throw_function, throw_file, and throw_line specializations.   
  
The general function won't work with those specializations, so they either need overloads that will be preferred in all scenarios or some kind of enable_if to disable the general r-value reference version of set_info.   
  
Adding their own r-value reference overloads seems like the easiest solution to me.

---

## Comment 2

> Username: rnburn  
> Created_at: 2015-03-22 03:40:47 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-84511003  

I added an include for boost/config.hpp so that BOOST_NO_CXX11_RVALUE_REFERENCES will be defined

---

## Comment 3

> Username: zajo  
> Created_at: 2015-03-22 04:53:48 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-84521564  

I'm sorry, including headers in boost/exception/exception.hpp is a no-go. I'll take a closer look myself, but I think it's possible to move the set_info && overloads for throw_function, throw_file and throw_line in boost/exception/info.hpp, where it is okay to include boost/config.hpp.

---

## Comment 4

> Username: rnburn  
> Created_at: 2015-03-22 05:38:05 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-84525103  

How would you handle the friend declarations since the functions access private data members of exception?

---

## Comment 5

> Username: zajo  
> Created_at: 2015-03-25 06:32:53 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-85873740  

I've pushed edited version for review in the rv_test branch of the exception repo. It requires a trivial change to boost/exception/exception.hpp, which has been pushed into the develop branch of the throw_exception repo.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-09-26 13:05:59 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/3#issuecomment-424707700  

Is this PR still relevant, or can it be closed?

---
