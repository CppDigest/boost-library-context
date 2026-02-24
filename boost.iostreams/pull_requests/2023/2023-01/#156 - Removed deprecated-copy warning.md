# #156 Removed deprecated-copy warning [Open]

> Username: chrisse74  
> Created at: 2023-01-17 11:42:45 UTC  
> Updated at: 2023-08-15 22:48:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/156  

Fixes several deprecated-copy warnings by using the BOOST_DEFAULTED_FUNCTION() macro.

---

## Comment 1

> Username: mclow  
> Created_at: 2023-01-22 00:52:39 UTC  
> Url: https://github.com/boostorg/iostreams/pull/156#issuecomment-1399368634  

This patch fails on C++03, because you need to provide a body for the function in the case that the compiler doesn't support `= default`.    
  
So, for example,  if you have:  
```  
template<typename T>  
class device_close_operation {  
public:  
    typedef void result_type;  
    device_close_operation(T& t, BOOST_IOS::openmode which)   
        : t_(t), which_(which)   
        { }  
    void operator()() const { boost::iostreams::close(t_, which_); }  
private:  
    BOOST_DELETED_FUNCTION(device_close_operation& operator=(const device_close_operation&))  
    T&                   t_;  
    BOOST_IOS::openmode  which_;  
};  
```  
  
you can't just add:  
`BOOST_DEFAULTED_FUNCTION(device_close_operation(const device_close_operation&), {});`  
because that leaves the reference member `t_` uninitialized.  
  
Instead, you need to say something like:  
`BOOST_DEFAULTED_FUNCTION(device_close_operation(const device_close_operation& rhs), : t_(rhs.t_) { which_ = rhs.which_; });`

---

## Comment 2

> Username: chrisse74  
> Created_at: 2023-01-26 09:10:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/156#issuecomment-1404724062  

Thanks for the explanation @mclow - working on it.

---

## Comment 3

> Username: chrisse74  
> Created_at: 2023-01-26 10:26:33 UTC  
> Url: https://github.com/boostorg/iostreams/pull/156#issuecomment-1404808345  

I had to drop three BOOST_DEFAULTED_FUNCTION() declarations (but commented this) to avoid "Uninitialized reference member" errors. Unfortunately the initializer list can only take one parameter, when using BOOST_DEFAULTED_FUNCTION.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-15 22:48:32 UTC  
> Updated_at: 2023-08-15 22:48:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/156#issuecomment-1679727304  

I have also been trying to fix couple of these warnings in #154 (but less than this pull request). Would it be possible to consider one of these pull request ?  
  
Also, i know some boost libraries dropped C++03 support in recent releases, I don't know if this is the case of boost iostream too.

---
