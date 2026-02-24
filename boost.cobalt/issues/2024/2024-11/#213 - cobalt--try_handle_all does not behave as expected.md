# #213 - cobalt::try_handle_all does not behave as expected [Closed]

> Username: Kajonn  
> Created at: 2024-11-13 09:02:48 UTC  
> Updated at: 2024-12-13 03:21:08 UTC  
> Closed at: 2024-12-13 03:21:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213  

Thanks for a great library!  
  
We have discovered that cobalt::try_handle_all may not "catch" errors as expected (as boost::leaf::try_handle_all does).  
  
This example program is expected to print 1 but print 2, indicating the SomeError cannot be handled:  
  
```  
 #include <boost/cobalt.hpp>  
#include <boost/cobalt/leaf.hpp>  
#include <boost/cobalt/main.hpp>  
#include <boost/leaf/handle_errors.hpp>  
#include <boost/leaf/result.hpp>  
#include <iostream>  
struct SomeError  
{  
    int val;  
};  
  
boost::cobalt::main co_main(int, char *[])  
{  
    auto r = co_await boost::cobalt::try_handle_all(  
        []() -> boost::cobalt::promise<boost::leaf::result<int>> {  
            co_return boost::leaf::new_error(SomeError {1234});  
            co_return 0;  
        }(),  
        [](SomeError &) -> int {  
            return 1;  
        },  
        []() -> int {  
            return 2;  
        });  
    std::cout << r << std::endl;  
    co_return r;  
}  
```  
  
The example using boost::leaf::try_handle_all does print 1 as expected:  
  
```  
#include <boost/leaf/handle_errors.hpp>  
#include <boost/leaf/result.hpp>  
#include <iostream>  
  
struct SomeError  
{  
    int val;  
};  
  
  
int main() {  
    auto r = boost::leaf::try_handle_all(  
        []() -> boost::leaf::result<int> {  
            return boost::leaf::new_error(SomeError {1234});  
        },  
        [](SomeError &e) {  
            return 1;  
        },  
        []() {  
            return 2;  
        });  
    std::cout << r << std::endl;      
    return r;  
}  
```  
  
Is it a bug or is it something wrong with our implementation in the example?  
  
Best regards  
Jonas

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-11-13 10:57:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2473195493  

Probably a bug.

---

## Comment 2

> Username: Kajonn  
> Created at: 2024-11-27 13:00:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2503819853  

Have discovered the cause of this bug? Are there any fixes in the pipeline? We are a little bit stuck because of this.  
  
Best regards  
Jonas

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-11-28 09:33:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2505655391  

The problem is the way the internal context of leaf works conflicts with asynchronicity. That is, it activates a context after the error was created thus losing the type erased handler. I don't know if that can be made to work; I misunderstood some of the internals of leaf.  
  
What are your requirements? using LEAF specifically?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-11-28 11:02:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2505845155  

After some more thinking I have to sadly conclude: this cannot work outside of minimalistic examples.  
  
LEAF creates a context for error handling and needs this to be active when creating a new error_id. ( I was testing with existing one by using exceptions...).  
In a normal program flow that's find, you create the context thread-locally in `try_handle` and then it's there when you create the error. In an asynchronous program this won't work, because you might have N coroutines all using try_handle, and thus N context. And there is no `coroutine_local`.  
  
This means that I could potentially write dedicated coroutine type, but then this couldn't await any `promise` or `task` that returns a `leaf::result`.   
  
I.e.  
  
```cpp  
leaf::coro_thingy<int> foo() {co_return leaf::result<int>(); } // fine  
  
promise<boost::leaf::resuit<int> foo_inner() {co_return leaf::new_error<int>();   
  
leaf::coro_thingy<int> bar {co_return co_await foo_inner(); } // compile error OR silent disabling of the context. either one is bad.  
  
```  
  
Alternatively you could pass down an explicit context that you would need to active & deactivate manually. But that also would be verbose and error-prone.

---

## Comment 5

> Username: Kajonn  
> Created at: 2024-11-28 12:49:07 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2506045007  

Ah, too bad. Thanks for looking into this issue.  We will use something like std::expected or boost::outcome instead.  
  
Best regards  
Jonas

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-11-29 04:49:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/213#issuecomment-2507085019  

Just FYI: There is also some support for `boost::system::result` in the library, just in case you interact with asio.  
  
```cpp  
extern asio::readable_pipe;  
  
system::result<std::size_t> r = co_await cobalt::as_result(pipe.read_some(..., cobalt::use_op));  
```
