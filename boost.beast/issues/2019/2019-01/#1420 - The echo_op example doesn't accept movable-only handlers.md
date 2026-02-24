# #1420 - The echo_op example doesn't accept movable-only handlers [Closed]

> Username: reddwarf69  
> Created at: 2019-01-29 16:47:18 UTC  
> Updated at: 2019-02-03 20:59:32 UTC  
> Closed at: 2019-02-03 20:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1420  

If the echo_op example is modified like this:  
  
```  
class movable_handler {  
public:  
    movable_handler(movable_handler&&) = default;  
    //movable_handler(movable_handler const&) = default;  
  
    void operator()(boost::beast::error_code ec)  
    {  
        if(ec)  
            std::cerr << ": " << ec.message() << std::endl;  
    }  
};  
  
int main(int, char** argv)  
{  
    ...  
    async_echo(sock, movable_handler{});  
    ...  
}  
```  
  
it fails to build with  
  
```  
$ clang++ -c -o echo_op2.o echo_op2.cpp  
In file included from echo_op2.cpp:10:  
In file included from boost/beast/core.hpp:16:  
In file included from boost/beast/core/buffered_read_stream.hpp:370:  
In file included from boost/beast/core/impl/buffered_read_stream.ipp:15:  
In file included from boost/beast/core/handler_ptr.hpp:216:  
boost/beast/core/impl/handler_ptr.ipp:85:14: error: call to implicitly-deleted copy constructor of 'moveable_handler'  
    new(&h_) Handler(std::forward<DeducedHandler>(h));  
             ^       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
echo_op2.cpp:149:11: note: in instantiation of function template specialization 'boost::beast::handler_ptr<echo_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, moveable_handler>::state,  
      moveable_handler>::handler_ptr<moveable_handler &, boost::asio::basic_stream_socket<boost::asio::ip::tcp> &>' requested here  
        : p_(std::forward<DeducedHandler>(handler), stream)  
          ^  
echo_op2.cpp:275:5: note: in instantiation of function template specialization 'echo_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, moveable_handler>::echo_op<moveable_handler &>' requested here  
    echo_op<  
    ^  
echo_op2.cpp:319:5: note: in instantiation of function template specialization 'async_echo<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, moveable_handler>' requested here  
    async_echo(sock, moveable_handler{});  
    ^  
echo_op2.cpp:294:5: note: copy constructor is implicitly deleted because 'moveable_handler' has a user-declared move constructor  
    moveable_handler(moveable_handler&&) = default;  
    ^  
1 error generated.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-29 16:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458615698  

Which version of Beast

---

## Comment 2

> Username: reddwarf69  
> Created at: 2019-01-29 16:51:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458616943  

1.69

---

## Comment 3

> Username: reddwarf69  
> Created at: 2019-01-29 17:00:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458620405  

I don't pretend to understand the magic under async_completion. But I guess the issue is that `init.completion_handler` (https://github.com/boostorg/beast/blob/develop/example/echo-op/echo_op.cpp#L280) is being passed by reference. So in https://github.com/boostorg/beast/blob/develop/example/echo-op/echo_op.cpp#L149 the copy constructor is used.  
  
I would say passing it by reference is the correct thing to do (cheaper than yet another move). Since we know the only user of echo_op is `async_echo` the `echo_op` constructor can happily accept only `DeducedHandler& handler` and move it inside `p_`.

---

## Comment 4

> Username: djarek  
> Created at: 2019-01-30 02:56:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458792792  

It should be `std::move(init.completion_handler)`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-01-30 04:27:54 UTC  
> Updated at: 2019-01-30 14:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458807574  

> (cheaper than yet another move)  
  
Remember that `std::move` doesn't actually move anything, it just performs a `static_cast`. The move happens later, at the end of the call chain.

---

## Comment 6

> Username: reddwarf69  
> Created at: 2019-01-30 11:18:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-458908202  

`Remember that std::move doesn't actually move anything, it just performs a static_cast`  
I got this.  
  
`The move happens later, at the end of the call chain.`  
The `at the end` part does depend on the optimizer?  
What I was worried about was about a move to the argument of the echo_op constructor and then a second move to the handler_ptr.  
  
Was also a bit confused for seeing it without the std::move in ASIO code (e.g. https://github.com/boostorg/asio/blob/886839cf55d65439791515c7de252d2430913f6a/include/boost/asio/basic_stream_socket.hpp#L605)  
  
But no need to show me this on Compiler Explorer. I trust you. I was a plain C dev 1 year ago, I just need to read about perfect forwarding again.

---

## Comment 7

> Username: djarek  
> Created at: 2019-01-30 16:19:52 UTC  
> Updated at: 2019-01-30 16:21:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-459006652  

Unless `async_result` is specialized, `async_completion` will just contain a reference to the handler, so there's only one move in that case.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-02-02 18:20:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1420#issuecomment-459987085  

The fix is `std::move(init.completion_handler)` (which simply casts it, so when it gets deduced it becomes a non-const rvalue reference). All the other composed operations in beast use `std::move`, I just forget it on the echo example.
