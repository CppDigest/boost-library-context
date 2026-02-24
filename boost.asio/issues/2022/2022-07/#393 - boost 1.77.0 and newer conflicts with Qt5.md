# #393 - boost 1.77.0 and newer conflicts with Qt5 [Open]

> Username: hauihau  
> Created at: 2022-07-04 11:36:23 UTC  
> Updated at: 2022-07-05 07:28:56 UTC  
> Url: https://github.com/boostorg/asio/issues/393  

We have a self-written program which compiled without any issues until 1.75.0. I had some spare time and tried to find out, why the compiler reports syntax errors since 1.76.0.  
  
In file included from/software/libs/boost/1.79.0/include/boost/asio/associated_cancellation_slot.hpp(20),  
                 from /software/libs/boost/1.79.0/include/boost/asio.hpp(22),  
                 from src/mainwindow.h(47),  
                 from src/main.cpp(7):  
/software/libs/boost/1.79.0/include/boost/asio/cancellation_signal.hpp(116): error: expected a ")"  
    void emit(cancellation_type_t type)  
  
  
There are other source files, which also include asio.hpp which do not show this error. I added --save-temps (GCC)/-P (ICC) to the compiler flags and compared the resulting .ii/.i files.  
  
The working source files contain:  
```  
  void emit(cancellation_type_t type)  
  {  
    if (handler_)  
      handler_->call(type);  
  }  
  
```  
  
The non working source files:  
```  
  void (cancellation_type_t type)  
  {  
    if (handler_)  
      handler_->call(type);  
  }  
```  
  
The issue is caused by Qt5 which has a line `# define emit` in https://github.com/qt/qtbase/blob/5.15.2/src/corelib/kernel/qobjectdefs.h#L97 (`/usr/include/qt5/QtCore/qobjectdefs.h`)  
  
Would it be possible to rename the function so that we can use a current Boost together with Qt5?  
  
  
  
Note: https://github.com/boostorg/asio/commit/3521da0834fd20a520dc33e5b256c59b20827842 introduced that function.

---

## Comment 1

> Username: jcmonnin  
> Created at: 2022-07-05 06:35:11 UTC  
> Url: https://github.com/boostorg/asio/issues/393#issuecomment-1174673218  

The headers should work around that with `pop_macro` /`push_macro`, however this doesn't work properly in boost version 1.77.0 on windows: https://github.com/chriskohlhoff/asio/issues/895  
Updating boost to a more recent version or appying the referenced patch is likely to fix the issue.

---

## Comment 2

> Username: hauihau  
> Created at: 2022-07-05 07:12:51 UTC  
> Url: https://github.com/boostorg/asio/issues/393#issuecomment-1174701437  

I forgot to mention, that we use the classic Intel Compiler. Looks like pop_options.hpp lacks the pop_macro calls in the section for the intel compilers.

---

## Comment 3

> Username: jcmonnin  
> Created at: 2022-07-05 07:28:56 UTC  
> Url: https://github.com/boostorg/asio/issues/393#issuecomment-1174714608  

I missed the ICC in the original message and the fact you are already using 1.79.0.  
Intel seems to support `pop_macro`/`push_macro`: https://www.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/compiler-reference/pragmas/intel-supported-pragma-reference.html  
I would suggest you modify `asio/detail/pop_options.hpp` and for the Intel compiler, and if it works send a pull request.
