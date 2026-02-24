# #80 Make move ctor move the write_op [Closed]

> Username: siyengar  
> Created at: 2018-02-02 04:49:13 UTC  
> Updated at: 2020-12-30 02:10:42 UTC  
> Closed at: 2020-12-30 02:10:41 UTC  
> Url: https://github.com/boostorg/asio/pull/80  

During profiling using TLS with libtorrent,  
which uses asio for TLS support, we found  
that libtorrent instantiates write_op with  
a std::list<iovec>. Whenever an async operation  
is instantiated asio will create a new io_op  
with the write_op which holds the std::list.  
The move ctor of io_op copies the list every  
time. We see that this causes a lot of allocations  
to be triggered which became a major bottleneck  
for an internal service. We bandaided it by using  
a vector instead of a list and that resulted in  
a major speedup in performance.  
  
This PR fixes the move ctor of io_op to move the  
write_op instead.  
  
Test plan:  
Ran all the asio unit tests on a mac, and they  
pass.

---

## Comment 1

> Username: siyengar  
> Created_at: 2018-02-02 04:50:28 UTC  
> Updated_at: 2018-02-02 04:53:19 UTC  
> Url: https://github.com/boostorg/asio/pull/80#issuecomment-362485837  

It's my first PR to boost and also working with submodules, so hopefully I did this right

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:10:41 UTC  
> Url: https://github.com/boostorg/asio/pull/80#issuecomment-752302214  

Fixed in asio 1.12.2 / boost 1.69.

---
