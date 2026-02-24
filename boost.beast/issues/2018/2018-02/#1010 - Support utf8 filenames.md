# #1010 - Support utf8 filenames [Closed]

> Username: cedral  
> Created at: 2018-02-06 22:11:32 UTC  
> Updated at: 2019-07-07 04:01:54 UTC  
> Closed at: 2019-07-07 04:01:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1010  

I had to add an assign function to value_type so I could open the file externally and thus use CreateFileW to support filenames that depend on unicode (windows doesn't natively support UTF-8) could you incorporate this into your code?  
  
```  
inline  
void  
basic_file_body<file_win32>::  
value_type::  
assign(file_win32::native_handle_type handle, error_code& ec)  
{  
  file_.native_handle(handle);  
  file_.seek(0, ec);  
  if (ec)  
    return;  
  size_ = file_.size(ec);  
  if (ec)  
  {  
    close();  
    return;  
  }  
  first_ = 0;  
  last_ = size_;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-07 00:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-363608982  

Beast is supposed to convert utf8 for you on Windows, I have just not written it yet...

---

## Comment 2

> Username: cedral  
> Created at: 2018-02-09 22:42:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-364590630  

That doesn't make for a very complete solution. Existing windows applications that are well written for internationalization will be storing filenames in wchar_t form. So that would mean 2 conversions anytime anybody wants to use your open function. They convert wide character to UTF-8 and then you convert it back. To avoid all that extra work I think it would be better to either allow assigning externally opened handles (which matches the ASIO interface) or provide a wide character open function on windows (which is how windows handles std::fstream). Alternatively you could do what boost streams does and take a boost::filesystem::path which can be created from either wide or narrow filename and only narrows as needed. I reserved this solution for last because it will require heap allocation which can get expensive in a multithreaded server.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-10 01:46:44 UTC  
> Updated at: 2018-02-10 01:47:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-364616015  

I'm a firm believer in "UTF-8 everywhere" so I would prefer that even if it requires an additional conversion on Widnows. I consider the Windows situation a mess, and the cause of much grief. Another source of grief is the `wchar_t` type which was ill-thought out. I am not alone in these views. That said, I thought Beast already supported assigning already-open handles using the `basic_file_body::value_type::reset` function, see:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_file_body__value_type/reset.html  
  
On Windows, `beast::file` is an alias for `beast::file_win32`, which allows setting the native handle:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__file_win32/native_handle/overload2.html  
  
It seems this functionality is already present. I'll note that no one has ever used it seriously (myself included) so I will be very glad to hear that it works! Of course, if there's a bug I will be less glad, but still happy to fix it. Good luck!

---

## Comment 4

> Username: cedral  
> Created at: 2018-02-12 13:58:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-364929851  

Thank you. That is the second time you have pointed me to a better way to do what I was trying to do.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-02-12 15:22:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-364954840  

Actually, Beast advertises utf-8 filename support but it is currently unimplemented so if it is okay I will keep this issue open since it reflects a defect - thanks for reminding me!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-07-07 04:01:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1010#issuecomment-508969762  

Duplicate of #793
