# #278 - Issues compiling a Project including asio with the Intel C++ Compiler 19.0 [Closed]

> Username: dasMax96  
> Created at: 2019-09-05 13:30:10 UTC  
> Updated at: 2020-12-30 01:08:58 UTC  
> Closed at: 2020-12-30 01:08:57 UTC  
> Url: https://github.com/boostorg/asio/issues/278  

Hey,   
  
whenever i try to compile a Project including boost asio with the Intel C++ Compiler, i end up with an Compile-Time Access Violation:  
  
`boost/asio/impl/executor.hpp(351): error : access violation  
1>      i->dispatch(function(BOOST_ASIO_MOVE_CAST(Function)(f), a));`  
  
  
also i end up with an Beast error, wich i might report later into the appropriate Repo.   
  
`beast/core/async_base.hpp(259): error : namespace "boost::asio" has no member "associated_allocator_t"`  
  
Example Cpp used to generate the Access Violation:   
  
  
`#include <boost/asio.hpp>  
int main() {  
return 0;   
}`  
  
Kind Regards

---

## Comment 1

> Username: dasMax96  
> Created at: 2020-02-12 13:41:54 UTC  
> Updated at: 2020-02-12 13:59:19 UTC  
> Url: https://github.com/boostorg/asio/issues/278#issuecomment-585210399  

The issue is still reproducible.   
  
Cloned the Main Repository from Master, ended up with a similar issue. Even tho Build now directly supports intel-19.0-vc14.2 by now.   
  
If any other info is needed, just let me know and I'll post it.   
  
(Also: Did a Compiler Update to Update 5 (Previously was 4), which didn't solve the problem.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:08:56 UTC  
> Url: https://github.com/boostorg/asio/issues/278#issuecomment-752292855  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#716](https://github.com/chriskohlhoff/asio/issues/716).
