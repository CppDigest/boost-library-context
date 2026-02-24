# #103 - Stack Overflow [Closed]

> Username: Jackmrzhou  
> Created at: 2018-04-03 11:23:24 UTC  
> Updated at: 2018-04-30 20:05:59 UTC  
> Closed at: 2018-04-30 20:05:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/103  

Recently I used this library for a project, and I works on mac and ubuntu very well. However, when I transplant it to Windows, it failed when storing data into disk. So I use Visual Stdio 2017 to track the bug. And Visual Stdio told me that the failure was due to stack overflow.  
So I use mingw/g++ and change the stack size to 16mb, and it worked.  
Here is the screen shot.  
![default](https://user-images.githubusercontent.com/22827869/38246392-3978bda0-3774-11e8-8fb1-e2aee8324f1e.png)  
basic_text_oprimitive.hpp, function:**save_impl**.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-04-30 20:05:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/103#issuecomment-385512844  

interesting information.  I don't really know how to incorporate this into the library though.  Maybe it should be added to the documentation.
