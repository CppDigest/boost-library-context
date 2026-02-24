# #117 - boost/filesystem.hpp [Closed]

> Username: klasing  
> Created at: 2019-09-04 13:17:18 UTC  
> Updated at: 2019-09-04 13:29:54 UTC  
> Closed at: 2019-09-04 13:27:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/117  

the following statement will not work in a Win32 environment  
`filesize = boost::filesystem::file_size(p, ec);`  
value of p is: `{m_pathname=L"logging"}`  
the function returns as value for ec:  
`{val_=2 failed_=true cat_=0x006d7688 {ManageServer.exe!boost::system::detail::system_error_category boost::system::detail::cat_holder<void>::sysyem_category_instance}{...}}`

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-04 13:27:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/117#issuecomment-527900157  

That the function returns an error code does not indicate a problem with the library. On Windows system error category, the error code of 2 means `ERROR_FILE_NOT_FOUND`, which suggests that the file is missing at the current working directory.

---

## Comment 2

> Username: klasing  
> Created at: 2019-09-04 13:29:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/117#issuecomment-527901294  

Right, thank you Andrey.  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: Andrey Semashev  
Verzonden: woensdag 4 september 2019 15:27  
Aan: boostorg/filesystem  
CC: klasing; Author  
Onderwerp: Re: [boostorg/filesystem] boost/filesystem.hpp (#117)  
  
That the function returns an error code does not indicate a problem with the library. On Windows system error category, the error code of 2 means ERROR_FILE_NOT_FOUND, which suggests that the file is missing at the current working directory.  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.
