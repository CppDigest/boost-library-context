# #813 Modernize `rule`, `guard`, `on_error` and `on_success` [Merged]

> Username: saki7  
> Created at: 2025-09-06 22:25:24 UTC  
> Updated at: 2025-09-07 17:26:52 UTC  
> Merged at: 2025-09-07 17:26:48 UTC  
> Closed at: 2025-09-07 17:26:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/813  

Part of boostorg/spirit_x4#4   
  
`rule` now resolves the parse function using concepts, providing  
significantly faster compile time & better errors.  
  
`core/error_handler_types.hpp`: New header to separate enum  
definition.  
  
`annotate_on_success`: Modernized.  
  
`on_error` and `on_success` now only accepts const iterators.  
This is a breaking change, but we should apply this immediately  
due to the reasons described below.  
  
Historically, Spirit has passed mutable lvalue references of the  
*internal* iterators to the `on_success`/`on_error` handlers. This  
behavior was semantically a mistake, because:  
  (1) `on_success`/`on_error` mechanism was designed to be  
      grammar-agnostic, and  
  (2) it does not make sense to modify the grammar-specific  
      iterator on the grammar-agnostic callback.  
  
Furthermore, any modification to X3's internal iterator variables  
may invoke undefined behavior, since we had never provided any  
kind of guarantee on how those variables are processed in X3's  
implementation details.  
  
In other words, I consider the old behavior as a serious BUG  
that involves undefined behavior which may even lead to   
security issues.  
  
`BOOST_SPIRIT_DECLARE`: Deprecated regarding compile-time slowness  
of `BOOST_PP_SEQ_FOR_EACH`.  
  
`BOOST_SPIRIT_DEFINE`: Ditto.  
  
`BOOST_SPIRIT_INSTANTIATE`: Deprecated because the name was not  
correctly prefixed with `X3_`.  
  
`BOOST_SPIRIT_X3_DECLARE`: New macro with correctly prefixed name.  
  
`BOOST_SPIRIT_X3_DEFINE`: Ditto.  
  
`BOOST_SPIRIT_X3_INSTANTIATE`: Ditto.

---
