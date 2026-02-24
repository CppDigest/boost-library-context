# #99 - UB instead of compile error when using any_range with Reference = T const & [Open]

> Username: allopislozano  
> Created at: 2019-12-13 09:41:28 UTC  
> Updated at: 2019-12-13 09:41:28 UTC  
> Url: https://github.com/boostorg/range/issues/99  

https://godbolt.org/z/b_YJ_L  
  
This issue was already reported in https://svn.boost.org/trac10/ticket/10493   
  
It looks like the issue is still in 1.71.   
I would expect that range_t_2 would fail to compile. The designer of any_iterator_range wrote an article about type erasure https://www.artima.com/cppsource/type_erasure.html where he mentions that:  
  
> However, if we try to assign to our number iterator a transform iterator which, upon dereferencing, multiplies by 100.0, we run into trouble.  
>   
> number_it = boost::make_transform_iterator(  
>   number_vector.begin(),  
>   boost::bind(std::multiplies<double>(), _1, 100)  
> );  
>   
> My any_iterator's assignment operator is not enabled for this assignment, and you will get an error message such as:  
> binary '=': no operator found which takes a right-hand operand of type...  
  
I think this is what should happen in the case of range_t_2 in my example, so that you can use const & as reference_type and you know that if someone tries to assign a transform_iterator or any other iterator that returns temporaries he will get a compiler error.
