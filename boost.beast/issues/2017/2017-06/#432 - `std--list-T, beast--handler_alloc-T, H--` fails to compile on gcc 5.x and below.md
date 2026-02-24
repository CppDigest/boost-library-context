# #432 - `std::list<T, beast::handler_alloc<T, H>>` fails to compile on gcc 5.x and below. [Closed]

> Username: harrishancock  
> Created at: 2017-06-07 20:49:06 UTC  
> Updated at: 2017-06-09 22:07:48 UTC  
> Closed at: 2017-06-09 22:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/432  

I'm using Beast's master branch.  
  
The `std::list` implementation that ships with gcc 5.x and below doesn't wrap allocator usage in `std::allocator_traits`.  
  
Since `beast::handler_alloc` doesn't provide some optional member typedefs, this leads to compile errors when instantiating `std::list<T, beast::handler_alloc<T, H>>`:  
  
```  
/usr/include/c++/5/bits/stl_list.h:520:58: error: no type named ‘pointer’ in ‘std::__cxx11::list<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Tp_alloc_type {aka class beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler>}’  
       typedef typename _Tp_alloc_type::pointer           pointer;  
                                                          ^  
/usr/include/c++/5/bits/stl_list.h:521:58: error: no type named ‘const_pointer’ in ‘std::__cxx11::list<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Tp_alloc_type {aka class beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler>}’  
       typedef typename _Tp_alloc_type::const_pointer     const_pointer;  
                                                          ^  
/usr/include/c++/5/bits/stl_list.h:522:58: error: no type named ‘reference’ in ‘std::__cxx11::list<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Tp_alloc_type {aka class beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler>}’  
       typedef typename _Tp_alloc_type::reference         reference;  
                                                          ^  
/usr/include/c++/5/bits/stl_list.h:523:58: error: no type named ‘const_reference’ in ‘std::__cxx11::list<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Tp_alloc_type {aka class beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler>}’  
       typedef typename _Tp_alloc_type::const_reference   const_reference;  
```  
  
This is fixed in gcc 6 and above. While this is a bug in libstdc++ ([bug report]), the maintainer says he does not plan to backport the fix into 5.x, and 5.x is still in widespread use.  
  
[bug report]: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=55409

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-07 20:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-306921501  

Very nice to see someone using the handler allocator! You're writing a composed operation I take it then? Have you seen this tutorial?  
http://vinniefalco.github.io/beast/beast/core/op_tutorial.html  
  
Anyway if its as easy as adding those types, I'll make sure its in v50 (doing it now)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-07 20:59:51 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-306923314  

Keep opening those issues and keep those bugs and suggestions coming, they are really helping to improve Beast!!!

---

## Comment 3

> Username: harrishancock  
> Created at: 2017-06-07 21:36:24 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-306932457  

Thanks @vinniefalco! Sorry I've been quiet -- work has kept me sorely busy.  
  
Indeed I am writing a composed operation! I saw that you added the composed op tutorial, but haven't read it yet. I'll read it and give you feedback today or tomorrow -- though I anticipate that you probably nailed it. ;)

---

## Comment 4

> Username: harrishancock  
> Created at: 2017-06-08 21:03:19 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307226766  

It looks like the libstdc++ in question also wants the `destroy()` and `construct()` member functions. :/  
  
```  
/usr/include/c++/5/bits/list.tcc:75:4: error: ‘std::__cxx11::_List_base<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Node_alloc_type {aka class beast::handler_alloc<std::_List_node<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, main_handler>}’ has no member named ‘destroy’  
    _M_get_Node_allocator().destroy(__tmp);  
/usr/include/c++/5/bits/stl_list.h:574:8: error: ‘std::__cxx11::_List_base<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Node_alloc_type {aka class /usr/include/c++/5/bits/stl_list.h:574:8: error: ‘std::__cxx11::_List_base<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, beast::handler_alloc<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, main_handler> >::_Node_alloc_type {aka class beast::handler_alloc<std::_List_node<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, main_handler>}’ has no member named ‘construct’  
        _M_get_Node_allocator().construct(__p,  
```  
  
After I un-`#ifdef`ed the `destroy` and added:  
  
```c++  
    template <class U, class... Args>  
    void construct(U* ptr, Args&&... args) {  
        ::new((void*)ptr) U(std::forward<Args>(args)...);  
    }  
```  
  
It compiled.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-08 21:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307236326  

Can I just add them in and everything will be ok?

---

## Comment 6

> Username: harrishancock  
> Created at: 2017-06-08 21:54:50 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307238308  

Those are the only changes needed to make it work for me with gcc 5.4.  
  
I suppose a consequence is that `std::allocator_traits` will use the `construct` and `destroy` functions you write, making Beast responsible for their correctness. A compromise might be to `#ifdef` the functions in for specific versions of libstdc++. I can come up with an expression to do this, if you're interested. I'm also fine with a wontfix if this is too much of a question mark -- using `boost::container::list` instead of `std::list` is an easy workaround. :)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-08 21:58:21 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307239008  

The `construct` and `destroy` in my commit look benign, can you think of a scenario where they won't work?

---

## Comment 8

> Username: harrishancock  
> Created at: 2017-06-08 22:00:03 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307239363  

No, I can't -- they look benign to me, too.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-08 22:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307239908  

This will go into v51 (in the Pull Request queue now)

---

## Comment 10

> Username: harrishancock  
> Created at: 2017-06-08 22:03:57 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307240163  

Thank you Vinnie!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-09 00:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307262270  

I'm working on conditionally activating this based on the version of libstdc++

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-09 00:48:09 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307265406  

Got that working with help from @glenfe, this is the secret sauce:  
```  
#if BOOST_WORKAROUND(BOOST_LIBSTDCXX_VERSION, < 60000)  
```

---

## Comment 13

> Username: harrishancock  
> Created at: 2017-06-09 00:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307265862  

One way, using Boost.Predef.  
  
```c++  
#include <boost/predef.h>  
// ...  
#if BOOST_LIB_STD_GNU && BOOST_LIB_STD_GNU < BOOST_VERSION_NUMBER(46, 4, 27)  
    void destroy(...);  
    void construct(...);  
#endif  
```  
  
I pulled the 46.4.27 number from Wandbox running gcc 6.1.0.  
  
But it looks like you and @glenfe beat me. ;)

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-09 00:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/432#issuecomment-307265947  

That's good to have in the back pocket just in case though
