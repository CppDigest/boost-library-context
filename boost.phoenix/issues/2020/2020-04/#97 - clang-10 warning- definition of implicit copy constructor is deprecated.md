# #97 - clang-10 warning: definition of implicit copy constructor is deprecated [Closed]

> Username: k15tfu  
> Created at: 2020-04-20 16:25:48 UTC  
> Updated at: 2023-03-07 14:38:16 UTC  
> Closed at: 2020-06-14 22:57:19 UTC  
> Url: https://github.com/boostorg/phoenix/issues/97  

In boost/phoenix/core/actor.hpp:  
```  
include/boost/phoenix/core/actor.hpp:148:9: error: definition of implicit copy constructor for 'actor<boost::log::v2s_mt_posix::expressions::aux::unary_function_terminal<boost::log::v2s_mt_posix::expressions::has_attribute<void> > >' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        BOOST_PROTO_EXTENDS_ASSIGN()  
        ^  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-04-20 17:03:51 UTC  
> Url: https://github.com/boostorg/phoenix/issues/97#issuecomment-616686615  

This is the Proto issue, it should silence the warning inside the macro.

---

## Comment 2

> Username: nickwilliams-zaxiom  
> Created at: 2023-03-07 14:33:25 UTC  
> Updated at: 2023-03-07 14:34:26 UTC  
> Url: https://github.com/boostorg/phoenix/issues/97#issuecomment-1458278972  

I'm still getting this warning in 1.80.0 (which includes #98), the warning has just moved to the added line:  
  
```  
phoenix/core/actor.hpp:228:39: warning: definition of implicit copy constructor for 'actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::boost_phoenix::detail::rethrow>, 0>>' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
        BOOST_DELETED_FUNCTION(actor& operator=(actor const&))  
```  
  
Do I need to file a new bug? Or is there already a different bug I didn't find?

---

## Comment 3

> Username: nickwilliams-zaxiom  
> Created at: 2023-03-07 14:35:19 UTC  
> Url: https://github.com/boostorg/phoenix/issues/97#issuecomment-1458282721  

And right as I commented, I found #114.

---

## Comment 4

> Username: Kojoley  
> Created at: 2023-03-07 14:38:16 UTC  
> Url: https://github.com/boostorg/phoenix/issues/97#issuecomment-1458287443  

> I'm still getting this warning in 1.80.0 (which includes #98), the warning has just moved to the added line:  
>   
> ```  
> phoenix/core/actor.hpp:228:39: warning: definition of implicit copy constructor for 'actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::boost_phoenix::detail::rethrow>, 0>>' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
>         BOOST_DELETED_FUNCTION(actor& operator=(actor const&))  
> ```  
>   
> Do I need to file a new bug? Or is there already a different bug I didn't find?  
  
It is fixed in 1.81 by https://github.com/boostorg/phoenix/pull/108
