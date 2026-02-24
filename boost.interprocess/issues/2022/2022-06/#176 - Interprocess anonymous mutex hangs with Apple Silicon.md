# #176 - Interprocess anonymous mutex hangs with Apple Silicon [Open]

> Username: r0ck3r008  
> Created at: 2022-06-06 15:53:14 UTC  
> Updated at: 2022-07-16 21:02:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/176  

Hi, boost interprocess anonymous mutex seems not to be handling well in ARM64 of Apple Silicon.  
  
```C++  
#include <iostream>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/sync/interprocess_mutex.hpp>  
  
using namespace boost::interprocess;  
  
int main() {  
    try{  
        managed_shared_memory managed_shm{open_or_create, "shm", 1024};  
        interprocess_mutex *mtx =  
            managed_shm.find_or_construct<interprocess_mutex>("mtx")();  
  
        mtx->lock();  
        std::cout << "Critial section" << std::endl;  
        mtx->unlock();  
    } catch (const boost::interprocess::interprocess_exception &e) {  
        std::cerr << "Interprocess exception thrown: " << e.what() << std::endl;  
        _exit(1);  
    }  
}  
```  
**Compilation steps followed**  
```bash  
clang++ -std=c++17 -arch arm64 boost_interprocess.cpp -I<BOOST_HOME> -o boost_interprocess_arm64 # For arm64 only build  
clang++ -std=c++17 -arch x86_64 boost_interprocess.cpp -I<BOOST_HOME> -o boost_interprocess_x64 # For x64 only build   
```  
**Expected Behavior**  
Both the instantiations should work and print *Critical Section*.  
  
**What was observed**  
When arm64 build is executed first, it works fine, but when x64 build is executed, the subsequent arm64 execution hangs.  
  
**Steps to reproduce**  
* `rm /tmp/boost_interprocess/shm`  
* `./boost_interprocess_x64`  
* `./boost_interprocess_arm64`  
  
**Environment tested on**  
* MacBook Air (M1, 2020)  
* MacOS Monterey 12.3.1  
*  Apple clang version 13.1.6 (clang-1316.0.21.2.5)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 20:43:45 UTC  
> Url: https://github.com/boostorg/interprocess/issues/176#issuecomment-1186287861  

I don't have access to a Mac, so I would need some debugging and help from your side.

---

## Comment 2

> Username: r0ck3r008  
> Created at: 2022-07-16 21:02:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/176#issuecomment-1186291651  

@igaztanaga sure let me know how I can help.
