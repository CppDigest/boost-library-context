# #104 - boost 1.68 fs::copy coredumped under OSX  while fs::copy_file is okay. [Closed]

> Username: ppLorins  
> Created at: 2019-02-18 05:26:13 UTC  
> Updated at: 2019-02-18 09:30:40 UTC  
> Closed at: 2019-02-18 09:30:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/104  

#### system info : osx high sierra (10.13.6).  
  
#### compiler : `g++ ( Apple LLVM version 10.0.0 (clang-1000.10.44.4)).`  
  
#### compile cmd : `g++ -std=c++17 -g -lboost_filesystem -lboost_system -o main main.cc`  
  
#### boost version: 1_68 .   
  
#### reproduce code :   
```c++  
  
#include "boost/filesystem.hpp"  
  
namespace fs = boost::filesystem;  
  
int main(int argc, char** argv){  
  
    fs::path _src("demo.cc");  
    fs::path _dst("demo_dst.cc");  
  
    //normal  
    fs::copy_file(_src, _dst);   
  
    //core  
    fs::copy(_src, _dst);  
    return 0;  
}  
```  
  
#### coredump info :   
```coredump  
(lldb) target create "main"                                                                                               
Current executable set to 'main' (x86_64).                                                                                
(lldb) r                                                                                                                  
Process 44221 launched: '/Users/arthur/test/main' (x86_64)                                                                
libboost_filesystem.dylib was compiled with optimization - stepping may behave oddly; variables may not be available.     
Process 44221 stopped                                                                                                     
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x0)                          
    frame #0: 0x00000001000a7435 libboost_filesystem.dylib`boost::filesystem::detail::copy(boost::filesystem::path const  
&, boost::filesystem::path const&, boost::system::error_code*) [inlined] boost::system::error_code::operator bool(this=0  
x0000000000000000) const at error_code.hpp:680 [opt]                                                                      
   677                                                                                                                    
   678        BOOST_SYSTEM_CONSTEXPR explicit operator bool() const BOOST_SYSTEM_NOEXCEPT  // true if error               
   679        {                                                                                                           
-> 680          return m_val != 0;                                                                                        
   681        }                                                                                                           
   682                                                                                                                    
   683  #else                                                                                                             
Target 0: (main) stopped.                                                                                                 
```  
  
I wonder if it was because fs::copy is deprecated in the recent boost release versions.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-02-18 09:30:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/104#issuecomment-464655163  

This should already be fixed in Boost 1.69.
