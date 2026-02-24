# #73 - unhelpful error message when using a class template which doesn't satisfy the expression concept [Closed]

> Username: rgrover  
> Created at: 2018-03-09 20:25:05 UTC  
> Updated at: 2018-06-11 17:16:21 UTC  
> Closed at: 2018-03-10 01:41:06 UTC  
> Url: https://github.com/boostorg/yap/issues/73  

The following definition of an expression template attempts to use std::tuple as the element container.   
     
```C++  
    template <expr_kind Kind, typename T>  
    struct m_expr {  
        constexpr static expr_kind kind = Kind;  
      
        using tuple_type = std::tuple<T>;  
      
        tuple_type elements;  
    };  
  
    int main()  
    {  
        constexpr auto e = make_terminal<m_expr>(1);  
    }  
  
```  
  
It fails to compile with a unhelpful error message `error: static_assert failed "evaluate() is only defined for expressions."`  
  
I propose the following version of `is_expr<>` as a remedy.  
  
```C++  
    template<  
        typename Expr,  
        typename = detail::void_t<>,  
        typename = detail::void_t<>>  
    struct is_expr : std::false_type  
    {  
    };  
  
    template<typename Expr>  
    struct is_expr<  
        Expr,  
        detail::void_t<decltype(detail::remove_cv_ref_t<Expr>::kind)>,  
        detail::void_t<decltype(std::declval<Expr>().elements)>>  
        : std::true_type  
    {  
        static_assert(  
            std::is_same<  
                std::remove_cv_t<decltype(detail::remove_cv_ref_t<Expr>::kind)>,  
                expr_kind  
            >::value,  
            "expression template needs a yap::expr_kind tag");  
        static_assert(  
            hana::is_a<  
                hana::tuple_tag,  
                decltype(std::declval<Expr>().elements)  
            >(),  
        "expression template needs a hana tuple like container for elements");  
    };  
```

---

## Comment 1

> Username: rgrover  
> Created at: 2018-03-09 21:19:32 UTC  
> Url: https://github.com/boostorg/yap/issues/73#issuecomment-371948026  

Maybe the fall-back instantiation of is_expr should also static_assert?

---

## Comment 2

> Username: tzlaine  
> Created at: 2018-03-10 01:41:06 UTC  
> Url: https://github.com/boostorg/yap/issues/73#issuecomment-371991866  

This is best we can do in this case, afaict.  We cannot add the static_asserts) above without breaking SFINAE uses of is_expr<>, which is one of its major use cases.  If you have another way to report errors more gracefully without resorting to static_assert(), I'd be happy to use it.

---

## Comment 3

> Username: rgrover  
> Created at: 2018-03-10 02:22:05 UTC  
> Url: https://github.com/boostorg/yap/issues/73#issuecomment-371994985  

If is_expr<> needs to retain its ability to SFINAE, then top-level YAP APIs should come with useful static_asserts to help identify concept-check failures.

---

## Comment 4

> Username: tzlaine  
> Created at: 2018-03-10 04:03:19 UTC  
> Url: https://github.com/boostorg/yap/issues/73#issuecomment-372000689  

They do.  usually it's just static_assert(is_expr<Expr>::value, "Expr must be a YAP expressions") or similar.  It's then up to the user to figure out why Expr is not a model of Expression.  The requirements are extremely simple for the user to check/audit/enforce.
