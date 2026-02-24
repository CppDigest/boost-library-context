# #72 - make_expression() generates a type more complicated than expected [Closed]

> Username: rgrover  
> Created at: 2018-03-09 19:11:49 UTC  
> Updated at: 2018-06-11 17:16:21 UTC  
> Closed at: 2018-03-10 01:37:52 UTC  
> Url: https://github.com/boostorg/yap/issues/72  

`  
make_expression<boost::yap::expression, expr_kind::terminal>(1);  
`  
  
generates a type which an a terminal within a terminal:  
`  
const boost::yap::expression<  
    boost::yap::expr_kind::terminal,  
    boost::hana::tuple<boost::yap::expression<boost::yap::expr_kind::terminal,  
                                              boost::hana::tuple<int>>>>  
`  
  
This is more complicated than the expected type:  
  
`const boost::yap::expression<boost::yap::expr_kind::terminal, boost::hana::tuple<int> >`  
  
The above is also the type correctly returned by   
  
`expression<expr_kind::terminal, boost::hana::tuple<int>>{1};`

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-03-10 01:37:52 UTC  
> Url: https://github.com/boostorg/yap/issues/72#issuecomment-371991592  

You got what you asked for. :)  You asked YAP to make you an expression of an arbitrary kind, in this case a terminal, whose subexpressions are just integer "1".  But integer 1 is not a YAP expression, so YAP wraps it in a terminal first.  Imagine you had instead called:  
  
auto x = make_expression<boost::yap::expression, boost::yap::expr_kind::unary_plus>(1);  
  
The type of x is:  
  
const boost::yap::expression< boost::yap::expr_kind::unary_plus, boost::hana::tuple<boost::yap::expression<boost::yap::expr_kind::terminal, boost::hana::tuple<int>>>>  
  
... which is the same pattern of return you got with your original call, just with the outer "terminal" replaced with "unary_plus".  
  
I think you meant to use make_terminal instead.  Here are the types MPL reports for these two expressions:  
  
    boost::mpl::print<decltype(boost::yap::make_expression<  
                               boost::yap::expression,  
                               boost::yap::expr_kind::terminal>(1))>  
        f;  
    (void)f;  
  
    boost::mpl::print<decltype(  
        boost::yap::make_terminal<boost::yap::expression>(1))>  
        e;  
    (void)e;  
  
/home/tzlaine/boost_1_66_0/boost/mpl/print.hpp: In instantiation of ‘struct boost::mpl::print<boost::yap::expression<(boost::yap::expr_kind)1, boost::hana::tuple<boost::yap::expression<(boost::yap::expr_kind)1, boost::hana::tuple<int> > > > >’:  
/home/tzlaine/yap/example/calc3.cpp:103:9:   required from here  
/home/tzlaine/boost_1_66_0/boost/mpl/print.hpp:64:22: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
            sizeof(T) > -1  
                  ~~~~^~~~  
/home/tzlaine/boost_1_66_0/boost/mpl/print.hpp: In instantiation of ‘struct boost::mpl::print<boost::yap::expression<(boost::yap::expr_kind)1, boost::hana::tuple<int> > >’:  
/home/tzlaine/yap/example/calc3.cpp:108:9:   required from here  
/home/tzlaine/boost_1_66_0/boost/mpl/print.hpp:64:22: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]

---

## Comment 2

> Username: rgrover  
> Created at: 2018-03-10 02:48:09 UTC  
> Url: https://github.com/boostorg/yap/issues/72#issuecomment-371996769  

Thanks for this explanation. Could be useful to add to the documentation.  
  
By the say, the trick about using boost::mpl::print doesn't work for me (at least with clang-5). It is a useful tool to have. How do you trigger your type-printing error/warning messages?

---

## Comment 3

> Username: tzlaine  
> Created at: 2018-03-10 04:05:10 UTC  
> Url: https://github.com/boostorg/yap/issues/72#issuecomment-372000775  

I think you need -Wall.  Another option is to do MPL_ASSERT:  
  
            BOOST_MPL_ASSERT((std::is_same<  
                              decltype(yap::deref(std::move(ref))),  
                              term<double> &>));  
  
Which produces very nice, very verbose output for what the two non-same types are.
