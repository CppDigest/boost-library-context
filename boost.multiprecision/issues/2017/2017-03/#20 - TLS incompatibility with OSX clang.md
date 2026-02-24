# #20 - TLS incompatibility with OSX clang [Closed]

> Username: evoskuil  
> Created at: 2017-03-26 07:37:54 UTC  
> Updated at: 2017-05-31 18:49:48 UTC  
> Closed at: 2017-05-31 18:22:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/20  

Full details [here](https://github.com/libbitcoin/libbitcoin/issues/733).  
  
```  
In file included from /usr/local/include/boost/multiprecision/detail/default_ops.hpp:1605:  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:246:47: error: initializer  
for thread-local variable must be a constant expression  
...long digits = boost::multiprecision::detail::digits2<number >::value();  
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:246:38: note: use  
'thread_local' to allow this  
static BOOST_MP_THREAD_LOCAL long digits = boost::multiprecision::detail::digits2<number...  
^  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:267:47: error: initializer  
for thread-local variable must be a constant expression  
...long digits = boost::multiprecision::detail::digits2<number >::value();  
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:267:38: note: use  
'thread_local' to allow this  
static BOOST_MP_THREAD_LOCAL long digits = boost::multiprecision::detail::digits2<number...  
^  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:285:47: error: initializer  
for thread-local variable must be a constant expression  
...long digits = boost::multiprecision::detail::digits2<number >::value();  
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/multiprecision/detail/functions/constants.hpp:285:38: note: use  
'thread_local' to allow this  
static BOOST_MP_THREAD_LOCAL long digits = boost::multiprecision::detail::digits2<number...  
^  
In file included from src/utility/random.cpp:28:  
In file included from ./include/bitcoin/bitcoin/constants.hpp:25:  
In file included from ./include/bitcoin/bitcoin/config/checkpoint.hpp:27:  
In file included from ./include/bitcoin/bitcoin/formats/base_16.hpp:24:  
In file included from ./include/bitcoin/bitcoin/math/hash.hpp:26:  
In file included from /usr/local/include/boost/multiprecision/cpp_int.hpp:12:  
In file included from /usr/local/include/boost/multiprecision/number.hpp:22:  
In file included from /usr/local/include/boost/multiprecision/detail/generic_interconvert.hpp:9:  
In file included from /usr/local/include/boost/multiprecision/detail/default_ops.hpp:1606:  
/usr/local/include/boost/multiprecision/detail/functions/pow.hpp:384:47: error: initializer for  
thread-local variable must be a constant expression  
...long digits = boost::multiprecision::detail::digits2<number >::value();  
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/multiprecision/detail/functions/pow.hpp:384:38: note: use 'thread_local'  
to allow this  
static BOOST_MP_THREAD_LOCAL long digits = boost::multiprecision::detail::digits2<number...  
^  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-05-31 18:22:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/20#issuecomment-305274207  

Closing down as this seems to be caused by macro redefinition of thread_local in libbitcoin.

---

## Comment 2

> Username: evoskuil  
> Created at: 2017-05-31 18:49:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/20#issuecomment-305281770  

@jzmaddock thanks for your help on this!
