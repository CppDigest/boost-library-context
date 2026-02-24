# #169 - boost/nowide/iostream undefined reference [Closed]

> Username: raiseFlaymeException  
> Created at: 2023-02-24 12:48:24 UTC  
> Updated at: 2023-06-22 11:09:41 UTC  
> Closed at: 2023-06-22 11:09:41 UTC  
> Url: https://github.com/boostorg/nowide/issues/169  

I compile boost version 1.66 on my computer (windows), I download this code and put it inside the include folder in MinGW, I created a simple program:   
```  
#include <boost/thread.hpp>  
#include <string>  
#include <boost/nowide/iostream.hpp>  
  
void fct(int s, std::string str)  
{  
    boost::this_thread::sleep_for(boost::chrono::seconds(s));  
    boost::nowide::cout << str;  
}  
  
int main()  
{  
    boost::thread t(&fct, 1, " World");  
    boost::nowide::cout << "Hello";  
    t.join();  
    return 0;  
}  
```  
the command to compile:  
`g++ test_thread.cpp src/iostream.cpp -o test_thread.exe -L. -lboost_thread-mgw63-mt-x32-1_66 -lboost_system-mgw63-mt-x32-1_66 -lboost_chrono-mgw63-mt-x32-1_66`  
  
and the compiler return this:  
  
`src/iostream.o:iostream.cpp:(.text$_ZN5boost6nowide6detail26console_output_buffer_baseC2Ev[__ZN5boost6nowide6detail26console_output_buffer_baseC2Ev]+0x14): undefined reference to `vtable for boost::nowide::detail::console_output_buffer_base'  
src/iostream.o:iostream.cpp:(.text$_ZN5boost6nowide6detail26console_output_buffer_baseD2Ev[__ZN5boost6nowide6detail26console_output_buffer_baseD2Ev]+0xa): undefined reference to `vtable for boost::nowide::detail::console_output_buffer_base'  
src/iostream.o:iostream.cpp:(.text$_ZN5boost6nowide6detail25console_input_buffer_baseC2Ev[__ZN5boost6nowide6detail25console_input_buffer_baseC2Ev]+0x14): undefined reference to `vtable for boost::nowide::detail::console_input_buffer_base'  
src/iostream.o:iostream.cpp:(.text$_ZN5boost6nowide6detail25console_input_buffer_baseD2Ev[__ZN5boost6nowide6detail25console_input_buffer_baseD2Ev]+0xa): undefined reference to `vtable for boost::nowide::detail::console_input_buffer_base'  
src/iostream.o:iostream.cpp:(.rdata$_ZTVN5boost6nowide6detail20console_input_bufferE[__ZTVN5boost6nowide6detail20console_input_bufferE]+0x20): undefined reference to `boost::nowide::detail::console_input_buffer_base::sync()'  
src/iostream.o:iostream.cpp:(.rdata$_ZTVN5boost6nowide6detail20console_input_bufferE[__ZTVN5boost6nowide6detail20console_input_bufferE]+0x2c): undefined reference to `boost::nowide::detail::console_input_buffer_base::underflow()'  
src/iostream.o:iostream.cpp:(.rdata$_ZTVN5boost6nowide6detail20console_input_bufferE[__ZTVN5boost6nowide6detail20console_input_bufferE]+0x34): undefined reference to `boost::nowide::detail::console_input_buffer_base::pbackfail(int)'  
src/iostream.o:iostream.cpp:(.rdata$_ZTVN5boost6nowide6detail21console_output_bufferE[__ZTVN5boost6nowide6detail21console_output_bufferE]+0x20): undefined reference to `boost::nowide::detail::console_output_buffer_base::sync()'  
src/iostream.o:iostream.cpp:(.rdata$_ZTVN5boost6nowide6detail21console_output_bufferE[__ZTVN5boost6nowide6detail21console_output_bufferE]+0x3c): undefined reference to `boost::nowide::detail::console_output_buffer_base::overflow(int)'  
collect2.exe: error: ld returned 1 exit status`

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-02-24 15:16:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/169#issuecomment-1443831466  

Undefined references are usually caused by missing libraries in the link step. I don't see nowide in your link command:   
> `g++ test_thread.cpp src/iostream.cpp -o test_thread.exe -L. -lboost_thread-mgw63-mt-x32-1_66 -lboost_system-mgw63-mt-x32-1_66 -lboost_chrono-mgw63-mt-x32-1_66`  
  
So you likely just need to add `-lboost_nowide-mgw63-mt-x32-1_66` to that.

---

## Comment 2

> Username: raiseFlaymeException  
> Created at: 2023-02-24 15:37:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/169#issuecomment-1443862766  

where is the librairies? I use this tutorial ( https://gist.github.com/zrsmithson/0b72e0cb58d0cb946fc48b5c88511da8 ) to download boost and in the lib folder there are no "libboost_nowide" file.

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-02-24 16:55:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/169#issuecomment-1444014392  

I've just seen you wrote you are using Boost 1.66. Boost.Nowide was added in Boost 1.73.  
  
So you must have gotten the `<boost/nowide/iostream.hpp>` include to work by doing something "unofficial" e.g. just copying the headers which doesn't work as can be seen by this error. You also need to build the library.  
  
I'd suggest to use a newer Boost version (latest or at least 1.73) rather than trying to make this work.

---

## Comment 4

> Username: raiseFlaymeException  
> Created at: 2023-02-24 18:34:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/169#issuecomment-1444223590  

ok
