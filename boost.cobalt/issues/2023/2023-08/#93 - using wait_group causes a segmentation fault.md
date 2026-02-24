# #93 - using wait_group causes a segmentation fault. [Closed]

> Username: matthijs  
> Created at: 2023-08-29 20:02:06 UTC  
> Updated at: 2023-09-04 07:32:28 UTC  
> Closed at: 2023-09-04 07:32:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93  

Hi,  
  
Compiler: clang-16  
Std: libc++  
Boost: 1.83.0  
Boost.async: current develop  
  
Compile line is:  
```bash  
/usr/bin/clang++-16  -isystem /home/matthijs/boost/boost_1_83_0 -stdlib=libc++ -march=native -g -std=c++20 -MD -MT CMakeFiles/main.dir/main.cpp.o -MF CMakeFiles/main.dir/main.cpp.o.d -o CMakeFiles/main.dir/main.cpp.o -c /home/matthijs/t10/main.cpp  
/usr/bin/clang++-16  -stdlib=libc++ -march=native -g -fuse-ld=lld-16 -ferror-limit=0 CMakeFiles/main.dir/main.cpp.o -o main  -lpthread /usr/lib/x86_64-linux-gnu/libssl.so /usr/lib/x86_64-linux-gnu/libcrypto.so /usr/local/lib/libboost_async.a -lpthread  
```  
  
The following test program causes a segmentation fault when cancelling the program (Ctrl-C):  
```c++  
#include <boost/async.hpp>  
#include <boost/asio/system_timer.hpp>  
  
namespace async = boost::async;  
  
async::promise<void> timeout_and_write(async::channel<std::string> &channel) {  
  while (!co_await async::this_coro::cancelled) {  
    boost::asio::system_timer timer{co_await async::this_coro::executor};  
    timer.expires_after(std::chrono::seconds{20});  
    co_await timer.async_wait(async::use_op);  
  
    co_await channel.write("Test!");  
  }  
  
  co_return;  
}  
  
async::promise<void> read(async::channel<std::string> &channel) {  
  while (!co_await async::this_coro::cancelled) {  
    co_await channel.read();  
  }  
}  
  
async::promise<void> test() {  
  async::channel<std::string> channel;  
  co_await async::join(timeout_and_write(channel), read(channel));  
}  
  
async::main co_main(int argc, char **argv) {  
  try {  
    async::wait_group wg;  
    wg.push_back(test());  
    co_await wg;  
  } catch(const std::exception& e) {}  
  
  co_return 0;  
}  
```  
When running this program and then cancelling it (Ctrl-C) then it causes an segmentation fault. When adding a try catch around the function definition `timeout_and_write` and `read` it cancels correctly.  
  
It is probably a good idea to catch exceptions anyway in those functions.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-29 21:04:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1698142789  

Probably an uncaught exception going rogue. You're right, the wait_group should handle that.

---

## Comment 2

> Username: matthijs  
> Created at: 2023-08-30 06:47:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1698591825  

Without the wait_group it gives a segfault too:  
```c++  
async::main co_main(int argc, char **argv) {  
  try {  
    co_await test();  
  } catch(const std::exception& e) {}  
  
  co_return 0;  
}  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-31 05:12:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1700377800  

Yeaup, that's an error in `join`. Thanks for reporting, this is important.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-31 07:07:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1700480906  

Found it, those were unchecked posts in the channel. Fix should be ready today.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-09-01 05:28:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1702185683  

Fix is in develop, but your code still causes issues on gcc-12 (not gcc-13) for some reason I can't figure out.

---

## Comment 6

> Username: matthijs  
> Created at: 2023-09-01 15:36:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1702944297  

Thanks for the fix, are you still getting segfaults with gcc-12 (I am still on debian bullseye which has gcc-10 by default).  
  
I can confirm that my app doesn't segfault anymore.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-09-01 16:49:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/93#issuecomment-1703036960  

It's a compiler bug on gcc12.1 & 12.2. Fixed in 12.3.   
Same thing happens with asio awaitables.  
  
I'll get the rest (non critical fixes) merged later today, hopefully.
