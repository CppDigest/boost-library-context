# #9 - Create BOOST_PARSER_ASSERT() macro to switch between runtime and compile time assertions. [Closed]

> Username: tzlaine  
> Created at: 2020-09-02 02:58:29 UTC  
> Updated at: 2020-09-02 04:04:12 UTC  
> Closed at: 2020-09-02 04:04:12 UTC  
> Url: https://github.com/boostorg/parser/issues/9  

Right now, there is an ad hoc system in place, in which a macro is used to conditionally compile a static_assert or a BOOST_ASSERT, and some other places never use the BOOST_ASSERT option. Regularize with a macro that hides the conditional assertion type.
