# #76 - Multiple locations in the code cause gcc -Wzero-as-null-pointer-constant warning [Closed]

> Username: BartSiwek  
> Created at: 2020-03-28 10:18:51 UTC  
> Updated at: 2020-04-11 19:33:10 UTC  
> Closed at: 2020-04-11 19:33:09 UTC  
> Url: https://github.com/boostorg/variant/issues/76  

Multiple places in the code use `0` as a null pointer constant inside SFINAE. This combined with GCC reporting this warning at call sites instead of headers makes it impossible to use variant and have this warning enabled.  
  
A list resulting from a quick grep:  
```  
./include/boost/variant/detail/apply_visitor_binary.hpp:264:    decltype(auto) operator()(Value2&& value2, typename enable_if_c<MoveSemantics && is_same<Value2, Value2>::value>::type* = 0)  
./include/boost/variant/detail/apply_visitor_binary.hpp:270:    decltype(auto) operator()(Value2&& value2, typename disable_if_c<MoveSemantics && is_same<Value2, Value2>::value>::type* = 0)  
./include/boost/variant/detail/apply_visitor_binary.hpp:296:    decltype(auto) operator()(Value1&& value1, typename enable_if_c<MoveSemantics && is_same<Value1, Value1>::value>::type* = 0)  
./include/boost/variant/detail/apply_visitor_binary.hpp:308:    decltype(auto) operator()(Value1&& value1, typename disable_if_c<MoveSemantics && is_same<Value1, Value1>::value>::type* = 0)  
./include/boost/variant/detail/apply_visitor_binary.hpp:329:    >::type* = 0)  
./include/boost/variant/detail/apply_visitor_binary.hpp:342:    >::type* = 0)  
./include/boost/variant/detail/apply_visitor_unary.hpp:134:    >::type* = 0)  
./include/boost/variant/detail/multivisitors_cpp14_based.hpp:110:        >::type* = 0)  
./include/boost/variant/detail/multivisitors_cpp14_based.hpp:131:        >::type* = 0)  
./include/boost/variant/detail/visitation_impl.hpp:180:    , NBF, W* = 0, S* = 0  
./include/boost/variant/detail/visitation_impl.hpp:199:    , Which* = 0, step0* = 0  
./include/boost/variant/get.hpp:106:        , t* = 0  
./include/boost/variant/polymorphic_get.hpp:139:        , t* = 0  
./include/boost/variant/variant.hpp:1717:            boost::is_same<T, boost::recursive_variant_> > >::type* = 0)  
./include/boost/variant/variant.hpp:1731:            boost::is_same<T, boost::recursive_variant_> > >::type* = 0  
./include/boost/variant/variant.hpp:1747:            boost::is_same<T, boost::recursive_variant_> > >::type* = 0)  
./test/variant_visit_test.cpp:82:inline void unary_test(Variant& var, Checker* = 0)  
./test/variant_visit_test.cpp:100:inline void binary_test(Variant1& var1, Variant2& var2, Checker* = 0)  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-04-11 19:33:09 UTC  
> Url: https://github.com/boostorg/variant/issues/76#issuecomment-612497645  

Fix will appear in Boost 1.73  
Many thanks for the report and for the fix!
