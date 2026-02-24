# #48 - custom type support [Open]

> Username: ohhmm  
> Created at: 2017-08-18 08:44:35 UTC  
> Updated at: 2017-08-24 08:13:18 UTC  
> Url: https://github.com/boostorg/ublas/issues/48  

matrix template has incomplete support for custom types or it was broken.  
  
can try it with this https://github.com/ohhmm/openmind/blob/master/omnn/extrapolator/Number.h  
  
the error message:  
[  4%] Built target omnn  
[  8%] Built target ct  
[ 12%] Built target TrainedUnit  
[ 16%] Built target MathTool  
[ 20%] Built target Mnist  
[ 24%] Built target opengl_rt_laby  
[ 26%] Building CXX object omnn/extrapolator/CMakeFiles/extrapolator.dir/Extrapolator.cpp.o  
[ 28%] Building CXX object omnn/extrapolator/CMakeFiles/extrapolator.dir/System.cpp.o  
[ 68%] Built target OpenMind  
[ 72%] Built target IdleTimeWorker  
[ 78%] Built target chatbot  
[ 90%] Built target arithm  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/System.cpp:5:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/System.h:11:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:13:  
In file included from /usr/local/include/boost/numeric/ublas/matrix.hpp:20:  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35: error: invalid operands to binary expression ('typename matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> > > >::result_type' (aka 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
        return norm_inf (e1 - e2) < epsilon *  
               ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::equals<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, omnn::extrapolator::Number>' requested here  
        return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON, BOOST_UBLAS_TYPE_CHECK_MIN);  
               ^  
/usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::expression_type_check<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > > >' requested here  
                           detail::expression_type_check (prod (triangular_adaptor<matrix_type, unit_lower> (m),  
                                   ^  
/Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30: note: in instantiation of function template specialization 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::permutation_matrix<unsigned long, boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator<unsigned long> > > >' requested here  
    auto isSingular = ublas::lu_factorize(matrix, pivots);  
                             ^  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.cpp:5:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:13:  
In file included from /usr/local/include/boost/numeric/ublas/matrix.hpp:20:  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35: error: invalid operands to binary expression ('typename matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_unit_lower<unsigned long> >, triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> > > >::result_type' (aka 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
        return norm_inf (e1 - e2) < epsilon *  
               ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::equals<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, omnn::extrapolator::Number>' requested here  
        return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON, BOOST_UBLAS_TYPE_CHECK_MIN);  
               ^  
/usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::expression_type_check<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > > >' requested here  
                           detail::expression_type_check (prod (triangular_adaptor<matrix_type, unit_lower> (m),  
                                   ^  
/Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30: note: in instantiation of function template specialization 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::permutation_matrix<unsigned long, boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator<unsigned long> > > >' requested here  
    auto isSingular = ublas::lu_factorize(matrix, pivots);  
                             ^  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/System.cpp:5:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/System.h:11:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:13:  
In file included from /usr/local/include/boost/numeric/ublas/matrix.hpp:20:  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35: error: invalid operands to binary expression ('typename matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> > > >::result_type' (aka 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
        return norm_inf (e1 - e2) < epsilon *  
               ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::equals<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, omnn::extrapolator::Number>' requested here  
        return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON, BOOST_UBLAS_TYPE_CHECK_MIN);  
               ^  
/usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::expression_type_check<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > > >' requested here  
                           detail::expression_type_check (prod (triangular_adaptor<matrix_type, unit_lower> (m),  
                                   ^  
/Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:87:34: note: in instantiation of function template specialization 'boost::numeric::ublas::lu_factorize<omnn::extrapolator::Extrapolator, boost::numeric::ublas::permutation_matrix<unsigned long, boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator<unsigned long> > > >' requested here  
        auto isSingular = ublas::lu_factorize(mLu, pivots);  
                                 ^  
/usr/local/include/boost/array.hpp:346:10: note: candidate template ignored: could not match 'array' against 'matrix_scalar_unary'  
    bool operator< (const array<T,N>& x, const array<T,N>& y) {  
         ^  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.cpp:5:  
In file included from /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:13:  
In file included from /usr/local/include/boost/numeric/ublas/matrix.hpp:20:  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35: error: invalid operands to binary expression ('typename matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower<unsigned long> >, triangular_adaptor<Extrapolator, basic_upper<unsigned long> >, Number> >, matrix<Number, basic_row_major<unsigned long, long>, unbounded_array<Number, allocator<Number> > >, scalar_minus<Number, Number> > > >::result_type' (aka 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number, omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
        return norm_inf (e1 - e2) < epsilon *  
               ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
/usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::equals<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > >, omnn::extrapolator::Number>' requested here  
        return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON, BOOST_UBLAS_TYPE_CHECK_MIN);  
               ^  
/usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in instantiation of function template specialization 'boost::numeric::ublas::detail::expression_type_check<boost::numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, boost::numeric::ublas::matrix_matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower<unsigned long> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_upper<unsigned long> >, omnn::extrapolator::Number> >, boost::numeric::ublas::matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number, std::__1::allocator<omnn::extrapolator::Number> > > >' requested here  
                           detail::expression_type_check (prod (triangular_adaptor<matrix_type, unit_lower> (m),  
                                   ^  
/Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:87:34: note: in instantiation of function template specialization 'boost::numeric::ublas::lu_factorize<omnn::extrapolator::Extrapolator, boost::numeric::ublas::permutation_matrix<unsigned long, boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator<unsigned long> > > >' requested here  
        auto isSingular = ublas::lu_factorize(mLu, pivots);  
                                 ^  
/usr/local/include/boost/array.hpp:346:10: note: candidate template ignored: could not match 'array' against 'matrix_scalar_unary'  
    bool operator< (const array<T,N>& x, const array<T,N>& y) {  
         ^  
2 errors generated.  
2 errors generated.  
gmake[2]: *** [omnn/extrapolator/CMakeFiles/extrapolator.dir/build.make:87: omnn/extrapolator/CMakeFiles/extrapolator.dir/System.cpp.o] Error 1  
gmake[2]: *** Waiting for unfinished jobs....  
gmake[2]: *** [omnn/extrapolator/CMakeFiles/extrapolator.dir/build.make:63: omnn/extrapolator/CMakeFiles/extrapolator.dir/Extrapolator.cpp.o] Error 1  
gmake[1]: *** [CMakeFiles/Makefile2:222: omnn/extrapolator/CMakeFiles/extrapolator.dir/all] Error 2  
gmake: *** [Makefile:95: all] Error 2

---

## Comment 1

> Username: yimyom  
> Created at: 2017-08-18 10:47:01 UTC  
> Url: https://github.com/boostorg/ublas/issues/48#issuecomment-323322378  

Hi,  
  
I'm not sure I really understand where the problem is.  
I tried your code as follows, and it works well for me.  
But maybe I missed something in the super long error message you sent.  
Let's try to figure out where the problem is and fix the bug in ublas.  
  
Cheers,  
David  
  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
class Number : boost::operators<Number>  
{  
        double d;  
  
public:  
        Number() : d(0.) {}  
        Number(double number) : d(number) {}  
  
        const double& AsDouble() const  
        {  
                return d;  
        }  
  
        Number operator-() const  
        {  
                return -d;  
        }  
  
        const Number &operator+=(const Number &number)  
        {  
                d -= number.d;  
                return *this;  
        }  
  
        const Number &operator-=(const Number &number)  
        {  
                d -= number.d;  
                return *this;  
        }  
  
        const Number &operator*=(const Number &number)  
        {  
                auto res = d * number.d;  
                d = d < 0 && number.d < 0 ? -res : res;  
                return *this;  
        }  
  
        const Number &operator/=(const Number &number)  
        {  
                auto res = d / number.d;  
                d = d < 0 && number.d < 0 ? -res : res;  
                return *this;  
        }  
  
        bool operator==(const Number &number) const  
        {  
                return number.d == d;  
        }  
  
        bool operator<(const Number &number) const  
        {  
                return d < number.d;  
        }  
  
        friend std::ostream& operator<<(std::ostream& out, const Number&  
obj)  
        {  
                out << obj.d;  
                return out;  
        }  
};  
  
int main()  
{  
        using namespace boost::numeric::ublas;  
        using namespace std;  
  
        matrix<Number> m(5,5);  
        m(2,2) = Number(12);  
  
        matrix<Number> n(5,5);  
        n(2,2) = Number(11);  
  
        cout << m << endl;  
        cout << n << endl;  
  
        auto v=m+n;  
  
        cout << v << endl;  
}  
  
  
  
  
On Fri, Aug 18, 2017 at 9:44 AM, Sergei Krivonos <notifications@github.com>  
wrote:  
  
> matrix template has incomplete support for custom types or it was broken.  
>  
> can try it with this https://github.com/ohhmm/openm  
> ind/blob/master/omnn/extrapolator/Number.h  
>  
> the error message:  
> [ 4%] Built target omnn  
> [ 8%] Built target ct  
> [ 12%] Built target TrainedUnit  
> [ 16%] Built target MathTool  
> [ 20%] Built target Mnist  
> [ 24%] Built target opengl_rt_laby  
> [ 26%] Building CXX object omnn/extrapolator/CMakeFiles/e  
> xtrapolator.dir/Extrapolator.cpp.o  
> [ 28%] Building CXX object omnn/extrapolator/CMakeFiles/e  
> xtrapolator.dir/System.cpp.o  
> [ 68%] Built target OpenMind  
> [ 72%] Built target IdleTimeWorker  
> [ 78%] Built target chatbot  
> [ 90%] Built target arithm  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/System.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/System.h:11:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/numer  
> ic/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binar  
> y<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> > > >::result_type' (aka 'matrix_scalar_unary<boost::nu  
> meric::ublas::matrix_binary<boost::numeric::ublas::matrix_ma  
> trix_binary<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric  
> ::ublas::matrix_matrix_binary<boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric::  
> ublas::matrix_matrix_binary<boost::numeric::ublas::triangula  
> r_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization  
> 'boost::numeric::ublas::detail::expression_type_check<boost:  
> :numeric::ublas::matrix_matrix_binary<boost::numeric::ublas:  
> :triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::  
> matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned  
> long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::permutation_matrix<unsigned long,  
> boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator  
> > > >' requested here  
> auto isSingular = ublas::lu_factorize(matrix, pivots);  
> ^  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/numer  
> ic/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binar  
> y<triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> > > >::result_type' (aka 'matrix_scalar_unary<boost::nu  
> meric::ublas::matrix_binary<boost::numeric::ublas::matrix_ma  
> trix_binary<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric  
> ::ublas::matrix_matrix_binary<boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric::  
> ublas::matrix_matrix_binary<boost::numeric::ublas::triangula  
> r_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization  
> 'boost::numeric::ublas::detail::expression_type_check<boost:  
> :numeric::ublas::matrix_matrix_binary<boost::numeric::ublas:  
> :triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<boost:  
> :numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::  
> matrix<omnn::extrapolator::Number, boost::numeric::ublas::basic_row_major<unsigned  
> long, long>, boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::permutation_matrix<unsigned long,  
> boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator  
> > > >' requested here  
> auto isSingular = ublas::lu_factorize(matrix, pivots);  
> ^  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/System.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/System.h:11:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/numer  
> ic/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binar  
> y<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<  
> matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> > > >::result_type' (aka  
> 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<bo  
> ost::numeric::ublas::matrix_matrix_binary<boost::numeric::ub  
> las::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric  
> ::ublas::matrix_matrix_binary<boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric::  
> ublas::matrix_matrix_binary<boost::numeric::ublas::triangula  
> r_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower  
> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization  
> 'boost::numeric::ublas::detail::expression_type_check<boost:  
> :numeric::ublas::matrix_matrix_binary<boost::numeric::ublas:  
> :triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:87:34:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::permutation_matrix<unsigned long,  
> boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator  
> > > >' requested here  
> auto isSingular = ublas::lu_factorize(mLu, pivots);  
> ^  
> /usr/local/include/boost/array.hpp:346:10: note: candidate template  
> ignored: could not match 'array' against 'matrix_scalar_unary'  
> bool operator< (const array<T,N>& x, const array<T,N>& y) {  
> ^  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects  
> /openmind/omnn/extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/numer  
> ic/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_binar  
> y<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<  
> matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> > > >::result_type' (aka  
> 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<bo  
> ost::numeric::ublas::matrix_matrix_binary<boost::numeric::ub  
> las::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::numeric  
> ::ublas::matrix_matrix_binary<boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric::  
> ublas::matrix_matrix_binary<boost::numeric::ublas::triangula  
> r_adaptor<omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower  
> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization  
> 'boost::numeric::ublas::detail::expression_type_check<boost:  
> :numeric::ublas::matrix_matrix_binary<boost::numeric::ublas:  
> :triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::triangu  
> lar_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:87:34:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::permutation_matrix<unsigned long,  
> boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator  
> > > >' requested here  
> auto isSingular = ublas::lu_factorize(mLu, pivots);  
> ^  
> /usr/local/include/boost/array.hpp:346:10: note: candidate template  
> ignored: could not match 'array' against 'matrix_scalar_unary'  
> bool operator< (const array<T,N>& x, const array<T,N>& y) {  
> ^  
> 2 errors generated.  
> 2 errors generated.  
> gmake[2]: *** [omnn/extrapolator/CMakeFiles/extrapolator.dir/build.make:87:  
> omnn/extrapolator/CMakeFiles/extrapolator.dir/System.cpp.o] Error 1  
> gmake[2]: *** Waiting for unfinished jobs....  
> gmake[2]: *** [omnn/extrapolator/CMakeFiles/extrapolator.dir/build.make:63:  
> omnn/extrapolator/CMakeFiles/extrapolator.dir/Extrapolator.cpp.o] Error 1  
> gmake[1]: *** [CMakeFiles/Makefile2:222: omnn/extrapolator/CMakeFiles/extrapolator.dir/all]  
> Error 2  
> gmake: *** [Makefile:95: all] Error 2  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/issues/48>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACDlcAX64Fh1gD9DYuFHXf5XcaxMXaw9ks5sZU70gaJpZM4O7RHI>  
> .  
>  
  
  
On Fri, Aug 18, 2017 at 9:44 AM, Sergei Krivonos <notifications@github.com>  
wrote:  
  
> matrix template has incomplete support for custom types or it was broken.  
>  
> can try it with this https://github.com/ohhmm/openmind/blob/master/omnn/  
> extrapolator/Number.h  
>  
> the error message:  
> [ 4%] Built target omnn  
> [ 8%] Built target ct  
> [ 12%] Built target TrainedUnit  
> [ 16%] Built target MathTool  
> [ 20%] Built target Mnist  
> [ 24%] Built target opengl_rt_laby  
> [ 26%] Building CXX object omnn/extrapolator/CMakeFiles/  
> extrapolator.dir/Extrapolator.cpp.o  
> [ 28%] Building CXX object omnn/extrapolator/CMakeFiles/  
> extrapolator.dir/System.cpp.o  
> [ 68%] Built target OpenMind  
> [ 72%] Built target IdleTimeWorker  
> [ 78%] Built target chatbot  
> [ 90%] Built target arithm  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/System.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/System.h:11:  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/  
> numeric/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_  
> binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> > > >::result_type' (aka 'matrix_scalar_unary<boost::  
> numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_  
> matrix_binary<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::  
> numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric:  
> :ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization 'boost::numeric::ublas::  
> detail::expression_type_check<boost::numeric::ublas::matrix_  
> matrix_binary<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >, boost::numeric::ublas::permutation_matrix<unsigned  
> long, boost::numeric::ublas::unbounded_array<unsigned long,  
> std::__1::allocator > > >' requested here  
> auto isSingular = ublas::lu_factorize(matrix, pivots);  
> ^  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/Extrapolator.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/  
> numeric/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_  
> binary<triangular_adaptor<matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> >, matrix_norm_inf<matrix_binary<matrix_matrix_binary<  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_unit_lower >,  
> triangular_adaptor<matrix<Number, basic_row_major<unsigned long, long>,  
> unbounded_array<Number, allocator > >, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_unit_lower >, triangular_adaptor<matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, basic_upper >, Number> >, matrix<Number, basic_row_major<unsigned long,  
> long>, unbounded_array<Number, allocator > >, scalar_minus<Number, Number>  
> > > >::result_type' (aka 'matrix_scalar_unary<boost::  
> numeric::ublas::matrix_binary<boost::numeric::ublas::matrix_  
> matrix_binary<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::  
> numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric:  
> :ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization 'boost::numeric::ublas::  
> detail::expression_type_check<boost::numeric::ublas::matrix_  
> matrix_binary<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:26:30:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >, boost::numeric::ublas::permutation_matrix<unsigned  
> long, boost::numeric::ublas::unbounded_array<unsigned long,  
> std::__1::allocator > > >' requested here  
> auto isSingular = ublas::lu_factorize(matrix, pivots);  
> ^  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/System.cpp:5:  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/System.h:11:  
> In file included from /Users/sergejkrivonos/Projects/openmind/omnn/  
> extrapolator/Extrapolator.h:13:  
> In file included from /usr/local/include/boost/  
> numeric/ublas/matrix.hpp:20:  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:33:35:  
> error: invalid operands to binary expression ('typename  
> matrix_scalar_unary_traits<matrix_binary<matrix_matrix_  
> binary<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> >, matrix_norm_inf<matrix_binary<  
> matrix_matrix_binary<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >,  
> matrix_matrix_prod<triangular_adaptor<Extrapolator, basic_unit_lower >,  
> triangular_adaptor<Extrapolator, basic_upper >, Number> >, matrix<Number,  
> basic_row_major<unsigned long, long>, unbounded_array<Number, allocator >  
> >, scalar_minus<Number, Number> > > >::result_type' (aka  
> 'matrix_scalar_unary<boost::numeric::ublas::matrix_binary<  
> boost::numeric::ublas::matrix_matrix_binary<boost::numeric::  
> ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> >, boost::numeric::ublas::matrix_  
> norm_inf<boost::numeric::ublas::matrix_binary<boost::  
> numeric::ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> boost::numeric::ublas::scalar_minus<omnn::extrapolator::Number,  
> omnn::extrapolator::Number> > > >') and 'omnn::extrapolator::Number')  
> return norm_inf (e1 - e2) < epsilon *  
> ~~~~~~~~~~~~~~~~~~ ^ ~~~~~~~~~  
> /usr/local/include/boost/numeric/ublas/detail/matrix_assign.hpp:42:16:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::detail::equals<boost::numeric:  
> :ublas::matrix_matrix_binary<boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > >,  
> omnn::extrapolator::Number>' requested here  
> return equals (e1, e2, BOOST_UBLAS_TYPE_CHECK_EPSILON,  
> BOOST_UBLAS_TYPE_CHECK_MIN);  
> ^  
> /usr/local/include/boost/numeric/ublas/lu.hpp:166:36: note: in  
> instantiation of function template specialization 'boost::numeric::ublas::  
> detail::expression_type_check<boost::numeric::ublas::matrix_  
> matrix_binary<boost::numeric::ublas::triangular_adaptor<  
> omnn::extrapolator::Extrapolator, boost::numeric::ublas::basic_unit_lower  
> >, boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, boost::numeric::ublas::matrix_  
> matrix_prod<boost::numeric::ublas::triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_unit_lower >, boost::numeric::ublas::  
> triangular_adaptor<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::basic_upper >, omnn::extrapolator::Number> >,  
> boost::numeric::ublas::matrix<omnn::extrapolator::Number,  
> boost::numeric::ublas::basic_row_major<unsigned long, long>,  
> boost::numeric::ublas::unbounded_array<omnn::extrapolator::Number,  
> std::__1::allocatoromnn::extrapolator::Number > > >' requested here  
> detail::expression_type_check (prod (triangular_adaptor<matrix_type,  
> unit_lower> (m),  
> ^  
> /Users/sergejkrivonos/Projects/openmind/omnn/extrapolator/Extrapolator.h:87:34:  
> note: in instantiation of function template specialization  
> 'boost::numeric::ublas::lu_factorize<omnn::extrapolator::Extrapolator,  
> boost::numeric::ublas::permutation_matrix<unsigned long,  
> boost::numeric::ublas::unbounded_array<unsigned long, std::__1::allocator  
> > > >' requested here  
> auto isSingular = ublas::lu_factorize(mLu, pivots);  
> ^  
> /usr/local/include/boost/array.hpp:346:10: note: candidate template  
> ignored: could not match 'array' against 'matrix_scalar_unary'  
> bool operator< (const array<T,N>& x, const array<T,N>& y) {  
> ^  
> In file included fr

---

## Comment 2

> Username: ohhmm  
> Created at: 2017-08-18 13:34:13 UTC  
> Url: https://github.com/boostorg/ublas/issues/48#issuecomment-323354966  

```  
#include <cmath>  
#include <iostream>  
#include <boost/operators.hpp>  
  
class Number : boost::operators<Number>  
{  
    double d;  
      
    public:  
    Number() : d(0.) {}  
    Number(double number) : d(number) {}  
      
    const double& AsDouble() const  
    {  
        return d;  
    }  
      
    Number operator-() const  
    {  
        return -d;  
    }  
      
    const Number &operator+=(const Number &number)  
    {  
        d -= number.d;  
        return *this;  
    }  
      
    const Number &operator-=(const Number &number)  
    {  
        d -= number.d;  
        return *this;  
    }  
      
    const Number &operator*=(const Number &number)  
    {  
        auto res = d * number.d;  
        d = d < 0 && number.d < 0 ? -res : res;  
        return *this;  
    }  
      
    const Number &operator/=(const Number &number)  
    {  
        auto res = d / number.d;  
        d = d < 0 && number.d < 0 ? -res : res;  
        return *this;  
    }  
      
    bool operator==(const Number &number) const  
    {  
        return number.d == d;  
    }  
      
    bool operator<(const Number &number) const  
    {  
        return d < number.d;  
    }  
      
    friend std::ostream& operator<<(std::ostream& out, const Number&  
                                    obj)  
    {  
        out << obj.d;  
        return out;  
    }  
};  
  
namespace std {  
    Number abs(const Number& n)  
    {  
        return Number(abs(n.AsDouble()));  
    }  
    Number sqrt(const Number& n)  
    {  
        if(n.AsDouble()<0) return -sqrt(-n.AsDouble());  
        else return sqrt(n.AsDouble());  
    }  
}  
  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
#include <boost/numeric/ublas/lu.hpp>  
  
  
namespace ublas = boost::numeric::ublas;  
  
Number det_fast(ublas::matrix<Number> matrix)  
{  
    ublas::permutation_matrix<std::size_t> pivots(matrix.size1());  
      
    auto isSingular = ublas::lu_factorize(matrix, pivots);  
    if (isSingular)  
    return static_cast<Number>(0);  
      
    Number det = static_cast<Number>(1);  
    for (std::size_t i = 0; i < pivots.size(); ++i)  
    {  
        if (pivots(i) != i)  
        det *= static_cast<Number>(-1);  
          
        det *= matrix(i, i);  
    }  
      
    return det;  
}  
  
int main()  
{  
    using namespace boost::numeric::ublas;  
    using namespace std;  
      
    matrix<Number> m(5,5);  
    m(2,2) = Number(12);  
      
    matrix<Number> n(5,5);  
    n(2,2) = Number(11);  
      
    cout << m << endl;  
    cout << n << endl;  
      
    auto v=m+n;  
      
    cout << v << endl;  
}  
```

---

## Comment 3

> Username: ohhmm  
> Created at: 2017-08-21 17:03:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/48#issuecomment-323799652  

det_fast here is to call lu_factorize that cause the issue. You may want to consider using this det_fast implementation to close another ticket https://github.com/boostorg/ublas/issues/38

---

## Comment 4

> Username: ohhmm  
> Created at: 2017-08-24 08:12:45 UTC  
> Updated at: 2017-08-24 08:13:18 UTC  
> Url: https://github.com/boostorg/ublas/issues/48#issuecomment-324565678  

Here is the workaround for this issue:  
  
```  
#include <cmath>  
#include <iostream>  
#include <boost/operators.hpp>  
  
class Number;  
  
class ops  
    : public boost::operators<Number>  
{  
    template<class T>  
    friend bool operator<=(const T& x, const Number& y) { return !static_cast<bool>(x > y); }  
    template<class T> friend bool operator>=(const T& x, const Number& y) { return !static_cast<bool>(x < y); }  
    template<class T> friend bool operator>(const Number& x, const T& y)  { return y < x; }  
    template<class T> friend bool operator<(const Number& x, const T& y)  { return y > x; }  
    template<class T> friend bool operator<=(const Number& x, const T& y) { return !static_cast<bool>(y < x); }  
    template<class T> friend bool operator>=(const Number& x, const T& y) { return !static_cast<bool>(y > x); }  
      
};  
  
class Number : public ops  
{  
    double d;  
      
    public:  
    Number() : d(0.) {}  
    Number(double number) : d(number) {}  
      
    const double& AsDouble() const  
    {  
        return d;  
    }  
      
    Number operator-() const  
    {  
        return -d;  
    }  
      
    const Number &operator+=(const Number &number)  
    {  
        d -= number.d;  
        return *this;  
    }  
      
    const Number &operator-=(const Number &number)  
    {  
        d -= number.d;  
        return *this;  
    }  
      
    const Number &operator*=(const Number &number)  
    {  
        auto res = d * number.d;  
        d = d < 0 && number.d < 0 ? -res : res;  
        return *this;  
    }  
      
    const Number &operator/=(const Number &number)  
    {  
        auto res = d / number.d;  
        d = d < 0 && number.d < 0 ? -res : res;  
        return *this;  
    }  
      
    bool operator==(const Number &number) const  
    {  
        return number.d == d;  
    }  
      
    bool operator<(const Number &number) const  
    {  
        return d < number.d;  
    }  
      
    friend std::ostream& operator<<(std::ostream& out, const Number&  
                                    obj)  
    {  
        out << obj.d;  
        return out;  
    }  
};  
  
namespace std {  
    Number abs(const Number& n)  
    {  
        return Number(abs(n.AsDouble()));  
    }  
    Number sqrt(const Number& n)  
    {  
        if(n.AsDouble()<0) return -sqrt(-n.AsDouble());  
        else return sqrt(n.AsDouble());  
    }  
}  
  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
#include <boost/numeric/ublas/lu.hpp>  
  
  
namespace ublas = boost::numeric::ublas;  
  
Number det_fast(ublas::matrix<Number> matrix)  
{  
    ublas::permutation_matrix<std::size_t> pivots(matrix.size1());  
      
    auto isSingular = ublas::lu_factorize(matrix, pivots);  
    if (isSingular)  
    return static_cast<Number>(0);  
      
    Number det = static_cast<Number>(1);  
    for (std::size_t i = 0; i < pivots.size(); ++i)  
    {  
        if (pivots(i) != i)  
        det *= static_cast<Number>(-1);  
          
        det *= matrix(i, i);  
    }  
      
    return det;  
}  
  
int main()  
{  
    using namespace boost::numeric::ublas;  
    using namespace std;  
      
    matrix<Number> m(5,5);  
    m(2,2) = Number(12);  
      
    matrix<Number> n(5,5);  
    n(2,2) = Number(11);  
      
    cout << m << endl;  
    cout << n << endl;  
      
    auto v=m+n;  
      
    cout << v << endl;  
}  
```  
  
Please, let me know your thoughts about how and where it should be fixed.
