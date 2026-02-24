# #5 Remove references to std::exception when BOOST_NO_EXCEPTIONS is defined [Closed]

> Username: felipealmeida  
> Created at: 2016-09-16 02:22:19 UTC  
> Updated at: 2016-10-02 15:37:14 UTC  
> Closed at: 2016-10-02 15:37:14 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/5  

Removed #include directive for <exception> and std::exception from  
boost::throw_exception user-defined function declaration.  
  
This is necessary in platforms which do not have std::exception when  
exceptions are disabled, such as AVR 8-bit CPUs (arduinos).

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-09-16 07:51:47 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/5#issuecomment-247539903  

This is not correct, it will break all programs using `throw_exception` with exceptions disabled, because `throw_exception(x)` will now require `throw_exception(X const& x)` to be defined.  
  
We need to introduce a separate config macro for the absence of `std::exception` - such as `BOOST_NO_STD_EXCEPTION` - and use that.

---

## Comment 2

> Username: felipealmeida  
> Created_at: 2016-09-16 16:10:29 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/5#issuecomment-247641278  

Hello @pdimov   
  
What comprehends in adding this config macro? Should I add it to boostorg/config? And where is it documented? (which repos?).

---

## Comment 3

> Username: zajo  
> Created_at: 2016-09-16 19:18:58 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/5#issuecomment-247684584  

On Fri, Sep 16, 2016 at 9:10 AM, Felipe Magno de Almeida <  
notifications@github.com> wrote:  
  
> What comprehends in adding this config macro? Should I add it to  
> boostorg/config? And where is it documented? (which repos?).  
>   
> As a first step you can add the ifdef for the new BOOST_NO_STD_EXCEPTION in  
> throw_exception.hpp, that way people who do work on platforms that don't  
> have std::exception will have a workaround. As for automatically defining  
> the macro in config, searching for BOOST_NO_EXCEPTIONS will get you started.  
  
Thanks,  
Emil

---

## Comment 4

> Username: pdimov  
> Created_at: 2016-09-16 19:33:34 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/5#issuecomment-247687847  

The procedure for adding a new macro to Boost.Config is documented here:  
  
http://www.boost.org/doc/libs/1_61_0/libs/config/doc/html/boost_config/guidelines_for_boost_authors.html#boost_config.guidelines_for_boost_authors.adding_new_defect_macros

---
