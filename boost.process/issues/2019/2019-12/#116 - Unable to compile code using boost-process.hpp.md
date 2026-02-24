# #116 - Unable to compile code using boost/process.hpp [Closed]

> Username: tim-wilkins  
> Created at: 2019-12-13 16:08:26 UTC  
> Updated at: 2020-05-25 01:52:28 UTC  
> Closed at: 2020-05-25 01:52:28 UTC  
> Url: https://github.com/boostorg/process/issues/116  

The following code fails to build in Visual Studio 2019 (16.4.1) using boost 1.72, debug x64 build.  
  
#include "boost/process.hpp"  
  
void out(const char* d);  
int main()  
{  
    char data[100000];  
    memset(data, 0, sizeof data);  
  
    boost::asio::io_context ios;  
  
    boost::process::child c("dir", boost::process::std_out > boost::asio::buffer(data), boost::process::std_err > boost::process::null, ios);  
  
    ios.run();  
    int result = c.exit_code();  
    out(data);  
}  
#include <iostream>  
void out(const char* d)  
{  
    std::cout << d;  
}  
  
Errors:  
  
C:\boost\boost_1_72_0\boost\asio\impl\read.hpp(460,39): error C2039: 'executor_type': is not a member of 'boost::process::detail::windows::async_pipe'  
C:\boost\boost_1_72_0\boost\asio\impl\read.hpp(460,1): error C3646: 'executor_type': unknown override specifier  
C:\boost\boost_1_72_0\boost\asio\impl\read.hpp(460,1): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
  
Adding  
#include "boost/asio.hpp"  
didn't help.

---

## Comment 1

> Username: jakobandersen  
> Created at: 2019-12-19 11:01:32 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-567442708  

I have a similar problem on Linux (with multiple GCC and Clang versions). The following is output from GCC on the program from klemens-morgenstern/boost-process#124  
```  
main.cpp:22:4:   required from here  
/opt/boost_1_72/include/boost/asio/impl/write.hpp:425:54: error: no type named ‘executor_type’ in ‘class boost::process::detail::posix::async_pipe’  
     typedef typename AsyncWriteStream::executor_type executor_type;  
                                                      ^~~~~~~~~~~~~  
```  
  
Applying the changes in 6a4d2ff72114ef47c7afaf92e1042aca3dfa41b0 seemingly fixes the problem (on Linux). The problem seems to be new in 1.72.

---

## Comment 2

> Username: tomazos  
> Created at: 2019-12-21 09:26:41 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-568166656  

I hit this problem too.  Reverting to 1.71 fixed.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-12-28 16:05:49 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-569428673  

boost.asio changed to require that, I will fix it. But it sadly will only make 1.73.

---

## Comment 4

> Username: tomazos  
> Created at: 2019-12-28 16:52:50 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-569432295  

How is there not an asio/process integration test that failed on the 1.72 release candidate?  The boost folks do execute the test suite as part of the release process I assume?  In addition to fixing it, would it make sense to also add a test that would have failed if not for the fix?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2019-12-28 20:11:52 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-569447646  

There are and they failed, but I failed to notice and fix it early enough. Boost has a pretty tight release schedule, so it's gonna be fixed in the next one.

---

## Comment 6

> Username: jwakely  
> Created at: 2020-04-29 09:59:27 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-621103616  

This seems to be fixed in 1.73.0, released yesterday. Can it be closed?

---

## Comment 7

> Username: tomazos  
> Created at: 2020-04-29 14:39:36 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-621254529  

I can't personally verify fixed until the release propogates to the boost conan recipe: https://github.com/conan-io/conan-center-index/blob/master/recipes/boost/all/conandata.yml  Will be able to do so in a few days.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2020-05-20 17:58:13 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-631632106  

@tomazos any news?

---

## Comment 9

> Username: tomazos  
> Created at: 2020-05-23 13:44:43 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-633056629  

conan recipe for 1.73 is up, I'll try it out within 24 hrs

---

## Comment 10

> Username: tomazos  
> Created at: 2020-05-23 14:45:20 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-633068197  

ok tried it.  it works.  vote confirmed fix and close

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2020-05-25 01:52:28 UTC  
> Url: https://github.com/boostorg/process/issues/116#issuecomment-633338854  

Awesome, thanks!
