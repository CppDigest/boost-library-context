# #78 - Memory leak when comparing error_code to std::errc [Closed]

> Username: SignalWhisperer  
> Created at: 2022-03-03 14:48:41 UTC  
> Updated at: 2022-04-22 23:13:24 UTC  
> Closed at: 2022-04-22 23:13:24 UTC  
> Url: https://github.com/boostorg/system/issues/78  

Whenever I compare a boost::system::error_code to a std::errc value, through operator==, valgrind reports a memory leak in the end. It's not much, 16 bytes in 1 block on x64 (1 atomic pointer), and it doesn't grow with repeated calls.  
  
Here is a trivial example to reproduce the leak:  
```c++  
#include <boost/system/error_code.hpp>  
#include <boost/asio.hpp>  
#include <system_error>  
#include <iostream>  
  
int main(int, char**)  
{  
    boost::system::error_code ec{boost::asio::error::eof};  
  
    if (ec == std::errc::broken_pipe) {  
        std::cout << "Do something\n";  
    }  
    else if (ec == std::errc::connection_aborted) {  
        std::cout << "Do something else\n";  
    }  
  
    return 0;  
}  
```  
  
Run it with `valgrind --leak-check=full --show-leak-kinds=all ./a.out`  
  
The memory seems to originate from:  
https://github.com/boostorg/system/blob/develop/include/boost/system/detail/error_category_impl.hpp#L146  
  
A call to `operator new` is not matched with a call to `operator delete`. This leaks only once -- when destroying the `error_category` on program shutdown, and not on subsequent calls to the comparison operator, due to the atomic exchange.  
  
As I explore more and more to try and find a solution, it seems the destructor of `boost::system::error_category` cannot really be user-defined, thus `ps_` cannot have its memory deleted, hence the leak. I think it's a necessary evil and the fix would require so many things to change, I'm not sure it's worth fixing. I think this warrants at least a mention of this limitation in the documentation.

---

## Comment 1

> Username: SignalWhisperer  
> Created at: 2022-03-03 15:02:30 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058130831  

Just a note, valgrind reports the memory is still accessible, rather than lost.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-03-03 15:33:36 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058162440  

It's not possible to run a destructor for `error_category` for two reasons. First, it's constexpr. Second, `error_code` has to be usable during program shutdown, and destroying error categories will make it crash because it uses virtual functions.

---

## Comment 3

> Username: SignalWhisperer  
> Created at: 2022-03-03 15:55:36 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058189375  

That's what I thought when I looked over it. Should this be mentioned in the documentation? I could not really find anything with regards to that other than code comments at  
https://github.com/boostorg/system/blob/develop/include/boost/system/detail/error_category.hpp#L97  
  
Maybe I missed it.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-03-03 16:17:11 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058221002  

No, there isn't anything in the documentation about it. Where do you think would be a good place to put it?

---

## Comment 5

> Username: SignalWhisperer  
> Created at: 2022-03-03 18:28:22 UTC  
> Updated at: 2022-03-03 18:28:47 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058356820  

Maybe in `Design Rationale`. There seems to be a mention where it says `error_code` and `error_condition` "do not require heap allocation". Maybe adding in there that `error_category` cannot be destroyed and will hold on to a heap-allocated pointer or atomic pointer, which could appear as a memory leak, but is strictly necessary.  
  
I haven't tried to test all possible ways to generate this allocation. It should generate it once when using std::errc, but does produce the same behavior for each category type? For example, if using 4 different `error_category`, does it have to allocate this memory 4 times?

---

## Comment 6

> Username: pdimov  
> Created at: 2022-03-03 18:33:48 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058361231  

It creates a corresponding `std::error_category` for each category that isn't `generic` or `system`, if you convert `error_code` to `std::error_code` or `error_condition` to `std::error_condition`.  
  
The easiest way to avoid this in your example is to compare against `boost::system::errc::something` instead of `std::errc::something`, although this may not be possible in context.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-03-03 18:36:07 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058363162  

The best place to document this seems to be in comments around https://github.com/boostorg/system/blob/develop/include/boost/system/detail/error_category_impl.hpp#L146, because that's where one would look. I doubt that putting it in the documentation will be easy to discover as there's no natural place to look once you discover that valgrind warns.

---

## Comment 8

> Username: SignalWhisperer  
> Created at: 2022-03-03 18:39:12 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058365720  

For reference, the valgrind trace:  
```  
==531322== 16 bytes in 1 blocks are still reachable in loss record 1 of 1  
==531322==    at 0x483BE63: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==531322==    by 0x10CD75: boost::system::error_category::operator std::_V2::error_category const&() const (error_category_impl.hpp:148)  
==531322==    by 0x10C485: boost::system::error_code::operator std::error_code() const (error_code.hpp:547)  
==531322==    by 0x10D8A8: bool boost::system::operator==<std::errc, void>(boost::system::error_code const&, std::errc) (error_code.hpp:453)  
==531322==    by 0x10B4D1: main (main.cpp:10)  
```  
  
I agree, it should be documented where one would look to further investigate, thus around the call to `new`.  
  
As for the workaround, thanks, I'll use that from now on. It most definitely works.

---

## Comment 9

> Username: SignalWhisperer  
> Created at: 2022-03-03 18:40:26 UTC  
> Url: https://github.com/boostorg/system/issues/78#issuecomment-1058366739  

The valgrind output is using Boost 1.78.0, thus line 148 in error_category_impl.hpp corresponds to line 146 you linked in the current develop branch.
