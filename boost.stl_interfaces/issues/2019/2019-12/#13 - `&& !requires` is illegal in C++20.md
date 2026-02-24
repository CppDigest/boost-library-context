# #13 - `&& !requires` is illegal in C++20 [Closed]

> Username: akrzemi1  
> Created at: 2019-12-23 10:54:36 UTC  
> Updated at: 2020-01-09 08:18:47 UTC  
> Closed at: 2020-01-09 08:18:47 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/13  

The implementation in `iterator_interface.hpp` (I do not know about other files) in six places uses the construct like the following one:  
https://github.com/tzlaine/stl_interfaces/blob/master/include/boost/stl_interfaces/iterator_interface.hpp#L625  
  
That is,  
  
```c++  
constexpr decltype(auto) operator++()  
 requires requires { ++access::base(derived()); } &&  
          !requires { derived() += difference_type(1); }  
```  
  
According to the letter of the Standard [[temp.constr.op]](http://eel.is/c++draft/temp.constr#op-5) negation is not an atomic constraint. There is no way in C++20 to say directly "I require that concept `A` is satisfied and concept `B` is not satisfied).  
  
The above code does not compile. You could make it to compile by wrapping the second operand of conjunction with parentheses:  
  
```c++  
constexpr decltype(auto) operator++()  
 requires requires { ++access::base(derived()); } &&  
          (!requires { derived() += difference_type(1); })  
```  
  
But now, it is only an expression and it does not work in subsumption relation when the best overload is selected. If subsumption (and concept ordering) is essential for your library, you would have to define for each of those ad hoc constraint a dedicated negated concept:  
  
```c++  
template <typename D, typename difference_type>  
  concept no_plus_equals = !requires(D& d) { d += difference_type(1); };   
  
constexpr decltype(auto) operator++()  
 requires requires { ++access::base(derived()); } &&  
          no_plus_equals<D, difference_type>  
```
