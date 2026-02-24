# #99 - getting crash on writing to readonly memory region while generating ec for exception [Open]

> Username: iradization  
> Created at: 2022-11-22 08:23:22 UTC  
> Updated at: 2022-11-26 01:27:33 UTC  
> Url: https://github.com/boostorg/system/issues/99  

I'm trying to figure out a crash dump that I got when initiating a boost implementation of unix domain socket.  
  
What's strange about it is that the relevant frame is when it parsed the ec.what() which is a function that basically create a string message ...  
  
```  
Thread 0 Crashed::  Dispatch queue: com.apple.main-thread  
0   P 0x104919660 boost::system::error_code::what() const (in P_arm64.dsym) + 528 + 251488  
1   P 0x104919654 boost::system::error_code::what() const (in P_arm64.dsym) + 516 + 251476  
2   P 0x104918ea8 boost::system::system_error::system_error(boost::system::error_code const&, char const*) (in P_arm64.dsym) + 308 + 249512  
3   P 0x104adbd10 boost::asio::basic_socket<boost::asio::local::stream_protocol, boost::asio::any_io_exec  
utor>::connect(boost::asio::local::basic_endpoint<boost::asio::local::stream_protocol> const&) (in P_arm64.dsym) + 1808 + 2096400  
(--> this is my c'tor that initiate the unix domain socket)   
4   P 0x104ad9ed4 service::api::Instance(std::__1::function<void (std::__1::   
```  
  
  
Besides the callstack, I could generate a crash file with a crash reason  
  
```  
Termination Reason:    Namespace SIGNAL, Code 10 Bus error: 10  
Terminating Process:   exc handler [2253]  
  
VM Region Info: 0x107008fa8 is in 0x107006000-0x10705e000;  bytes after start: 12200  bytes before end: 348247  
      REGION TYPE                    START - END         [ VSIZE] PRT/MAX SHRMOD  REGION DETAIL  
      __TEXT                      10664e000-107006000    [ 9952K] r-x/r-x SM=COW  ...s/MacOS/P  
--->  __DATA_CONST                107006000-10705e000    [  352K] r--/rw- SM=COW  ...s/MacOS/P  
      __DATA                      10705e000-107072000    [   80K] rw-/rw- SM=COW  ...s/MacOS/P  
```  
  
So It seems like when the ec message is created, we attempt to write a read only memory (DATA_CONST) . I think that this is the problematic code :  
```  
  
source_location const & location() const BOOST_NOEXCEPT  
{  
   BOOST_STATIC_CONSTEXPR source_location loc;  
   return lc_flags_ >= 4? *reinterpret_cast<source_location const*>( lc_flags_ &~ static_cast<boost::uintptr_t>( 1 ) ): loc;  
}  
  
```  
which is called from here  
  
```  
std::string what() const  
{  
    std::string r = message();  
    r += " [";  
    r += to_string();  
    if( has_location() )  
    {  
        r += " at ";  
-->     r += location().to_string();  
    }  
  
    r += "]";  
    return r;  
}  
  
```  
However, i don't understand where do we attempt to write to the const expression 'loc'... any idea what may be the reason ?

---

## Comment 1

> Username: iradization  
> Created at: 2022-11-22 12:31:06 UTC  
> Updated at: 2022-11-22 12:46:48 UTC  
> Url: https://github.com/boostorg/system/issues/99#issuecomment-1327945876  

It appears that when the c'tor of loc is first created, the empty string ("") value should be written to this members of loc which are stored in __DATA_CONST section. Maybe the compiler should allow writing the __DATA_CONST section upon construction of constexpr like in this case, and prevent further writes from this point onward (I guess not).   
Or maybe this loc object should be stored in the heap, since it's created during runtime by the c'tor (so it shouldn't be constexpr only static)  
  
I'm using macOS M1/M2 architecture with Xcode/LLVM compiler, Perhaps do you know if there's a compilation flag that enables it ?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-11-26 01:11:52 UTC  
> Url: https://github.com/boostorg/system/issues/99#issuecomment-1327948940  

`constexpr` variables don't execute any code on construction, they are initialized at compile time.  
  
Without seeing the disassembly around the crash address, it's hard to tell what exactly is going on.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-26 01:16:05 UTC  
> Url: https://github.com/boostorg/system/issues/99#issuecomment-1327950594  

The `system_error` is constructed either here https://github.com/boostorg/asio/blob/ce07af1fdf532c22e6bfe161ded186cb356aa9c2/include/boost/asio/basic_socket.hpp#L861  
  
or here https://github.com/boostorg/asio/blob/ce07af1fdf532c22e6bfe161ded186cb356aa9c2/include/boost/asio/basic_socket.hpp#L864  
  
and it's possible that `ec.lc_flags_` at this point somehow stores an invalid pointer to `source_location`, but why it would do so, isn't clear.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-26 01:27:33 UTC  
> Url: https://github.com/boostorg/system/issues/99#issuecomment-1327952801  

The location of the error code is probably set here https://github.com/boostorg/asio/blob/723eee7a402f5bd1c39a4821fc4951abfe0d0bbd/include/boost/asio/detail/reactive_socket_service.hpp#L587  
  
although I'm not sure whether this is the right socket implementation for macOS.
