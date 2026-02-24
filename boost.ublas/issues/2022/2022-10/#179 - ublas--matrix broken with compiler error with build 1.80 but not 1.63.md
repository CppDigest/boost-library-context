# #179 - ublas::matrix broken with compiler error with build 1.80 but not 1.63 [Open]

> Username: carljwhite3  
> Created at: 2022-10-25 22:53:44 UTC  
> Updated at: 2024-03-23 16:13:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/179  

Was trying to compile the following code and received the following error information.  
  
## Code:  
  
```  
#include <boost/numeric/ublas/matrix.hpp>  
  
int main()  
{  
    boost::numeric::ublas::matrix<double> u;  
  
    u = u / norm_frobenius(u);  // u /= norm_frobenius(u) works  
  
    return 0;  
}  
```  
  
## Error:  
```  
In file included from /opt/boost/boost/typeof/typeof.hpp:216,  
                 from /opt/boost/boost/numeric/ublas/traits.hpp:29,  
                 from /opt/boost/boost/numeric/ublas/storage.hpp:27,  
                 from /opt/boost/boost/numeric/ublas/vector.hpp:21,  
                 from /opt/boost/boost/numeric/ublas/matrix.hpp:18,  
                 from example.cpp:1:  
/opt/boost/boost/numeric/ublas/traits.hpp: In instantiation of ‘struct boost::numeric::ublas::promote_traits<double, boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > > >’:  
/opt/boost/boost/numeric/ublas/functional.hpp:126:63:   required from ‘struct boost::numeric::ublas::scalar_binary_functor<double, boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > > >’  
/opt/boost/boost/numeric/ublas/functional.hpp:166:12:   required from ‘struct boost::numeric::ublas::scalar_divides<double, boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > > >’  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:3491:34:   required from ‘class boost::numeric::ublas::matrix_binary_scalar2<boost::numeric::ublas::matrix<double>, const boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >, boost::numeric::ublas::scalar_divides<double, boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > > > >’  
example.cpp:7:29:   required from here  
/opt/boost/boost/numeric/ublas/traits.hpp:154:40: error: no matching function for call to ‘boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >::matrix_scalar_unary()’  
  154 |         typedef BOOST_TYPEOF_TPL(X() + Y()) promote_type;  
      |                                        ^~~  
/opt/boost/boost/typeof/decltype.hpp:17:69: note: in definition of macro ‘BOOST_TYPEOF’  
   17 | #define BOOST_TYPEOF(expr) boost::type_of::remove_cv_ref_t<decltype(expr)>  
      |                                                                     ^~~~  
/opt/boost/boost/numeric/ublas/traits.hpp:154:17: note: in expansion of macro ‘BOOST_TYPEOF_TPL’  
  154 |         typedef BOOST_TYPEOF_TPL(X() + Y()) promote_type;  
      |                 ^~~~~~~~~~~~~~~~  
In file included from /opt/boost/boost/numeric/ublas/matrix.hpp:19,  
                 from example.cpp:1:  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5637:11: note: candidate: ‘boost::numeric::ublas::matrix_scalar_unary<E, F>::matrix_scalar_unary(const expression_type&) [with E = boost::numeric::ublas::matrix<double>; F = boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> >; boost::numeric::ublas::matrix_scalar_unary<E, F>::expression_type = boost::numeric::ublas::matrix<double>]’  
 5637 |  explicit matrix_scalar_unary (const expression_type &e):  
      |           ^~~~~~~~~~~~~~~~~~~  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5637:11: note:   candidate expects 1 argument, 0 provided  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5627:7: note: candidate: ‘constexpr boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >::matrix_scalar_unary(const boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >&)’  
 5627 | class matrix_scalar_unary:  
      |       ^~~~~~~~~~~~~~~~~~~  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5627:7: note:   candidate expects 1 argument, 0 provided  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5627:7: note: candidate: ‘constexpr boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >::matrix_scalar_unary(boost::numeric::ublas::matrix_scalar_unary<boost::numeric::ublas::matrix<double>, boost::numeric::ublas::matrix_norm_frobenius<boost::numeric::ublas::matrix<double> > >&&)’  
/opt/boost/boost/numeric/ublas/matrix_expression.hpp:5627:7: note:   candidate expects 1 argument, 0 provided  
```

---

## Comment 1

> Username: yihuajack  
> Created at: 2024-01-18 14:37:54 UTC  
> Url: https://github.com/boostorg/ublas/issues/179#issuecomment-1898603296  

This code also fails for boost 1.83.0 on MSVC

---

## Comment 2

> Username: carljwhite3  
> Created at: 2024-03-23 16:13:32 UTC  
> Url: https://github.com/boostorg/ublas/issues/179#issuecomment-2016536374  

This code also fails boost 1.84.0, gnu g++
