# #55 - boost_log 1.67 can't compile on CentOS 5.0 [Closed]

> Username: yokarno  
> Created at: 2018-06-21 01:08:30 UTC  
> Updated at: 2018-06-21 23:25:51 UTC  
> Closed at: 2018-06-21 23:25:51 UTC  
> Url: https://github.com/boostorg/log/issues/55  

Hello:  
  I try to compile boost_log on CentOS 5.0, but it show the error:  
  
./boost/type_traits/is_class.hpp:51: warning: lowering visibility of ‘boost::type_traits::no_type boost::detail::is_class_tester(...) [with U = long long unsigned int]’ to match its type  
/usr/include/linux/futex.h:96: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:96: error: ‘uaddr’ was not declared in this scope  
/usr/include/linux/futex.h:96: error: expected primary-expression before ‘int’  
/usr/include/linux/futex.h:96: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:96: error: expected primary-expression before ‘unsigned’  
/usr/include/linux/futex.h:97: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:97: error: ‘uaddr2’ was not declared in this scope  
/usr/include/linux/futex.h:97: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:97: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:97: error: initializer expression list treated as compound expression  
/usr/include/linux/futex.h:100: error: ‘u32’ was not declared in this scope  
/usr/include/linux/futex.h:100: error: ‘uaddr’ was not declared in this scope  
/usr/include/linux/futex.h:100: error: expected primary-expression before ‘struct’  
/usr/include/linux/futex.h:100: error: expected primary-expression before ‘int’  
/usr/include/linux/futex.h:100: error: initializer expression list treated as compound expression  
./boost/type_traits/is_class.hpp: In instantiation of ‘const bool boost::detail::is_class_impl<uint32_t>::value’:  
./boost/type_traits/is_class.hpp:105:   instantiated from ‘boost::is_class<uint32_t>’  
./boost/type_traits/is_enum.hpp:44:   instantiated from ‘const bool boost::detail::is_class_or_union<uint32_t>::value’  
./boost/type_traits/is_enum.hpp:108:   instantiated from ‘const bool boost::detail::is_enum_impl<uint32_t>::selector’  
  
Does this version do not compatible with such an old CentOS ? or there has some way to avoid it ?

---

## Comment 1

> Username: Lastique  
> Created at: 2018-06-21 08:49:54 UTC  
> Url: https://github.com/boostorg/log/issues/55#issuecomment-399026110  

Given that the error is in the system headers, I would guess that the problem is that some system macros clash with linux/futex.h. Looks like something sockets-related. You can try patching Boost.Log to avoid using futexes directly (see boost/log/detail/event.hpp) or try deducing what causes the clash.

---

## Comment 2

> Username: yokarno  
> Created at: 2018-06-21 23:25:51 UTC  
> Url: https://github.com/boostorg/log/issues/55#issuecomment-399273977  

yes I removed include futex.h from event.hpp and add macors which used , then it's OK.
