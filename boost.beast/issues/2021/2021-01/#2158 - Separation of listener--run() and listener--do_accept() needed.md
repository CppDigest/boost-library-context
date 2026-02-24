# #2158 - Separation of listener::run() and listener::do_accept() needed? [Closed]

> Username: avitase  
> Created at: 2021-01-30 17:01:41 UTC  
> Updated at: 2022-01-09 05:17:14 UTC  
> Closed at: 2022-01-09 05:17:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2158  

I am wondering if the separation of `listener::run()`  
  
https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L598  
  
and `listener::do_accept()`  
  
https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L613  
  
is needed. Since `run` just dispatches `do_accept` but the latter itself only initiate an asynchronous operation the additional runtime overhead, i.e., the time `run()` blocks, should be negligible. Further, if `run()` and `do_accept()` could be merged one could get rid of `acceptor_`'s strand, cf.  
  
https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L558  
  
Am I missing something?

---

## Comment 1

> Username: mzimbres  
> Created at: 2021-01-30 17:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770248072  

The comment says it all  
> Although not strictly necessary  for single-threaded contexts, this example code is written to be  thread-safe by default.  
  
dispatch guarantees that `accept_.async_accept `will be called on the correct executor.

---

## Comment 2

> Username: avitase  
> Created at: 2021-01-30 17:43:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770249795  

Thanks for your help. But I don't understand: what is the correct executor? The completion handler of `async_accept` is called in a new executor, cf.  
  
https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L618  
  
and when `do_accept()` is called, it is associated with `acceptor_`'s strand. In fact, since dispatch does not forbid an immediate execution (directly below on the call stack), `run()` could indeed be equivalent to `run() { do_accept(); }` under certain circumstances. Why not elide this then?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-30 17:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770250841  

The code is written to be robust in case of refactoring.  
  
We took the view that ensuring that all accesses to the listener and its  
sub objects takes place on the listener’s strand is good practice for  
negligible cost.  
  
  
On Sat, 30 Jan 2021 at 18:43, Nis Meinert <notifications@github.com> wrote:  
  
> Thanks for your help. But I don't understand: what is the correct  
> executor? The completion handler of async_accept is called in a new  
> executor, cf.  
>  
>  
> https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L618  
>  
> and when do_accept() is called, it is associated with acceptor_'s strand.  
> In fact, since dispatch does not forbid an immediate execution (directly  
> below on the call stack), run() could indeed be equivalent to run() {  
> do_accept(); } under certain circumstances. Why not elide this then?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2158#issuecomment-770249795>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSOAYHX5IUJV54K4HA3S4RAMTANCNFSM4W2PFY5Q>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 4

> Username: mzimbres  
> Created at: 2021-01-30 17:57:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770251908  

I believe the code would be equally correct but less confusing if the call to `async_accept ` would use the overload without the executor as parameter as it is already in the correct one.  
  
https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/reference/basic_socket_acceptor/async_accept/overload3.html

---

## Comment 5

> Username: avitase  
> Created at: 2021-01-30 17:58:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770252090  

ok. I understand. So you agree that in the context of this example one could safely move `do_accept()` into `run()`, delete the old `run()` method and also delete the explicit strand  
  
https://github.com/boostorg/beast/blob/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/advanced/server/advanced_server.cpp#L558  
  
even in a multithreaded context?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-01-30 18:47:26 UTC  
> Updated at: 2021-01-30 18:47:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770262538  

> I believe the code would be equally correct but less confusing if the call  
> to async_accept would use the overload without the executor as parameter  
> as it is already in the correct one.  
  
If we did that, then **every** new connection would be put on the same strand as the listening socket, which is clearly not what we want. Each new connection should get a new strand.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-01-30 18:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770262603  

It's probably true that if we do a deep dive we'll agree that this code  
could conflate run() and do_accept().  
  
The problem for me is that I'd have to expend effort in order to prove it,  
as would anyone reading or maintaining the code.  
  
Whereas using the dispatch to explicitly marshall the call onto the correct  
strand, thereby guaranteeing correctness, provides an easy and visible way  
to ensure that the code is correct in all configurations.  
  
So my answer is, "yes it looks like you're right, but I will continue to  
use dispatch in situations like this because the cost is almost zero".  
  
Remember that async_accept is called very rarely in comparison to all other  
IO initiating functions in a typical program.

---

## Comment 8

> Username: mzimbres  
> Created at: 2021-01-30 19:07:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770265375  

> Each new connection should get a new strand  
  
Looks very exotic to me. In a server with many thousands of connections that would mean many thousands of strands, whereas the number of threads people will be calling run() on is not likely to exceed the number of cores. I don't know whether locking mutexes so often can impact performance in a measurable way, it at least make things very unpreductable and difficult to debug. I would not encourage it at least.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-01-30 19:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770268107  

> Looks very exotic to me. In a server with many thousands of connections that would mean many thousands of strands,  
  
Yes, and there's nothing wrong with that. Don't worry about the mutex. If the connections are completely separate (i.e. they do not communicate with each other) then you can use one `io_context` per thread and avoid strands entirely.

---

## Comment 10

> Username: avitase  
> Created at: 2021-02-01 13:17:58 UTC  
> Updated at: 2021-02-01 13:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770850340  

Sorry for coming back to this issue, but I am now wondering why one should assign a strand to an acceptor at all, given that new connections are accepted with their own strand? For example, given the following example (from the documentation):  
```  
net::ip::tcp::acceptor acceptor(net::make_strand(ioc));  
acceptor.bind(net::ip::tcp::endpoint(net::ip::make_address_v4("127.0.0.1"), 0));  
acceptor.listen(0);  
  
net::ip::tcp::socket s = acceptor.accept(net::make_strand(ioc));  
[...]  
```  
What could possibly go wrong when multiple threads are surrendered to `ioc` and no dedicated strand is created for `acceptor`?

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-02-01 14:38:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770904359  

Provided there is only ever one thread touching the accepter at one time (and all memory fences have been correctly handled) then there is no need for a strand.  
  
A strand provides an easy-to-maintain way to ensure that "there is only ever one thread touching the accepter at one time (and all memory fences have been correctly handled)"

---

## Comment 12

> Username: avitase  
> Created at: 2021-02-01 14:53:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770914692  

Thanks again for your help. But even if multiple threads touch `acceptor`, what would be the consequence? Say two threads are surrendered to `ioc` and, by chance, simultaneously handle a new connection. The completion handler of `async_accept` would then be called twice on both threads but I don't see a problem here. Both handlers would associate a new strand and `acceptor` could continue its work. To be more precise, I understand the reason for association of strands to sockets, but I don't understand how multiple threads could interfere with the accepting process.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2021-02-01 15:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-770941455  

Yeah probably the acceptor doesn't need it

---

## Comment 14

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-850857886  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 15

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2158#issuecomment-1008232373  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
