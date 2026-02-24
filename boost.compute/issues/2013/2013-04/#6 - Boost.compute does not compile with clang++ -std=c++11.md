# #6 - Boost.compute does not compile with clang++ -std=c++11 [Closed]

> Username: ddemidov  
> Created at: 2013-04-09 06:58:02 UTC  
> Updated at: 2013-04-11 03:41:12 UTC  
> Closed at: 2013-04-11 03:41:12 UTC  
> Url: https://github.com/boostorg/compute/issues/6  

Hi,  
  
The compilation succeeds with default settings, but fails if c++11 standard is used:  
  
```  
$ cd compute/build  
$ CC=clang CXX=clang++ cmake -DCMAKE_CXX_FLAGS=-std=c++11 -DBOOST_COMPUTE_BUILD_BENCHMARKS=ON -DBOOST_COMPUTE_BUILD_EXAMPLES=ON -DBOOST_COMPUTE_BUILD_TESTS=ON ..  
-- The C compiler identification is Clang 3.2.0  
-- The CXX compiler identification is Clang 3.2.0  
-- Check for working C compiler: /usr/bin/clang  
-- Check for working C compiler: /usr/bin/clang -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/clang++  
-- Check for working CXX compiler: /usr/bin/clang++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Found OpenCL: /usr/lib64/libOpenCL.so    
-- Boost version: 1.52.0  
-- Boost version: 1.52.0  
-- Found the following Boost libraries:  
--   unit_test_framework  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/demidov/work/opencl/compute/build  
  
$ make   
Scanning dependencies of target copy_data  
[  1%] Building CXX object example/CMakeFiles/copy_data.dir/copy_data.cpp.o  
In file included from /home/demidov/work/opencl/compute/example/copy_data.cpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm.hpp:16:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm/adjacent_find.hpp:17:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm/find.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/lambda.hpp:14:  
/home/demidov/work/opencl/compute/include/boost/compute/lambda/context.hpp:360:58: error: no type named 'type' in  
      'boost::result_of<boost::compute::lambda::expression<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,  
      boost::proto::argsns_::term<boost::compute::lambda::placeholder<0> >, 0> > ()>'  
        typename ::boost::result_of<expression<Expr>()>::type  
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
/home/demidov/work/opencl/compute/include/boost/compute/lambda/placeholders.hpp:27:58: note: in instantiation of template class  
      'boost::compute::lambda::expression<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::compute::lambda::placeholder<0> >, 0>  
      >' requested here  
expression<proto::terminal<placeholder<0> >::type> const _1;  
                                                         ^  
In file included from /home/demidov/work/opencl/compute/example/copy_data.cpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm.hpp:16:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm/adjacent_find.hpp:17:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm/find.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/lambda.hpp:14:  
/home/demidov/work/opencl/compute/include/boost/compute/lambda/context.hpp:360:58: error: no type named 'type' in  
      'boost::result_of<boost::compute::lambda::expression<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,  
      boost::proto::argsns_::term<boost::compute::lambda::placeholder<1> >, 0> > ()>'  
        typename ::boost::result_of<expression<Expr>()>::type  
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
/home/demidov/work/opencl/compute/include/boost/compute/lambda/placeholders.hpp:28:58: note: in instantiation of template class  
      'boost::compute::lambda::expression<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::compute::lambda::placeholder<1> >, 0>  
      >' requested here  
expression<proto::terminal<placeholder<1> >::type> const _2;  
                                                         ^  
2 errors generated.  
make[2]: *** [example/CMakeFiles/copy_data.dir/copy_data.cpp.o] Error 1  
make[1]: *** [example/CMakeFiles/copy_data.dir/all] Error 2  
make: *** [all] Error 2  
  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-04-09 22:39:53 UTC  
> Url: https://github.com/boostorg/compute/issues/6#issuecomment-16144907  

I suspect this is due to the new `decltype` based `result_of` implementation. Can you try changing the calls from `::boost::result_of` to `::boost::tr1_result_of` and see if that solves the issue?

---

## Comment 2

> Username: ddemidov  
> Created at: 2013-04-10 03:19:08 UTC  
> Url: https://github.com/boostorg/compute/issues/6#issuecomment-16153228  

With the following patch:  
  
```  
diff --git a/include/boost/compute/lambda/context.hpp b/include/boost/compute/lambda/context.hpp  
index 0ba43c4..2cde9cf 100644  
--- a/include/boost/compute/lambda/context.hpp  
+++ b/include/boost/compute/lambda/context.hpp  
@@ -357,11 +357,11 @@ struct expression : proto::extends<Expr, expression<Expr>, domain>  
     };  
  
     ::boost::compute::detail::meta_kernel_variable<  
-        typename ::boost::result_of<expression<Expr>()>::type  
+        typename ::boost::tr1_result_of<expression<Expr>()>::type  
     >  
     operator()() const  
     {  
-        typedef typename ::boost::result_of<expression<Expr>()>::type result_type;  
+        typedef typename ::boost::tr1_result_of<expression<Expr>()>::type result_type;  
  
         context ctx;  
         proto::eval(*this, ctx);  
```  
  
the `copy_data` example compiles, but `find_longest_vector` fails with:  
  
```  
[  1%] Built target copy_data  
[  2%] Building CXX object example/CMakeFiles/find_longest_vector.dir/find_longest_vector.cpp.o  
In file included from /home/demidov/work/opencl/compute/example/find_longest_vector.cpp:15:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/algorithm/accumulate.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/functional.hpp:18:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/functional/geometry.hpp:14:  
In file included from /home/demidov/work/opencl/compute/include/boost/compute/type_traits.hpp:18:  
/home/demidov/work/opencl/compute/include/boost/compute/type_traits/type_name.hpp:79:20: error: implicit instantiation of undefined template  
      'boost::compute::detail::type_name_trait<boost::compute::detail::invoked_unary_function<boost::compute::float4_, float> >'  
    return detail::type_name_trait<T>::value();  
                   ^  
/home/demidov/work/opencl/compute/include/boost/compute/detail/meta_kernel.hpp:436:19: note: in instantiation of function template specialization  
      'boost::compute::type_name<boost::compute::detail::invoked_unary_function<boost::compute::float4_, float> >' requested here  
        stream << type_name<Type>();  
                  ^  
/home/demidov/work/opencl/compute/include/boost/compute/detail/meta_kernel.hpp:452:16: note: in instantiation of function template specialization  
      'boost::compute::detail::meta_kernel::type<boost::compute::detail::invoked_unary_function<boost::compute::float4_, float> >' requested here  
        return type<T>() + " " + name;  
               ^  
/home/demidov/work/opencl/compute/include/boost/compute/algorithm/detail/serial_find_extrema.hpp:39:11: note: in instantiation of function template specialization  
      'boost::compute::detail::meta_kernel::decl<boost::compute::detail::invoked_unary_function<boost::compute::float4_, float> >' requested here  
        k.decl<value_type>("value") << " = " << first[k.expr<uint_>("0")] << ";\n" <<  
          ^  
/home/demidov/work/opencl/compute/include/boost/compute/algorithm/detail/find_extrema.hpp:37:16: note: in instantiation of function template specialization  
      'boost::compute::detail::serial_find_extrema<boost::compute::transform_iterator<boost::compute::buffer_iterator<boost::compute::float4_>,  
      boost::compute::length<boost::compute::float4_> > >' requested here  
        return serial_find_extrema(first, last, sign, queue);  
               ^  
/home/demidov/work/opencl/compute/include/boost/compute/algorithm/max_element.hpp:26:12: note: in instantiation of function template specialization  
      'boost::compute::detail::find_extrema<boost::compute::transform_iterator<boost::compute::buffer_iterator<boost::compute::float4_>,  
      boost::compute::length<boost::compute::float4_> > >' requested here  
    return detail::find_extrema(first, last, '>', queue);  
           ^  
/home/demidov/work/opencl/compute/include/boost/compute/algorithm/max_element.hpp:35:12: note: in instantiation of function template specialization  
      'boost::compute::max_element<boost::compute::transform_iterator<boost::compute::buffer_iterator<boost::compute::float4_>, boost::compute::length<boost::compute::float4_> > >'  
      requested here  
    return ::boost::compute::max_element(first, last, queue);  
           ^  
/home/demidov/work/opencl/compute/example/find_longest_vector.cpp:36:9: note: in instantiation of function template specialization  
      'boost::compute::max_element<boost::compute::transform_iterator<boost::compute::buffer_iterator<boost::compute::float4_>, boost::compute::length<boost::compute::float4_> > >'  
      requested here  
        boost::compute::max_element(  
        ^  
/home/demidov/work/opencl/compute/include/boost/compute/type_traits/type_name.hpp:24:8: note: template is declared here  
struct type_name_trait;  
       ^  
1 error generated.  
make[2]: *** [example/CMakeFiles/find_longest_vector.dir/find_longest_vector.cpp.o] Error 1  
make[1]: *** [example/CMakeFiles/find_longest_vector.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-04-10 21:44:32 UTC  
> Url: https://github.com/boostorg/compute/issues/6#issuecomment-16204293  

I was able to reproduce this on my machine with Clang 3.2 and Boost 1.53. I'll get a fix pushed tonight.

---

## Comment 4

> Username: kylelutz  
> Created at: 2013-04-11 00:33:52 UTC  
> Url: https://github.com/boostorg/compute/issues/6#issuecomment-16210905  

This commit: 6fdffd8a2b16cd71fb20dd210bb39950429198f8 should fix the issue. Let me know if it works for you.

---

## Comment 5

> Username: ddemidov  
> Created at: 2013-04-11 03:41:11 UTC  
> Url: https://github.com/boostorg/compute/issues/6#issuecomment-16215460  

Thanks, this does fix the issue.
