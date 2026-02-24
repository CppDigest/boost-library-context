# #283 - boost/detail/iterator.hpp is deprecated. [Closed]

> Username: TommyPec  
> Created at: 2022-01-19 21:53:39 UTC  
> Updated at: 2022-01-20 03:51:06 UTC  
> Closed at: 2022-01-20 03:51:06 UTC  
> Url: https://github.com/boostorg/graph/issues/283  

This is a followup of #228  
  
The problem is still there as of boost 1.76.0 - with no workaround (as far as I know):  
```  
[...]  
In file included from /usr/local/include/boost/graph/adjacency_list.hpp:255:  
In file included from /usr/local/include/boost/graph/detail/adjacency_list.hpp:37:  
In file included from /usr/local/include/boost/graph/adjacency_iterator.hpp:13:  
/usr/local/include/boost/detail/iterator.hpp:13:1: warning: This header is deprecated. Use <iterator> instead. [-W#pragma-messages]  
BOOST_HEADER_DEPRECATED("<iterator>")  
^  
/usr/local/include/boost/config/header_deprecated.hpp:23:37: note: expanded from macro 'BOOST_HEADER_DEPRECATED'  
# define BOOST_HEADER_DEPRECATED(a) BOOST_PRAGMA_MESSAGE("This header is deprecated. Use " a " instead.")  
                                    ^  
/usr/local/include/boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:73:2: note: expanded from here  
 message("This header is deprecated. Use " "<iterator>" " instead.")  
 ^  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-01-20 03:51:05 UTC  
> Url: https://github.com/boostorg/graph/issues/283#issuecomment-1017094058  

I reopened the original issue, sorry for the delay.
