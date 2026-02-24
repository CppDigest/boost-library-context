# #8 - winconsole_ostream should flush in the destructor [Closed]

> Username: kivadiu  
> Created at: 2016-09-23 16:06:36 UTC  
> Updated at: 2017-06-10 14:29:10 UTC  
> Closed at: 2017-06-10 14:28:06 UTC  
> Url: https://github.com/boostorg/nowide/issues/8  

This simple program do not print anything:  
# include "nowide/iostream.hpp"  
  
int main(int, char**) {  
  nowide::cout<<"hello world\n";  
  return 0;  
}  
  
This is solved by adding flush() to the destructor of winconsole_ostream  
winconsole_ostream::~winconsole_ostream() {  
  flush();  
}

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-06-10 14:29:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/8#issuecomment-307568469  

Fixed in 9b3c367868a1efa7caa953f27af5eaccd7db8b68
