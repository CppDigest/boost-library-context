# #39 - boost::apply_visitor failes when passed inlined mutable lambda [Closed]

> Username: paweldac  
> Created at: 2017-11-07 22:39:14 UTC  
> Updated at: 2018-01-23 07:39:29 UTC  
> Closed at: 2018-01-23 07:39:29 UTC  
> Url: https://github.com/boostorg/variant/issues/39  

same as: https://svn.boost.org/trac10/ticket/13288  
  
when passed inlined mutable lambda to `boost::apply_visitor` wrong specialization is taken into use.  
  
temporary lambda object is binded to const T&. As a result when result_wrapper1 is created:  
`boost::detail::variant::result_wrapper1<const Visitor, typename remove_reference<Visitable>::type> cpp14_vis(visitor);` it's done with `const Visitor`. This results in making visitor `const Visitor&` in line apply_visitor_unary.hpp:158 and leads to compilation error.
