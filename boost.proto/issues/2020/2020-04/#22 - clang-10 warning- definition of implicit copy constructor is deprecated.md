# #22 - clang-10 warning: definition of implicit copy constructor is deprecated [Closed]

> Username: k15tfu  
> Created at: 2020-04-20 16:30:16 UTC  
> Updated at: 2020-04-30 22:59:32 UTC  
> Closed at: 2020-04-30 22:59:32 UTC  
> Url: https://github.com/boostorg/proto/issues/22  

In file boost/proto/detail/preprocessed/expr_variadic.hpp:  
```  
boost/proto/detail/preprocessed/expr_variadic.hpp:96:9: error: definition of implicit copy constructor for 'expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::log::v2s_mt_posix::expressions::aux::unary_function_terminal<boost::log::v2s_mt_posix::expressions::has_attribute<void> > >, 0>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        operator =(expr const &a)  
        ^  
```
