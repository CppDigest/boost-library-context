# #91 - warning: Undefined or garbage value returned to caller [clang-analyzer-core.uninitialized.UndefReturn] [Closed]

> Username: hgkjshegfskef  
> Created at: 2021-09-03 22:20:08 UTC  
> Updated at: 2022-09-02 13:27:13 UTC  
> Closed at: 2022-09-02 13:27:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/91  

Clang-tidy 12 is complaining:  
```  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/detail/sequence_tuple.hpp:53:5: warning: Undefined or garbage value returned to caller [clang-analyzer-core.uninitialized.UndefReturn]  
    return t.value;  
    ^  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:126:37: note: Assuming 'nth_batch' is < 'batch_count'  
    for (std::size_t nth_batch = 0; nth_batch < batch_count; ++nth_batch) {  
                                    ^  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:126:5: note: Loop condition is true.  Entering loop body  
    for (std::size_t nth_batch = 0; nth_batch < batch_count; ++nth_batch) {  
    ^  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:127:9: note: Loop condition is true.  Entering loop body  
        for (std::size_t nth_color = 0; nth_color < batch_size; ++nth_color) {  
        ^  
/home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:128:13: note: Calling 'for_each_field<cherry_blazer::Color &, (lambda at /home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:129:62)>'  
            boost::pfr::for_each_field(  
            ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/core.hpp:187:5: note: Calling 'for_each_field_dispatcher<cherry_blazer::Color, (lambda at /home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/core.hpp:189:9), 0, 1, 2>'  
    ::boost::pfr::detail::for_each_field_dispatcher(  
    ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/detail/core17.hpp:64:5: note: Calling 'operator()'  
    std::forward<F>(f)(  
    ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/core.hpp:194:13: note: Calling 'for_each_field_impl<boost::pfr::detail::sequence_tuple::tuple<double &, double &, double &>, (lambda at /home/runner/work/cherry_blazer/cherry_blazer/src/canvas.cc:129:62), 0, 1, 2>'  
            ::boost::pfr::detail::for_each_field_impl(  
            ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/detail/for_each_field_impl.hpp:35:44: note: Calling 'get<0, double &, double &, double &>'  
         detail::for_each_field_impl_apply(sequence_tuple::get<I>(t), std::forward<F>(f), size_t_<I>{}, 1L),  
                                           ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/detail/sequence_tuple.hpp:92:12: note: Calling 'get_impl<0, double &>'  
    return sequence_tuple::get_impl<N>(t);  
           ^  
/home/runner/work/cherry_blazer/cherry_blazer/_deps/boost_pfr-src/include/boost/pfr/detail/sequence_tuple.hpp:53:5: note: Undefined or garbage value returned to caller  
    return t.value;  
    ^  
```  
Not sure if this is something easily reproducible or not, I haven't tried. Just my first time using this library.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-09-10 16:30:27 UTC  
> Url: https://github.com/boostorg/pfr/issues/91#issuecomment-917040242  

Please, provide a minimal working example to reproduce the issue

---

## Comment 2

> Username: hgkjshegfskef  
> Created at: 2021-09-10 17:10:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/91#issuecomment-917068719  

Here: https://godbolt.org/z/GsEd7YqjP

---

## Comment 3

> Username: apolukhin  
> Created at: 2022-09-02 13:27:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/91#issuecomment-1235505950  

Looks like it was fixed in https://github.com/boostorg/pfr/commit/2a1fd53724d41fe590633d67f8f712c2e06a6398  
  
Thanks for the bugreport!
