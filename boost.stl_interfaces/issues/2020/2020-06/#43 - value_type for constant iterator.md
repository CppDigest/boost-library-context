# #43 - value_type for constant iterator [Closed]

> Username: rolandschulz  
> Created at: 2020-06-23 05:38:44 UTC  
> Updated at: 2020-06-23 23:29:41 UTC  
> Closed at: 2020-06-23 23:19:54 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/43  

Taking the example from https://boostorg.github.io/stl_interfaces/doc/html/boost_stlinterfaces/examples/mutable_and_constant_iterator_interoperability.html and replace the `std::copy` call with `std::partial_sum` this fails to compile. This is because for `partial_sum` a requirement is that "`iterator_traits<InputIt>::value_type` can be assigned". This requirement is fulfilled by all common STL iterators. E.g. `std::vector<T>::const_iterator` is `T` and not `const T`.   
  
Is there a reason why the documentation suggests to create an iterator so that `const_iterator::value_type` is `const int` and not `int`? And if this isn't intentional and should be fixed what's the correct fix? Should the example be changed to   
  
```c++  
struct random_access_iterator : boost::stl_interfaces::iterator_interface<  
                                    random_access_iterator<ValueType>,  
                                    std::random_access_iterator_tag,  
                                    std::remove_const_t<ValueType>,  
                                    ValueType&, ValueType*>  
```  
?  
Or should the `iterator_interface` be changed  from  
```c++  
using value_type = ValueType;  
```  
to  
```c++  
using value_type = std::remove_const_t<ValueType>;   
```  
so that the example doesn't have to be changed?

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-06-23 23:19:28 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/43#issuecomment-648477253  

Thanks for pointing this out!  You have great timing -- the next Boost release freezes tomorrow.  
  
I decided just to remove the const from value_type within iterator_interface, since that's the same behavior that you see when you use `std::iterator_traits`.  For instance, `std::iterator_traits<int const *>::type` is `int`, not `int const`.

---

## Comment 2

> Username: rolandschulz  
> Created at: 2020-06-23 23:29:41 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/43#issuecomment-648480277  

Thanks a lot for the very fast fix.
