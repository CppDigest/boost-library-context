# #23 - Errors with boost(1.70.0) in using ptr_vector [Closed]

> Username: kkumar45  
> Created at: 2019-06-27 10:09:25 UTC  
> Updated at: 2019-07-05 14:14:26 UTC  
> Closed at: 2019-07-05 14:14:26 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23  

I am moving from Boost 1.65.1 to 1.70.0. My project has the usage of "ptr_vector" in function parameter.   
The build is failing in 1.70.0. I have written a program that demonstrates the problem in using "ptr_vector" in function parameter.  
  
Command used for compiling:  
`g++ -std=c++14 ptr_vector_test.cpp -Iboost_1_70_0`  
  
Test Program:  
  
```  
#include <boost/ptr_container/ptr_vector.hpp>  
namespace A  
{  
    template<class T>  
    class Foo  
    {  
        public:  
        static void test(boost::ptr_vector<Foo<T>>& t){  
            return;  
        }  
    };  
    template<class T>  
    using test = boost::ptr_vector<Foo<T>>;  
}  
  
int main()  
{  
  
    A::test<double> ptr;  
    return 0;  
}  
```  
  
Error (On Linux : gcc 6.3.0)  
  
```  
ptr_vector_test.cpp: In instantiation of ‘class A::Foo<double>’:  
boost_1_70_0/boost/ptr_container/nullable.hpp:55:13:   recursively required by substitution of ‘template<class T> boost::type_traits::yes_type boost::ptr_container_detail::is_nullable(const boost::nullable<T>*) [with T = <missing>]’  
boost_1_70_0/boost/ptr_container/nullable.hpp:55:13:   required from ‘const bool boost::is_nullable<A::Foo<double> >::value’  
boost_1_70_0/boost/mpl/if.hpp:63:11:   required from ‘struct boost::mpl::if_<boost::is_nullable<A::Foo<double> >, A::Foo<double>, boost::mpl::identity<A::Foo<double> > >’  
boost_1_70_0/boost/mpl/eval_if.hpp:37:41:   required from ‘struct boost::mpl::eval_if<boost::is_nullable<A::Foo<double> >, A::Foo<double>, boost::mpl::identity<A::Foo<double> > >’  
boost_1_70_0/boost/ptr_container/nullable.hpp:69:13:   required from ‘struct boost::remove_nullable<A::Foo<double> >’  
boost_1_70_0/boost/ptr_container/nullable.hpp:80:55:   required from ‘struct boost::ptr_container_detail::void_ptr<A::Foo<double> >’  
ptr_vector_test.cpp:18:43:   required by substitution of ‘template<class T> using test = boost::ptr_vector<A::Foo<T> > [with T = double]’  
ptr_vector_test.cpp:26:19:   required from here  
ptr_vector_test.cpp:11:21: error: no type named ‘type’ in ‘struct boost::ptr_container_detail::void_ptr<A::Foo<double> >’  
         static void test(boost::ptr_vector<Foo<T>>& t){  
  
```  
  
Clang command:  
  
`clang++ -std=c++14 ptr_vector_test.cpp -Iboost_1_70_0`  
  
Error( On Clang):  
  
```  
In file included from ptr_vector_test.cpp:1:  
boost_1_70_0/boost/ptr_container/ptr_vector.hpp:35:91: error: no type named 'type' in 'boost::ptr_container_detail::void_ptr<A::Foo<double> >'  
        class Allocator      = std::allocator<typename ptr_container_detail::void_ptr<T>::type>  
                                              ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
ptr_vector_test.cpp:11:33: note: in instantiation of default argument for 'ptr_vector<A::Foo<double>, boost::heap_clone_allocator>' required here  
        static void test(boost::ptr_vector<Foo<T>>& t){  
                                ^~~~~~~~~~~~~~~~~~  
boost_1_70_0/boost/ptr_container/nullable.hpp:55:57: note: in instantiation of template class 'A::Foo<double>' requested here  
            BOOST_STATIC_CONSTANT(bool, value = sizeof( ptr_container_detail::is_nullable( var ) )   
                                                        ^  
boost_1_70_0/boost/mpl/if.hpp:63:68: note: in instantiation of template class 'boost::is_nullable<A::Foo<double> >' requested here  
          BOOST_MPL_AUX_STATIC_CAST(bool, BOOST_MPL_AUX_VALUE_WKND(T1)::value)  
                                                                   ^  
boost_1_70_0/boost/mpl/eval_if.hpp:37:22: note: in instantiation of template class 'boost::mpl::if_<boost::is_nullable<A::Foo<double> >, A::Foo<double>, boost::mpl::identity<A::Foo<double> > >' requested here  
    typedef typename if_<C,F1,F2>::type f_;  
                     ^  
boost_1_70_0/boost/ptr_container/nullable.hpp:66:45: note: in instantiation of template class 'boost::mpl::eval_if<boost::is_nullable<A::Foo<double> >, A::Foo<double>, boost::mpl::identity<A::Foo<double> > >' requested here  
        typedef BOOST_DEDUCED_TYPENAME mpl::eval_if< is_nullable<T>,  
                                            ^  
boost_1_70_0/boost/ptr_container/nullable.hpp:79:61: note: in instantiation of template class 'boost::remove_nullable<A::Foo<double> >' requested here  
                              BOOST_DEDUCED_TYPENAME boost::remove_nullable<T>::type >::value,  
                                                            ^  
boost_1_70_0/boost/ptr_container/ptr_vector.hpp:35:78: note: in instantiation of template class 'boost::ptr_container_detail::void_ptr<A::Foo<double> >' requested here  
        class Allocator      = std::allocator<typename ptr_container_detail::void_ptr<T>::type>  
                                                                             ^  
ptr_vector_test.cpp:18:5: note: in instantiation of default argument for 'ptr_vector<A::Foo<double>, boost::heap_clone_allocator>' required here  
    using test = boost::ptr_vector<Foo<T>>;  
    ^~~~~  
ptr_vector_test.cpp:26:8: note: in instantiation of template type alias 'test' requested here  
    A::test<double> ptr;  
       ^  
1 error generated.  
```

---

## Comment 1

> Username: kkumar45  
> Created at: 2019-07-02 03:39:22 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23#issuecomment-507505477  

Can anyone reproduce it?

---

## Comment 2

> Username: mclow  
> Created at: 2019-07-03 16:35:57 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23#issuecomment-508167086  

Yep, I'm seeing it.  
  
It appears to me that the basic problem is that `boost::ptr_vector<Foo<T>>` is not a complete type when you are trying to use it (you're still defining `Foo`).

---

## Comment 3

> Username: mclow  
> Created at: 2019-07-03 17:02:06 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23#issuecomment-508176350  

Verified that it fails with Boost 1.66.0 as well.

---

## Comment 4

> Username: mclow  
> Created at: 2019-07-03 17:06:37 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23#issuecomment-508177956  

This commit appears to be the behavior change: https://github.com/boostorg/ptr_container/commit/d1a588b25d2d7d510dadcf5d839c77b0998c58ab

---

## Comment 5

> Username: pdimov  
> Created at: 2019-07-05 07:36:43 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/23#issuecomment-508662664  

See PR #24.
