# #260 - Is `phoenix::val(spirit::ascii::string("Hello"))` a valid `qi` expression? [Closed]

> Username: Kojoley  
> Created at: 2017-11-06 16:05:26 UTC  
> Updated at: 2017-11-08 21:58:50 UTC  
> Closed at: 2017-11-08 21:58:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/260  

This example https://github.com/boostorg/spirit/blob/1e31221cdd9c3586946e2aac9782fa93d4658373/example/qi/reference.cpp#L773 of [`lazy` documentation](http://www.boost.org/doc/libs/1_65_1/libs/spirit/doc/html/spirit/qi/reference/auxiliary/lazy.html) does not compile. It fails on assert `BOOST_SPIRIT_ASSERT_MATCH(qi::domain, Expr);` what is perfectly logical, because `phoenix::val` is not a `qi` expression.  
  
I suppose it is an error in documentation, should not it be `string("Hello")`?

---

## Comment 1

> Username: Kojoley  
> Created at: 2017-11-08 17:12:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/260#issuecomment-342887369  

@djowel what do you think about this? Merge #267 if you are agree.
