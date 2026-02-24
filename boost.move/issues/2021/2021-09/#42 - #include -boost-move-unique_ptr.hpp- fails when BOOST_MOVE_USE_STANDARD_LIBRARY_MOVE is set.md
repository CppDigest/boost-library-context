# #42 - #include <boost/move/unique_ptr.hpp> fails when BOOST_MOVE_USE_STANDARD_LIBRARY_MOVE is set [Closed]

> Username: jm-mikkelsen  
> Created at: 2021-09-15 13:00:21 UTC  
> Updated at: 2021-10-21 14:11:46 UTC  
> Closed at: 2021-10-21 14:11:36 UTC  
> Url: https://github.com/boostorg/move/issues/42  

As in the heading.  
  
To reproduce:  
  
```  
$ echo '#include <boost/move/unique_ptr.hpp>' > test.cpp  
$ c++ -c -std=c++14 -I/usr/local/include/boost-default -DBOOST_MOVE_USE_STANDARD_LIBRARY_MOVE test.cpp  
In file included from test.cpp:1:  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:530:67: error: 'D'  
      does not refer to a value  
      : m_data(u.release(), ::boost::move_if_not_lvalue_reference<D>(u.g...  
                                                                  ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:353:26: note:   
      declared here  
template <class T, class D = default_delete<T> >  
                         ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:530:38: error: no  
      member named 'move_if_not_lvalue_reference' in namespace 'boost'  
      : m_data(u.release(), ::boost::move_if_not_lvalue_reference<D>(u.g...  
                            ~~~~~~~~~^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:552:67: error: 'E'  
      does not refer to a value  
      : m_data(u.release(), ::boost::move_if_not_lvalue_reference<E>(u.g...  
                                                                  ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:548:29: note:   
      declared here  
   template <class U, class E>  
                            ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:552:38: error: no  
      member named 'move_if_not_lvalue_reference' in namespace 'boost'  
      : m_data(u.release(), ::boost::move_if_not_lvalue_reference<E>(u.g...  
                            ~~~~~~~~~^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:581:64: error: 'D'  
      does not refer to a value  
      m_data.deleter() = ::boost::move_if_not_lvalue_reference<D>(u.get_...  
                                                               ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:353:26: note:   
      declared here  
template <class T, class D = default_delete<T> >  
                         ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:581:35: error: no  
      member named 'move_if_not_lvalue_reference' in namespace 'boost'  
      m_data.deleter() = ::boost::move_if_not_lvalue_reference<D>(u.get_...  
                         ~~~~~~~~~^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:603:64: error: 'E'  
      does not refer to a value  
      m_data.deleter() = ::boost::move_if_not_lvalue_reference<E>(u.get_...  
                                                               ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:597:29: note:   
      declared here  
   template <class U, class E>  
                            ^  
/usr/local/include/boost-default/boost/move/unique_ptr.hpp:603:35: error: no  
      member named 'move_if_not_lvalue_reference' in namespace 'boost'  
      m_data.deleter() = ::boost::move_if_not_lvalue_reference<E>(u.get_...  
                         ~~~~~~~~~^  
8 errors generated.  
```  
  
This error message is from Clang 10.0.1 on FreeBSD. The same problem occurs with gcc 9.3. Both cases are with Boost 1.74.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-10-21 14:11:46 UTC  
> Url: https://github.com/boostorg/move/issues/42#issuecomment-948658660  

Many thanks for the report!
