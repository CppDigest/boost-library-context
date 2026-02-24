# #67 Actor is taken by reference on assignment/subscript [Merged]

> Username: Kojoley  
> Created at: 2018-02-04 20:24:24 UTC  
> Updated at: 2018-10-25 21:11:27 UTC  
> Merged at: 2018-02-07 01:05:56 UTC  
> Closed at: 2018-02-07 01:05:56 UTC  
> Url: https://github.com/boostorg/phoenix/pull/67  

It looks like I have found the reason why assignment/subscript operators were defined by hand (ref #64) but not with `BOOST_PROTO_EXTENDS_SUBSCRIPT`/`BOOST_PROTO_EXTENDS_ASSIGN`.  
  
For unknown reason proto's assignment and subscript operators takes rhs by reference while other operators takes both arguments by value.  
  
Example:  
```cpp  
    std::cout << typeid(decltype(boost::phoenix::ref(s) = "asd")).name() << std::endl;  
    std::cout << typeid(decltype(boost::phoenix::ref(s) + "asd")).name() << std::endl;  
```  
```  
boost::phoenix::actor<  
    boost::proto::exprns_::basic_expr<  
        boost::proto::tagns_::tag::assign  
      , boost::proto::argsns_::list2<  
            boost::phoenix::actor<  
                boost::proto::exprns_::basic_expr<  
                    boost::proto::tagns_::tag::terminal  
                  , boost::proto::argsns_::term<  
                        boost::reference_wrapper<  
                            std::basic_string<  
                                char  
                              , std::char_traits<char>  
                              , std::allocator<char>  
                            >  
                        >  
                    >  
                  , 0  
                >  
            > const & /////////////////////////// HERE  
          , boost::phoenix::actor<  
                boost::proto::exprns_::basic_expr<  
                    boost::proto::tagns_::tag::terminal  
                  , boost::proto::argsns_::term<char>  
                  , 0  
                >  
            >  
        >  
      , 2  
    >  
>  
  
boost::phoenix::actor<  
    boost::proto::exprns_::basic_expr<  
        boost::proto::tagns_::tag::plus  
      , boost::proto::argsns_::list2<  
            boost::phoenix::actor<  
                boost::proto::exprns_::basic_expr<  
                    boost::proto::tagns_::tag::terminal  
                  , boost::proto::argsns_::term<  
                        boost::reference_wrapper<  
                            std::basic_string<  
                                char  
                              , std::char_traits<char>  
                              , std::allocator<char>  
                            >  
                        >  
                    >  
                  , 0  
                >  
            > /////////////////////////// HERE  
          , boost::phoenix::actor<  
                boost::proto::exprns_::basic_expr<  
                    boost::proto::tagns_::tag::terminal  
                  , boost::proto::argsns_::term<char>  
                  , 0  
                >  
            >  
        >  
      , 2  
    >  
>  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-02-04 20:29:08 UTC  
> Url: https://github.com/boostorg/phoenix/pull/67#issuecomment-362936893  

I tried to use something like this `proto::call<phoenix_generator(proto::call<proto::value(proto::_left)>, proto::_right)>` but it did not compile/work. I would be glad for pointing me towards a such working rewrite transformation.

---

## Comment 2

> Username: djowel  
> Created_at: 2018-02-04 22:04:58 UTC  
> Updated_at: 2018-02-04 22:05:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/67#issuecomment-362943914  

Bam! There you go. I'm sorry my memory of it has slipped me :-/

---
