# #3035 - win32_unicode_path fails to clear ec. [Open]

> Username: evoskuil  
> Created at: 2025-10-02 08:05:42 UTC  
> Updated at: 2025-10-05 22:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/3035  

If there is no failure ec remains unchanged (not cleared).  
  
```cpp  
    win32_unicode_path(const char* utf8_path, error_code& ec) {  
        int ret = mb2wide(utf8_path, static_buf_.data(),  
            static_buf_.size());  
        if (ret == 0)  
        {  
            int sz = mb2wide(utf8_path, nullptr, 0);  
            if (sz == 0)  
            {  
                ec.assign(boost::winapi::GetLastError(),  
                    system_category());  
                return;  
            }  
            dynamic_buf_.resize(sz);  
            int ret2 = mb2wide(utf8_path,  
                dynamic_buf_.data(),  
                dynamic_buf_.size());  
            if (ret2 == 0)  
            {  
                ec.assign(boost::winapi::GetLastError(),  
                    system_category());  
                return;  
            }  
        }  
    }  
```  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/win32_unicode_path.hpp#L31-L54

---

## Comment 1

> Username: ashtum  
> Created at: 2025-10-03 18:50:12 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3366847243  

I believe functions that take `error_code& ec` are not supposed to clear it unless they internally need to rely on it (for example, by calling another function and checking `ec`).    
Therefore, it is the caller's responsibility to start with a clear `ec`.    
@vinniefalco, any thoughts?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-10-03 22:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3367362407  

That's the theory but in this case it can't hurt to clear it. I don't know if this optimizing of not clearing `ec` every time is worth it, or even needed.

---

## Comment 3

> Username: evoskuil  
> Created at: 2025-10-04 13:14:59 UTC  
> Updated at: 2025-10-04 13:15:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3368219342  

This propagates up to higher level calls like file.open(path, ec) where the behavior was unexpected (to me).  
  
I’ve used the non-clearing approach internally, but only when coupled with the ec causing each call to no-op when set, which allows calls to be safely chained without repeating the ec test until complete. But otherwise I can’t see how not clearing it upon success is helpful.  
  
In this case I had set a default ec value on construct prior to try/catch around the file open, so that I didn’t have to set it explicitly in the catch. And of course I got the failure even though it was succeeding. No big deal, but just unexpected.

---

## Comment 4

> Username: ashtum  
> Created at: 2025-10-04 13:39:40 UTC  
> Updated at: 2025-10-04 13:43:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3368242949  

> This propagates up to higher level calls like file.open(path, ec) where the behavior was unexpected (to me).  
  
Which file API are you using? The `file_win32::open` function clears the `error_code` on success:  
https://github.com/boostorg/beast/blob/9b24b28d502946583300d3e58767cb30a8ec8486/include/boost/beast/core/impl/file_win32.ipp#L222  
  
Edit:  
I believe you mean this line:  
https://github.com/boostorg/beast/blob/9b24b28d502946583300d3e58767cb30a8ec8486/include/boost/beast/core/impl/file_win32.ipp#L190-L192

---

## Comment 5

> Username: evoskuil  
> Created at: 2025-10-04 15:16:03 UTC  
> Updated at: 2025-10-04 15:25:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3368334265  

```cpp  
boost::beast::http::file_body::value_type file{};  
boost::beast::error_code code{ error::not_found };  
  
try  
{  
    // BUGBUG: file.open does not reset code for success.  
    file.open(path.c_str(), boost::beast::file_mode::read, code);  
}  
catch (...)  
{  
}  
```  
  
I found this inconsistent with the above APIs (and std::filesystem). For example:   
  
https://en.cppreference.com/w/cpp/filesystem/create_directory.html  
  
2,6) Sets a [std::error_code](https://en.cppreference.com/w/cpp/error/error_code.html)& parameter to the OS API error code if an OS API call fails, and executes ec.[clear](https://en.cppreference.com/w/cpp/error/error_code/clear.html)() if no errors occur.  
...  
4) Sets a [std::error_code](https://en.cppreference.com/w/cpp/error/error_code.html)& parameter to the OS API error code if an OS API call fails, and executes ec.[clear](https://en.cppreference.com/w/cpp/error/error_code/clear.html)() if no errors occur.  
```cpp  
bool create_directories(const std::filesystem::path& p, std::error_code& ec);  
```  
  
So maybe the issue isn't with this lower level call but instead boost::beast (and maybe others). Though it seems that beast at least made the same assumption that I did.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2025-10-05 22:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/3035#issuecomment-3369393004  

to avoid these kinds of annoying issues, I think it is worth clearing `ec` immediately upon entry into the function, to eliminate any doubts.
