# #10 - Some examples fail to build with visual studio 2010 [Closed]

> Username: d-meiser  
> Created at: 2013-04-30 14:05:51 UTC  
> Updated at: 2013-05-03 22:39:46 UTC  
> Closed at: 2013-05-03 22:39:46 UTC  
> Url: https://github.com/boostorg/compute/issues/10  

Hey Kyle,  
  
I started to look into building the compute library under windows. Some of the examples don't build. I assume that they should? I haven't looked too closely into this yet and wanted to get an idea of whether something has been done under windows yet. Have you tried building under windows or do you know if other people have? I can't build the tests yet because of a cmake problem that I haven't been able to solve. I hope to sort that out soon.  
  
Cheers,  
Dominic

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-05-01 01:24:21 UTC  
> Url: https://github.com/boostorg/compute/issues/10#issuecomment-17264095  

Ideally they should all work. It's been a while since I tried building it on Windows so some bugs may have crept in. What kind of errors are you getting?

---

## Comment 2

> Username: d-meiser  
> Created at: 2013-05-02 03:10:05 UTC  
> Url: https://github.com/boostorg/compute/issues/10#issuecomment-17318425  

It seems like the build failures are all due to line 230 in binary_transform_iterator.hpp. I get error messages similar to the following:  
  
2>C:\Users\Dominic\Documents\GitHub\compute\include\boost/compute/iterator/detail/binary_transform_iterator.hpp(230): error C2678: binary '==' : no operator found which takes a left-hand operand of type 'const boost::compute::plus<T>' (or there is no acceptable conversion)  
2>          with  
3>C:\Users\Dominic\Documents\GitHub\compute\include\boost/compute/iterator/detail/binary_transform_iterator.hpp(230): error C2678: binary '==' : no operator found which takes a left-hand operand of type 'const boost::compute::less_equal<T>' (or there is no acceptable conversion)  
3>          with  
3>          [  
3>              T=value_type  
3>          ]  
3>          C:\Boost\include\boost-1_53\boost/function/function_base.hpp(808): could be 'bool boost::operator ==<BinaryFunction>(const boost::function_base &,Functor)'  
3>          with  
3>          [  
3>              BinaryFunction=boost::compute::less_equal<value_type>,  
3>              Functor=boost::compute::less_equal<value_type>  
3>          ]  
3>          C:\Boost\include\boost-1_53\boost/function/function_base.hpp(817): or       'bool boost::operator ==<BinaryFunction>(Functor,const boost::function_base &)'  
3>          with  
3>          [  
3>              BinaryFunction=boost::compute::less_equal<value_type>,  
3>              Functor=boost::compute::less_equal<value_type>  
3>          ]  
3>          C:\Boost\include\boost-1_53\boost/function/function_base.hpp(746): or       'bool boost::operator ==(const boost::function_base &,boost::detail::function::useless_clear_type *)'  
3>          C:\Boost\include\boost-1_53\boost/function/function_base.hpp(758): or       'bool boost::operator ==(boost::detail::function::useless_clear_type *,const boost::function_base &)'  
3>          C:\Boost\include\boost-1_53\boost/blank.hpp(58): or       'bool boost::operator ==(const boost::blank &,const boost::blank &)'  
3>          while trying to match the argument list '(const boost::compute::less_equal<T>, const boost::compute::less_equal<T>)'  
3>          with  
3>          [  
3>              T=value_type  
3>          ]  
3>          C:\Users\Dominic\Documents\GitHub\compute\include\boost/compute/iterator/detail/binary_transform_iterator.hpp(227) : while compiling class template member function 'bool boost::compute::detail::binary_transform_iterator<InputIterator1,InputIterator2,BinaryFunction>::equal(const boost::compute::detail::binary_transform_iterator<InputIterator1,InputIterator2,BinaryFunction> &) const'  
3>          with  
3>          [  
3>              InputIterator1=boost::compute::buffer_iterator<unsigned int>,  
3>              InputIterator2=boost::compute::buffer_iterator<unsigned int>,  
3>              BinaryFunction=boost::compute::less_equal<value_type>  
3>          ]  
3>          C:\Users\Dominic\Documents\GitHub\compute\include\boost/compute/algorithm/is_sorted.hpp(35) : see reference to class template instantiation 'boost::compute::detail::binary_transform_iterator<InputIterator1,InputIterator2,BinaryFunction>' being compiled  
3>          with  
3>          [  
3>              InputIterator1=boost::compute::buffer_iterator<unsigned int>,  
3>              InputIterator2=boost::compute::buffer_iterator<unsigned int>,  
3>              BinaryFunction=boost::compute::less_equal<value_type>  
3>          ]  
3>          ......\Documents\GitHub\compute\perf\perf_sort_uint.cpp(60) : see reference to function template instantiation 'bool boost::compute::is_sortedboost::compute::buffer_iterator<T>(InputIterator,InputIterator,boost::compute::command_queue &)' being compiled  
3>          with  
3>          [  
3>              T=unsigned int,  
3>              InputIterator=boost::compute::buffer_iterator<unsigned int>  
3>          ]  
  
There are 3 more error messages like this. Let me know if it helps if I post those here.

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-05-03 00:40:26 UTC  
> Url: https://github.com/boostorg/compute/issues/10#issuecomment-17373432  

Interesting. Can you try removing the line that compares the transforms? Having the same transform type should be enough to ensure that the iterators are the same (if their base iterators are the same).

---

## Comment 4

> Username: d-meiser  
> Created at: 2013-05-03 13:18:05 UTC  
> Url: https://github.com/boostorg/compute/issues/10#issuecomment-17393468  

All examples and benchmarks build with this change. Would you like me to  
open a pull request?  
  
On Thu, May 2, 2013 at 5:40 PM, Kyle Lutz notifications@github.com wrote:  
  
> Interesting. Can you try removing the line that compares the transforms?  
> Having the same transform type should be enough to ensure that the  
> iterators are the same (if their base iterators are the same).  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/issues/10#issuecomment-17373432  
> .

---

## Comment 5

> Username: kylelutz  
> Created at: 2013-05-03 22:39:46 UTC  
> Url: https://github.com/boostorg/compute/issues/10#issuecomment-17422063  

Excellent! I've cherry-picked the change into my branch here: 7c5e321c2a9a371879003c62fbb8fd207b40d5f7. I just changed the new code to use spaces instead of tabs for indentation (so it matches the other code).  
  
Thanks for the patch!  
  
-kyle
