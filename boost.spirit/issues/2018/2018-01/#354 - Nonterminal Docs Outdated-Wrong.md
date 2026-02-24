# #354 - Nonterminal Docs Outdated/Wrong [Closed]

> Username: cmazakas  
> Created at: 2018-01-18 16:03:25 UTC  
> Updated at: 2018-02-15 16:17:39 UTC  
> Closed at: 2018-02-15 16:17:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/354  

http://www.boost.org/doc/libs/1_66_0/libs/spirit/doc/html/spirit/qi/reference/parser_concepts/nonterminal.html  
  
^ From the above, one can read:  
> `X::sig_type` The Signature of X: An MPL Forward Sequence. The first element is the Nonterminal's synthesized attribute type and the rest are the inherited attribute types.  
  
In all reality, the type is a function signature like `std::string(void)` given a rule type of `qi::rule<char const*, std::string()>` and as such, doesn't really work with any of the MPL functions, namely `mpl::front<...>::type`.
