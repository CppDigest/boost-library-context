# #881 - Explore relationship between grammar::string_view_base and std::string_view [Open]

> Username: alandefreitas  
> Created at: 2024-11-11 18:51:21 UTC  
> Updated at: 2024-11-11 18:51:21 UTC  
> Url: https://github.com/boostorg/url/issues/881  

Can someone derive from `std::string_view` as an alternative to deriving from `boost::urls::grammar::string_view_base`?  
  
`string_view_base` stores a `core::string_view` and is a proxy for its member functions. In principle and semantically, it's guaranteed that `string_view_base` is OK to use as a base class while `std::string_view` or `core::string_view` are not. In practice, `string_view_base` has no virtual functions and doesn't have a virtual destructor.  
  
The only class that refers to and derives from it is `pct_string_view`, making it inherit the usual member functions of a string_view.
