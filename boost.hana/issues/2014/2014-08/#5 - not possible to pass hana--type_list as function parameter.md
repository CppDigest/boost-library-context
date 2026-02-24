# #5 - [improvement] not possible to pass hana::type_list as function parameter? [Closed]

> Username: kris-jusiak  
> Created at: 2014-08-05 14:31:11 UTC  
> Updated at: 2014-08-05 17:31:20 UTC  
> Closed at: 2014-08-05 15:28:22 UTC  
> Url: https://github.com/boostorg/hana/issues/5  

its not really hana issue, but would be nice to have.  
  
since type_list is a variable template returning `tlist_detail::type_list<xs...>::type` is not possible to pass it to function?  
  
guess, it would works if not for nested type struct?  
  
```  
    namespace tlist_detail {  
        template <typename ...xs>  
        struct type_list {  
            struct type : operators::enable {  
                using hana_datatype = TypeList;  
  
                template <template <typename ...> class f>  
                using storage = f<xs...>;  
  
                template <typename F>  
                static constexpr auto storage_(F f) {  
                    return f.template apply<xs...>();  
                }  
            };  
        };  
    }  
```  
  
```  
template<typename... TArgs>  
void f(const typename hana::tlist_detail::type_list<TArgs...>::type&) { }  
  
int main() {  
    f(hana::type_list<int, double>);  
}  
```  
  
would be great to be able to do sth like that:  
  
```  
template<typename... TArgs>  
void f(const hana::type_list<TArgs...>&) { }  
```  
  
error: no matching function for call to 'f'  
note: candidate function [with TArgs = <>] not viable: no known conversion from 'const typename tlist_detail::type_list<int, double>::type' to 'const typename hana::tlist_detail::type_list<>::type'  
      for 1st argument  
void f(const typename hana::tlist_detail::type_list<TArgs...>::type&) { }

---

## Comment 1

> Username: ldionne  
> Created at: 2014-08-05 14:54:23 UTC  
> Url: https://github.com/boostorg/hana/issues/5#issuecomment-51208723  

The reason why   
  
```  
template<typename... TArgs>  
void f(const typename hana::tlist_detail::type_list<TArgs...>::type&) { }  
```  
  
won't work is that `typename hana::tlist_detail::type_list<TArgs...>::type` is a dependent type, which can't be used to perform template argument deduction.   
  
The reason why `type_list<...>` has such a weird type is because of an issue with ADL. Consider the following:  
  
```  
template <typename ...x>  
struct invalid_if_instantiated {  
    static_assert(sizeof...(x) && false,   
    "this causes a hard error when instantiated");  
};  
  
type_list<invalid_if_instantiated<>> != type_list<void>;  
```  
  
When we perform the comparison, ADL kicks in to resolve the operator!=. However, ADL has this (annoying IMO) property of instantiating the types used as template arguments of the operands. Hence, if `invalid_if_instantiated<>` appears somewhere in the type of `type_list<invalid_if_instantiated<>>`, it will be instantiated and that will cause a hard error. In summary, we need to hide the contents of `type_list<...>` because ADL could instantiate it undesirably.  
  
That being said, the library was built so this kind of trick (i.e. pattern matching on the type of the containers) was not necessary. If you need a variadic access to the contents of the `type_list`, use  
  
```  
auto f = [](auto ...types) {  
    // whatever  
};  
  
unpack(f, type_list<...>)  
```  
  
Or, if you really only need type-level computations (e.g. without side effects), you can use:  
  
```  
template <typename ...types>  
struct f {  
    // regular Boost.MPL metafunction for type only computation  
};  
  
unpack(metafunction<f>, type_list<...>)  
```  
  
The latter will be more efficient once a couple of easy optimizations are implemented.

---

## Comment 2

> Username: kris-jusiak  
> Created at: 2014-08-05 17:31:20 UTC  
> Url: https://github.com/boostorg/hana/issues/5#issuecomment-51231374  

unpack will do the thing, cheers
