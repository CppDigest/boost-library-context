# #79 - Error when I run examples/tensor/simple_expressions.cpp [Closed]

> Username: mrityunjay-tripathi  
> Created at: 2020-03-07 07:53:25 UTC  
> Updated at: 2021-12-15 09:57:52 UTC  
> Closed at: 2020-04-12 13:03:22 UTC  
> Url: https://github.com/boostorg/ublas/issues/79  

I am getting the following error when I run ```examples/tensor/simple_expressions.cpp```  
```  
In file included from simple_expressions.cpp:13:  
In file included from /usr/include/boost/numeric/ublas/tensor.hpp:18:  
In file included from /usr/include/boost/numeric/ublas/tensor/functions.hpp:25:  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: no member named 'l' in '(lambda at  
      /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:122:80)'  
          : e(l.e), op(op.l) {}  
                       ~~ ^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:9: note: in instantiation of member function  
      'boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<unsigned long, long>,  
      std::vector<float, std::allocator<float> > >, boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<unsigned long, long>,  
      std::vector<float, std::allocator<float> > >, (lambda at /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:122:80)>::unary_tensor_expression'  
      requested here  
        return unary_tensor_expression<T,E,OP>( e() , op);  
               ^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: no member named 'l' in '(lambda at  
      /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:112:80)'  
          : e(l.e), op(op.l) {}  
                       ~~ ^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:9: note: in instantiation of member function  
      'boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<unsigned long, long>,  
      std::vector<float, std::allocator<float> > >, boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<unsigned long, long>,  
      std::vector<float, std::allocator<float> > >, (lambda at /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:112:80)>::unary_tensor_expression'  
      requested here  
        return unary_tensor_expression<T,E,OP>( e() , op);  
               ^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: no member named 'l' in '(lambda at  
      /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:126:80)'  
          : e(l.e), op(op.l) {}  
                       ~~ ^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:9: note: in instantiation of member function  
      'boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<unsigned long, long>,  
      std::vector<float, std::allocator<float> > >, boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float,  
      boost::numeric::ublas::basic_column_major<unsigned long, long>, std::vector<float, std::allocator<float> > >, boost::numeric::ublas::tensor<float,  
      boost::numeric::ublas::basic_column_major<unsigned long, long>, std::vector<float, std::allocator<float> > >, (lambda at  
      /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:112:80)>, (lambda at  
      /usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:126:80)>::unary_tensor_expression' requested here  
        return unary_tensor_expression<T,E,OP>( e() , op);  
               ^  
3 errors generated.  
  
```

---

## Comment 1

> Username: bassoy  
> Created at: 2020-04-12 13:03:22 UTC  
> Url: https://github.com/boostorg/ublas/issues/79#issuecomment-612610945  

@mrityunjay-tripathi   
  
Please specify what compiler & environment you have used.  
You can see [here](https://godbolt.org/z/TGf-ev) that the *simple expression example* compiles and executes as expected.

---

## Comment 2

> Username: lcpt  
> Created at: 2021-12-15 08:31:57 UTC  
> Updated at: 2021-12-15 08:32:45 UTC  
> Url: https://github.com/boostorg/ublas/issues/79#issuecomment-994491932  

Hi.  
  
I get the same error when I try to compile: `g++ simple_expressions.cpp` without any other arguments (no NDEBUG, ...).  
  
My compiler details:   
```  
g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0  
Copyright (C) 2019 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
And the errors returned by the compiler:  
```  
In file included from /usr/include/boost/numeric/ublas/tensor/functions.hpp:25,  
                 from /usr/include/boost/numeric/ublas/tensor.hpp:18,  
                 from simple_expressions.cpp:13:  
/usr/include/boost/numeric/ublas/tensor/expression.hpp: In instantiation of ‘boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>::unary_tensor_expression(boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>&&) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::tensor<float>; OP = operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:53&)>]’:  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:50:   required from ‘auto boost::numeric::ublas::detail::make_unary_tensor_expression(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, OP) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::tensor<float>; OP = operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:53&)>]’  
/usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:122:72:   required from ‘auto operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]’  
simple_expressions.cpp:31:14:   required from here  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:53&)>’ has no member named ‘l’  
  148 |    : e(l.e), op(op.l) {}  
      |                 ~~~^  
/usr/include/boost/numeric/ublas/tensor/expression.hpp: In instantiation of ‘boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>::unary_tensor_expression(boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>&&) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::tensor<float>; OP = operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)>]’:  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:50:   required from ‘auto boost::numeric::ublas::detail::make_unary_tensor_expression(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, OP) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::tensor<float>; OP = operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)>]’  
/usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:112:72:   required from ‘auto operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]’  
simple_expressions.cpp:32:10:   required from here  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)>’ has no member named ‘l’  
/usr/include/boost/numeric/ublas/tensor/expression.hpp: In instantiation of ‘boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>::unary_tensor_expression(boost::numeric::ublas::detail::unary_tensor_expression<T, E, OP>&&) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; OP = operator-(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; typename T::const_reference = const float&]::<lambda(const auto:54&)>]’:  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:161:50:   required from ‘auto boost::numeric::ublas::detail::make_unary_tensor_expression(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, OP) [with T = boost::numeric::ublas::tensor<float>; E = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; OP = operator-(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; typename T::const_reference = const float&]::<lambda(const auto:54&)>]’  
/usr/include/boost/numeric/ublas/tensor/operators_arithmetic.hpp:126:72:   required from ‘auto operator-(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; typename T::const_reference = const float&]’  
simple_expressions.cpp:32:14:   required from here  
/usr/include/boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator-(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float>; L = boost::numeric::ublas::detail::unary_tensor_expression<boost::numeric::ublas::tensor<float>, boost::numeric::ublas::tensor<float>, operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<float>; R = boost::numeric::ublas::tensor<float>; typename T::const_reference = const float&]::<lambda(const auto:51&)> >; typename T::const_reference = const float&]::<lambda(const auto:54&)>’ has no member named ‘l’  
```

---

## Comment 3

> Username: lcpt  
> Created at: 2021-12-15 09:57:51 UTC  
> Url: https://github.com/boostorg/ublas/issues/79#issuecomment-994602283  

Never mind, I've solved it using: `g++ -std=gnu++17 simple_expressions.cpp`.
