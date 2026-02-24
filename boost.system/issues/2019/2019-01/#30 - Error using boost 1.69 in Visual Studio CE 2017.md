# #30 - Error using boost 1.69 in Visual Studio CE 2017? [Closed]

> Username: Phatcat  
> Created at: 2019-01-07 19:30:23 UTC  
> Updated at: 2019-01-07 20:33:51 UTC  
> Closed at: 2019-01-07 20:33:51 UTC  
> Url: https://github.com/boostorg/system/issues/30  

I was trying to build a project which kept throwing up an error;  
  
![image](https://user-images.githubusercontent.com/1907820/50788805-b916b180-12ba-11e9-97cf-8f94e8fac987.png)  
  
I found a solution. It's a bit of a messy one but it worked.  
  
If you look in the posted picture, you'll notice all those errors are coming from the same file;  
  
boost_1_69_0\boost\system\detail\system_category_win32.hpp  
  
On line 52 it reads;  
  
std::snprintf( buffer, len, "Unknown error (%d)", ev );  
  
And replacing the std::snprintf with _snprintf it then builds the project without error...  
  
This to me is a bit strange, and I thought I would ask about it here since the solution was not one I had to write into their project, but into boost. What do you guys think?  
  
I am on Windows 10 x64 building said project in x64 as well.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-07 20:10:47 UTC  
> Url: https://github.com/boostorg/system/issues/30#issuecomment-452065566  

The errors you are getting lead me to think that at some point before including the Boost.System headers you have  
```  
#define snprintf _snprintf  
```  
This was a necessary workaround in the past, because Visual Studio did not provide the standard `snprintf`, but should no longer be needed for VS 2017 (and 2015).

---

## Comment 2

> Username: Phatcat  
> Created at: 2019-01-07 20:33:51 UTC  
> Url: https://github.com/boostorg/system/issues/30#issuecomment-452072266  

This indeed is the case, and removing that definition did solve the issue. Thank you.
