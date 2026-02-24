# #387 - Header file missing [Closed]

> Username: praveen-velliengiri  
> Created at: 2018-02-26 19:41:33 UTC  
> Updated at: 2018-02-28 16:30:08 UTC  
> Closed at: 2018-02-27 06:59:59 UTC  
> Url: https://github.com/boostorg/hana/issues/387  

Hi   
I'm new to boost.hana and I got some problems when I use boost.hana.  
Recently I installed boost.hana with the following commands   
I have the source files in /home/praveen/boost.hana/hana-master. And I created a directory using mkdir build && cd build.   
To install   
1) cmake /home/praveen/boost.hana/hana-master -DCMAKE_INSTALL_PREFIX=/home/praveen/boost.hana/install_dir.  
2) cmake --build . --target install  
Project is installed successfully.   
  
Trying with simple program   
```c++   
#include </home/praveen/boost.hana/install_dir/include/boost/hana.hpp>  
  
namespace hana = boost::hana;  
int main()  
{  
  int i=0;  
    
 return 0;  
}  
```  
  
  
and tried to compile with clang++ -std=c++14 filename.cpp   
compilers produce error such as   
In file included from hana.cpp:1:  
  
/home/praveen/boost.hana/install_dir/include/boost/hana.hpp:45:10:   
fatal error: 'boost/hana/config.hpp' file not found  
#include <boost/hana/config.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
  
How I can get boost.hana compilation correct ?   
Sorry this is not an issue, I don't know where to ask, I will close once I get the compilation correct   
Thanks  
Pree

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-02-26 19:55:49 UTC  
> Updated at: 2018-02-26 19:56:43 UTC  
> Url: https://github.com/boostorg/hana/issues/387#issuecomment-368629180  

You need to have the contents of the `include` directory in the include path.  
  
It would look something like this:  
```  
clang++ -std=c++14 -I/home/praveen/boost.hana/install_dir/include filename.cpp  
```  
Then in your code you would have to use `#include <boost/hana.hpp>`

---

## Comment 2

> Username: ldionne  
> Created at: 2018-02-27 06:59:59 UTC  
> Url: https://github.com/boostorg/hana/issues/387#issuecomment-368767237  

Thanks Jason.

---

## Comment 3

> Username: praveen-velliengiri  
> Created at: 2018-02-28 16:30:08 UTC  
> Url: https://github.com/boostorg/hana/issues/387#issuecomment-369296694  

@ricejasonf @ldionne   
Thank you very much guys ! It compiles successfully :+1:
