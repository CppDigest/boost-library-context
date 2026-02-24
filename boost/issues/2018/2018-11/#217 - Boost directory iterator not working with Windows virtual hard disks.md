# #217 - Boost directory iterator not working with Windows virtual hard disks ? [Closed]

> Username: kenkit  
> Created at: 2018-11-04 21:06:25 UTC  
> Updated at: 2018-11-27 18:52:49 UTC  
> Closed at: 2018-11-27 18:52:49 UTC  
> Url: https://github.com/boostorg/boost/issues/217  

I've experienced the above bug on windows x86 using latest boost.  
  
The said code is below  
`  for (boost::filesystem::recursive_directory_iterator end,  
         dir(path(file_name));  
         dir != end; ++dir) `  
I was initially using ` dir(file_name);` when I discovered this.  
The error occured only when scanning root drive. ie `F:\ `

---

## Comment 1

> Username: mclow  
> Created at: 2018-11-04 21:44:10 UTC  
> Url: https://github.com/boostorg/boost/issues/217#issuecomment-435710710  

You should probably report this against Boost.Filesystem; it will get more attention there.  
https://github.com/boostorg/filesystem

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-27 00:13:39 UTC  
> Url: https://github.com/boostorg/boost/issues/217#issuecomment-441849032  

@kenkit you can close this issue here if it was moved to the right repo.   Thanks.

---

## Comment 3

> Username: kenkit  
> Created at: 2018-11-27 18:52:49 UTC  
> Url: https://github.com/boostorg/boost/issues/217#issuecomment-442174030  

ok, thanks
