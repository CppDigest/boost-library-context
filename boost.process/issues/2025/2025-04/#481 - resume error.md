# #481 - resume error [Closed]

> Username: liuaifu  
> Created at: 2025-04-23 10:52:12 UTC  
> Updated at: 2025-04-28 15:57:26 UTC  
> Closed at: 2025-04-28 15:57:26 UTC  
> Url: https://github.com/boostorg/process/issues/481  

I am using process in boost1.88, `suspend` can suspend the process, but `resume` can not resume the process, I checked the source code, found that the function call error, please see `include/boost/process/v2/process.hpp` line 234, where `suspend` should be `resume`.  
  
    /// Send the process a signal requesting it to resume. This may rely on undocumented functions.  
    void resume(error_code &ec)  
    {  
      process_handle_.resume(ec);    
    }  
    
    /// Send the process a signal requesting it to resume. This may rely on undocumented functions.  
    void resume()  
    {  
        error_code ec;  
        suspend(ec);                                            <----- line 234  
        if (ec)  
            detail::throw_error(ec, "resume");  
    }  
  
In addition, the indentation style is not uniform.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-04-28 15:47:56 UTC  
> Url: https://github.com/boostorg/process/issues/481#issuecomment-2835698761  

Thanks for reporting, that's a major typo.
