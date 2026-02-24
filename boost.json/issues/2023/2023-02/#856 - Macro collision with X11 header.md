# #856 - Macro collision with X11 header [Closed]

> Username: wh1t3lord  
> Created at: 2023-02-11 09:02:22 UTC  
> Updated at: 2023-02-12 14:43:50 UTC  
> Closed at: 2023-02-12 14:43:50 UTC  
> Url: https://github.com/boostorg/json/issues/856  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
I put my star to this project two years ago, I guess.  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
1.81.0  
  
### Steps necessary to reproduce the problem  
  
The problem of collision definition and template class parameter as Bool.  
  
We have a define in #include <X11/Xlib.h> the line is 82 as you see on screenshot below.  
  
![image](https://user-images.githubusercontent.com/68504048/218249239-207d1465-ed9d-4c72-b5ef-f9a8673421f9.png)  
  
Steps for reproducing is simple use gcc compiler (mines is gcc (Ubuntu 12.2.0-3ubuntu1) 12.2.0  
) and the next screenshot is the template parameter.   
  
![image](https://user-images.githubusercontent.com/68504048/218249359-a039637c-aa47-4275-af3f-9415a7577c59.png)  
  
I know probably it is a bug of compiler, but technically it is better write any names that will not collide with macros at all. Hope you will just rename that parameter.  
  
I can't change the header including order or defined some undef for Bool, it is just compilcated. The project is not simple it has tons of modules, and the X11 header is required to be with json header in one project. And in some cases user wants to use specific macros from X11 while I put undef for Bool and it is kinda sad for user who wants programm on native X11 :/.   
  
For temporary solution I put #undef Bool for json header (before the #include jsonheader statement), but hope you will take some reasonable considerations of that name design.  
  
Steps to reproduce just create this:  
  
```cpp  
  
#include <X11/Xlib.h>  
#include <boost/json.hpp>  
  
int main(void)  
{  
return 0;  
}  
  
```  
  
  
### All relevant compiler information  
  
Just default cmake generated project, Idk how to know how it builds my project.  
  
Have a nice day.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-11 12:31:11 UTC  
> Url: https://github.com/boostorg/json/issues/856#issuecomment-1426756227  

> I put my star to this project two years ago, I guess.  
  
Damn.. its already been two years?? Time flies...

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-02-12 14:15:22 UTC  
> Url: https://github.com/boostorg/json/issues/856#issuecomment-1427044627  

X is the gift that keeps giving…
