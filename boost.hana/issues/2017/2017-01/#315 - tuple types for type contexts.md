# #315 - tuple types for type contexts [Open]

> Username: trueqbit  
> Created at: 2017-01-04 17:45:21 UTC  
> Updated at: 2017-02-12 23:07:35 UTC  
> Url: https://github.com/boostorg/hana/issues/315  

I may have not yet fully switched my brain to thinking in terms of heterogenous programming, however I think that being able to derive from hana::tuple<hana::type<...>> and hana::tuple<hana::integral_constant<...>> w/o the disturbing boiler-plate is useful, just like using the variable templates hana::tuple_t<> and hana::tuple_c<>.  
  
Consider partial specialization for the sake of overload resolution;  
Right now one has to write:  
```  
template<typename ...Types>  
struct my_type_list: hana::tuple<hana::type<Types...>> {};  
// or  
template<typename T, T ...v>  
struct my_value_list: hana::tuple<hana::integral_constant<T, v>...> {};  
```  
or  
```  
template<typename ...Types>  
struct my_type_list: decltype(hana::tuple_t<Types...>) {};  
// or  
template<typename T, T ...v>  
struct my_value_list: decltype(hana::tuple_c<T, v>) {};  
```  
  
  
If hana would provide template aliases it would be as simple as:  
```  
namespace hana {  
    template<typename ...Types>  
    using type_tuple = decltype(hana::tuple_t<Types...>);  
    template<typename T, T ...v>  
    using ic_tuple = decltype(hana::tuple_c<T, v...>);  
}  
  
template<typename ...Types>  
struct my_type_list: hana::type_tuple<Types...> {};  
// or  
template<typename T, T ...v>  
struct my_value_list: hana::ic_tuple<T, v...> {};  
```  
  
As hana already aims at aggressively reducing boiler-plate code, I would say that adding a template alias for this usage pattern is worth it?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-01-04 18:05:29 UTC  
> Url: https://github.com/boostorg/hana/issues/315#issuecomment-270441173  

Hi.  
  
For a list of types, take a look at `hana::experimental::types`. It is optimized for types and I believe it is meant to eventually replace `tuple_t`.  
  
For a list of homogeneous integers, an `std::index_sequence` is more efficient than a tuple of integral constants.  
  
Using a `hana::tuple` for these means that you have to wrap each element which results in more template instantiations and longer compile times.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-01-04 20:44:57 UTC  
> Url: https://github.com/boostorg/hana/issues/315#issuecomment-270481483  

BTW, I don't think it is recommended to derive from Hana types. What is the purpose for doing that?

---

## Comment 3

> Username: ldionne  
> Created at: 2017-01-05 05:30:06 UTC  
> Url: https://github.com/boostorg/hana/issues/315#issuecomment-270567211  

In a future version of Hana, `hana::tuple_t` and `hana::tuple_c` will not be variable templates, but types equivalent to the current `decltype(hana::tuple_t<...>)` and `decltype(hana::tuple_c<...>)` instead. I believe that would solve your problem.

---

## Comment 4

> Username: trueqbit  
> Created at: 2017-01-05 12:51:29 UTC  
> Url: https://github.com/boostorg/hana/issues/315#issuecomment-270637066  

@ldionne Yes, this would actually solve the need. Variable templates OTH are quite handy to actually work with objects, so I would choose a different nomenclature to get both.  
  
@ricejasonf One usecase sometimes coming up is to collect symbols with external linkage (function addresses and extern variables) in a compile-time sequence and later do stuff with it. Apart from just giving something an alias name, a custom type is more specialized and allows for picking overloads when "doing stuff", e.g.:  
```  
template<FnPtr ...Functions>  
struct function_tuple: decltype(hana::tuple_c<FnPtr, Functions...>) {};  
  
template<FnPtr ...Functions>  
std::ostream& operator <<(std::ostream& os, function_tuple<Functions...> functions);  
```  
... while an alias would match all `hana::tuple_c<FnPtr, Functions...>`, no matter what purpose they serve.  
  
For a fully working example see this "[fiddle](http://melpon.org/wandbox/permlink/BMA02p3mEC52gIhq)", actually produced for this [stackoverflow question](http://stackoverflow.com/questions/19535290/how-to-create-static-strings-from-types-at-compile-time).  
  
Maybe the same could be achieved by tag specialization?

---

## Comment 5

> Username: ldionne  
> Created at: 2017-02-12 23:04:19 UTC  
> Url: https://github.com/boostorg/hana/issues/315#issuecomment-279258721  

> For a fully working example see this "fiddle", actually produced for this stackoverflow question.  
> Maybe the same could be achieved by tag specialization?  
  
This is actually tricky. You're inheriting from `hana::tuple` and then assuming that stuff like `hana::intersperse` will work on that. It's not required to, and it just works because of the way tag dispatching was set up. Instead, I would suggest providing a method to your `function_tuple` that returns an actual `hana::tuple` with the right stuff in it. Then, you pass around the `function_tuple` and when you need a real Hana sequence, you get it by calling that function.  
  
The other way is to make your `function_tuple` a full blown Hana sequence, but that's most likely _way_ overkill.
