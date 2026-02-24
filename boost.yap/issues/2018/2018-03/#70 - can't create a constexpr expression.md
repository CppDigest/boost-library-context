# #70 - can't create a constexpr expression [Closed]

> Username: rgrover  
> Created at: 2018-03-09 18:50:06 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-03-10 01:24:55 UTC  
> Url: https://github.com/boostorg/yap/issues/70  

having expression<> as an aggregate would allow the following to work:  
`constexpr auto e = make_expression<yap::expression, expr_kind::terminal>(1);`  
  
The compiler should generate useful default constructors anyway. Can we do without the constructor taking a hana::tuple as an r-value?

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-03-10 01:25:27 UTC  
> Url: https://github.com/boostorg/yap/issues/70#issuecomment-371990383  

I like the goal, but removing ctors is unnecessary when making a literal type.
