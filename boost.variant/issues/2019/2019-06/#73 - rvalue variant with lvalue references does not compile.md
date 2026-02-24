# #73 - rvalue variant with lvalue references does not compile [Open]

> Username: slymz  
> Created at: 2019-06-24 15:25:42 UTC  
> Updated at: 2019-12-19 05:29:55 UTC  
> Url: https://github.com/boostorg/variant/issues/73  

As far as I can tell, the following is a legitimate code which attempts to a temporary variant to keep references. But it fails to compile:  
  
```.cpp  
struct Changer : boost::static_visitor<void> {  
    void operator()(int& i) const  {  
        i += 10;  
    }  
    void operator()(double& d) const {  
        d += 20;  
    }  
};  
  
auto getVar(int choose, int& i, double& d){  
    using V = boost::variant<int&, double&>;  
    return choose == 0 ? V(i) : V(d);  
}  
  
void foo() {  
    int i = 0;  
    double d = 0;  
    Changer c;  
    auto v1 = getVar(0,i,d);  
    boost::apply_visitor(c, v1); // OK  
    assert(i == 10);  
    assert(d == 0.);  
    boost::apply_visitor(c, getVar(1,i,d));  // ERROR  
    assert(i == 10);  
    assert(d == 20.);  
}  
```  
  
```  
error: no matching function for call to object of type 'Changer'  
```  
  
See https://godbolt.org/z/FVVA_t for the error.  
  
It seems `apply_visitor` is making the assumption that if a variant is rvalue, so should be the elements. But (unlike std::variant) boost::variant supports references as elements, so the two are decoupled.  
  
This makes variants impossible to use as proxy objects.

---

## Comment 1

> Username: lennoxho  
> Created at: 2019-12-19 05:08:27 UTC  
> Updated at: 2019-12-19 05:29:55 UTC  
> Url: https://github.com/boostorg/variant/issues/73#issuecomment-567335337  

It appears that the issue manifests within boost::detail::variant::invoke_visitor.  
  
Here's the prototype  
```  
template <typename Visitor, bool MoveSemantics>  
class invoke_visitor;  
```  
  
The second template parameter is MoveSemantics.  
Depending on its value, we call a different overload of internal_visit:  
```  
    //using workaround with is_same<T, T> to prenvent compilation error, because we need to use T in enable_if to make SFINAE work  
    template <typename T>  
    typename enable_if_c<MoveSemantics && is_same<T, T>::value, result_type>::type internal_visit(T&& operand, int)  
    {  
        return visitor_(::boost::move<T>(operand));  
    }  
  
    //using workaround with is_same<T, T> to prenvent compilation error, because we need to use T in enable_if to make SFINAE work  
    template <typename T>  
    typename disable_if_c<MoveSemantics && is_same<T, T>::value, result_type>::type internal_visit(T&& operand, int)  
    {  
        return visitor_(operand);  
    }  
```  
To be precise, if MoveSemantics == true, we always call  
```  
visitor_(::boost::move<T>(operand));  
```  
which casts operand, which is an int& to int&&.  
  
We can confirm this by looking at the compiler error messages from your example:  
```  
/celibs/boost_1_70_0/boost/variant/variant.hpp: In instantiation of 'typename boost::enable_if_c<(MoveSemantics && boost::is_same<Value2, Value2>::value), typename Visitor::result_type>::type boost::detail::variant::invoke_visitor<Visitor, MoveSemantics>::internal_visit(T&&, int) [with T = int&; Visitor = Changer; bool MoveSemantics = true; typename boost::enable_if_c<(MoveSemantics && boost::is_same<Value2, Value2>::value), typename Visitor::result_type>::type = void]':  
```  
Note the "with T = int&".  
  
This tells us that the non-const lvalue ref qualifiers is properly propagated down to internal_visit.  
  
Going up the compiler trace, it would appear that depending on the ref-qualifier of the given boost::variant object, boost::detail::variant::invoke_visitor is instantiated differently.  
  
In particular, in the case where the boost::variant is an rvalue, the rvalue-ref qualified overload of apply_visitor is called  
```  
    template <typename Visitor>  
    typename Visitor::result_type  
    apply_visitor(Visitor& visitor) &&  
    {  
        detail::variant::invoke_visitor<Visitor, true> invoker(visitor);  
        return this->internal_apply_visitor(invoker);  
    }  
```  
the second "true" template argument corresponds to MoveSemantics.  
  
What this means is if the boost::variant object is an rvalue, then we always attempt to move the value.  
  
In the case where the variant type is non-reference I guess that makes sense, but in the case where we have an lvalue reference (both const and non-const) in a variant, then I would argue that the variant does not own the value referred to and therefore should not attempt to move.
