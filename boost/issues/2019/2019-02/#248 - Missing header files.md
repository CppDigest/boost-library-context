# #248 - Missing header files [Closed]

> Username: Yvain-Desplat  
> Created at: 2019-02-15 18:57:15 UTC  
> Updated at: 2020-06-10 13:25:20 UTC  
> Closed at: 2020-06-10 13:25:20 UTC  
> Url: https://github.com/boostorg/boost/issues/248  

Hi everybody,   
  
I'm downloading the latest versions of boost (1.69.0) but it seems that some of the files are missing in the installation. I'm trying to run a transcriptome assembler that needs to have files in   
#include <bits/typesizes.h>  
and  
#include <bits/types.h>   
The problem is that when I download the libraries no bits directory is created and thus no typesizes.h or types.h headers files are found, causing my software building crash.   
  
Anyone had this issue before ? Anyone knows how to fix this ?   
  
I am running on OSX 10.14.2 and on the terminal.   
  
thanks in advance for your help.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-02-15 19:11:03 UTC  
> Url: https://github.com/boostorg/boost/issues/248#issuecomment-464164816  

AMDG  
  
On 2/15/19 11:57 AM, Yvain-Desplat wrote:  
> I'm downloading the latest versions of boost (1.69.0) but it seems that some of the files are missing in the installation. I'm trying to run a transcriptome assembler that needs to have files in   
> #include <bits/typesizes.h>  
> and  
> #include <bits/types.h>   
> The problem is that when I download the libraries no bits directory is created and thus no typesizes.h or types.h headers files are found, causing my software building crash.   
>   
  
Those headers are not part of Boost.  They're system headers.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: yashagarwal1999  
> Created at: 2019-03-17 17:24:48 UTC  
> Url: https://github.com/boostorg/boost/issues/248#issuecomment-473686636  

> Hi everybody,  
>   
> I'm downloading the latest versions of boost (1.69.0) but it seems that some of the files are missing in the installation. I'm trying to run a transcriptome assembler that needs to have files in  
> #include <bits/typesizes.h>  
> and  
> #include <bits/types.h>  
> The problem is that when I download the libraries no bits directory is created and thus no typesizes.h or types.h headers files are found, causing my software building crash.  
>   
> Anyone had this issue before ? Anyone knows how to fix this ?  
>   
> I am running on OSX 10.14.2 and on the terminal.  
>   
> thanks in advance for your help.  
  
Hi you can check the path for the source files included in your code while compiling using the syntax  
g++ -H filename.cpp  
It will give you which files are included and their respective address.  
Hope this helps.

---

## Comment 3

> Username: mclow  
> Created at: 2019-03-17 19:28:31 UTC  
> Url: https://github.com/boostorg/boost/issues/248#issuecomment-473706556  

> AMDG  
> On 2/15/19 11:57 AM, Yvain-Desplat wrote: I'm downloading the latest versions of boost (1.69.0) but it seems that some of the files are missing in the installation. I'm trying to run a transcriptome assembler that needs to have files in #include <bits/typesizes.h> and #include <bits/types.h> The problem is that when I download the libraries no bits directory is created and thus no typesizes.h or types.h headers files are found, causing my software building crash.  
> Those headers are not part of Boost. They're system headers. In Christ, Steven Watanabe  
  
Even better, they're part of libstdc++, the GNU standard library, and are not available on Mac OS X (since about 10.8)
