# #116 - Unexpected results when setting a parameter value with a string returned from a lambda [Closed]

> Username: spchamp  
> Created at: 2025-12-29 15:28:16 UTC  
> Updated at: 2025-12-30 17:50:15 UTC  
> Closed at: 2025-12-29 20:38:19 UTC  
> Url: https://github.com/boostorg/parameter/issues/116  

After storing a string value to an argument pack for a given parameter keyword,  when the string value is provided via std::invoke of a lambda expression, then the string retrieved for that same keyword may represent an unexpected value.   
  
There's a minimal example of the issue, available [at the Compiler Explorer](https://godbolt.org/z/rGjved78b).   
  
The parameter value is being set with the following function:  
  
```cpp  
template <typename Tag, typename Func, typename Args>  
  requires (!detail::argument_pack_contains<Args, param::keyword<Tag>>::value)  
inline auto ensure_param_default(Args const& args, param::keyword<Tag> const& kw,  
                                 Func&& func) {  
  return param::compose((kw = std::invoke(func, args)), args);  
}  
````  
  
When this is called like as follows, the resulting string value may have a size far exceeding 3:  
  
```cpp  
BOOST_PARAMETER_NAME((_arg1, tags) test_arg1)  
BOOST_PARAMETER_NAME((_arg2, tags) test_arg2)  
   
// [...]  
  
int main() {  
  auto const args_in = (_arg2 = 31213);  
  
  auto const args_out =  
        ensure_param_default(args_in, _arg1, [](auto const&) -> std::string {  
          return "ABC";  
        });  
  
   // [...]  
  
  std::string const& s_out = args_out[_arg1];  
  
  // [...]  
}  
```  
  
With the example [at the Compiler Explorer](https://godbolt.org/z/rGjved78b), when this is compiled locally, I'm seeing results such as the following. The mangled string is varying in each call.  
  
```  
-- failed : string test  
s_out.size() := 140722429578688  
s_out.data() := �9  
-- passed : int test  
```  
  
The Compiler Explorer may not display the unexpected character data, in this case.  
  
I'd discovered this when trying to produce a function that would return an argument pack, after providing any system-specified default values for certain keywords when not provided in the user args. This was for a purpose of applying the resulting argument pack, when initializing a Boost.Log console frontend.  I'm certain that there may be other ways to approach this, using Boost.Parameter.   
  
With the present example, could it be a matter of how the string is being provided for the parameter keyword?  
  
I'm afraid that I've not been able to reproduce a failing test for integer storage, using a similar approach The example at the compiler explorer might at least illustrate the failing characters/string test.

---

## Comment 1

> Username: spchamp  
> Created at: 2025-12-29 19:04:38 UTC  
> Url: https://github.com/boostorg/parameter/issues/116#issuecomment-3697278222  

I notice that the example fails to compile if defining `args_out_2` as beginning from the value  initially returned from the local  `ensure_param_default` function \[[Compiler Explorer](https://godbolt.org/z/dh5bcTc39)]:  
  
```cpp  
  auto const args_out_2 =  
        ensure_param_default(args_out, _arg3, [&called_p](auto const&) -> int {  
          called_p = true;  
          return 12321;  
        });  
```  
This results in the following error message:  
  
```  
<source>:55:10: err: no matching function for call to 'compose'  
   55 |   return param::compose((kw = std::invoke(func, args)), args);  
      |          ^~~~~~~~~~~~~~  
<source>:101:9: note: in instantiation of function template specialization 'ensure_param_default<tags::test_arg3, (lambda at <source>:101:47), boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<tags::test_arg1, boost::parameter::aux::tagged_argument_rref<tags::test_arg1, std::basic_string<char>>>, boost::parameter::aux::flat_like_arg_tuple<tags::test_arg2, boost::parameter::aux::tagged_argument<tags::test_arg2, const int>>>>' requested here  
  101 |         ensure_param_default(args_out, _arg3, [&called_p](auto const&) -> int {  
      |         ^  
/app/boost/include/boost/parameter/compose.hpp:84:9: note: candidate template ignored: substitution failure [with TaggedArg0 = typename ::boost::lazy_enable_if< ::boost::mp11::mp_if< ::std::is_scalar<int>, ::boost::mp11::mp_true, ::boost::mp11::mp_if< ::std::is_same<typename test_arg3::qualifier, ::boost::parameter::in_reference>, ::boost::mp11::mp_true, ::std::is_same<typename test_arg3::qualifier, ::boost::parameter::forward_reference>>>, ::boost::parameter::aux::tag<test_arg3, const int &>>::type, TaggedArgs = <boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<tags::test_arg1, boost::parameter::aux::tagged_argument_rref<tags::test_arg1, std::basic_string<char>>>, boost::parameter::aux::flat_like_arg_tuple<tags::test_arg2, boost::parameter::aux::tagged_argument<tags::test_arg2, const int>>>>]: no type named 'type' in 'boost::parameter::result_of::compose<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<tags::test_arg3, const int>>, boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<tags::test_arg1, boost::parameter::aux::tagged_argument_rref<tags::test_arg1, std::basic_string<char>>>, boost::parameter::aux::flat_like_arg_tuple<tags::test_arg2, boost::parameter::aux::tagged_argument<tags::test_arg2, const int>>>>'  
   83 |     ::compose<TaggedArg0,TaggedArgs...>::type  
      |                                          ~~~~  
   84 |         compose(TaggedArg0 const& arg0, TaggedArgs const&... args)  
      |         ^  
/app/boost/include/boost/parameter/compose.hpp:13:68: note: candidate function not viable: requires 0 arguments, but 2 were provided  
   13 |     inline BOOST_CONSTEXPR ::boost::parameter::aux::empty_arg_list compose()  
      |      
```  
  
Perhaps the value I've returned from the `ensure_param_default(...)`  is not completely an argument pack?   
  
It seemed to match the signature, I'd thought, of `(keyword = value, args)`

---

## Comment 2

> Username: Lastique  
> Created at: 2025-12-29 20:38:19 UTC  
> Updated at: 2025-12-29 22:19:10 UTC  
> Url: https://github.com/boostorg/parameter/issues/116#issuecomment-3697499729  

I'm not very familiar with Boost.Parameter internals, but parameter packs store references to the passed values to avoid copies, if possible. In your example, [this](https://github.com/boostorg/parameter/blob/9984eff746f0ceb7b553ff89699d0405a3bfa896/include/boost/parameter/keyword.hpp#L360-L364) is the `operator=` overload that is being called by `kw = ...` assignment, and the right-hand argument is an `std::string` rvalue returned by `std::invoke`. `tag<Tag,T>::type` expands [here](https://github.com/boostorg/parameter/blob/9984eff746f0ceb7b553ff89699d0405a3bfa896/include/boost/parameter/aux_/tag.hpp#L45-L65) to [`tagged_argument_rref`](https://github.com/boostorg/parameter/blob/9984eff746f0ceb7b553ff89699d0405a3bfa896/include/boost/parameter/aux_/tagged_argument.hpp#L382-L403), which stores a reference to the value internally. This means that the temporary returned by `std::invoke`/lambda is destroyed after the call to `ensure_param_default` completes, leaving the reference to the string in `args_out` dangling.  
  
Boost.Parameter parameter packs are designed to pass parameters to functions and not to be used as regular tuples. This is why storing a reference internally is appropriate, since temporaries created for a function call remain valid until the call returns.  
  
If you want to provide a default value for an argument, you can use the `param | default_value` or `param || default_func` [syntax](https://www.boost.org/doc/libs/latest/libs/parameter/doc/html/index.html#lazy-default-computation). I.e., you would write your wrapper function that accepts Boost.Parameter parameter pack with the defaults you want, then, from within that function, call Boost.Log or whatever other API you are working with underneath.  
  
Alternatively, just collect the parameters you want to pass to Boost.Log as separate variables and then pass them as named parameters to Boost.Log.

---

## Comment 3

> Username: spchamp  
> Created at: 2025-12-30 17:50:15 UTC  
> Url: https://github.com/boostorg/parameter/issues/116#issuecomment-3700067268  

Thanks for the clarification!   
  
fwiw I've updated the test cases to use a visitor pattern,  then accepting a visitor function, such that the visitor function would be provided with the argument pack. The test cases can then be run within the visitor function.    
  
I'm certain that it may be fairly indecorous, in source form.  The updated functions would accept both a function for the visitor and a function for eagerly computing a default param value, when applicable, as well as an object representing the param keyword.  The visitor pattern appears to keep the objects in scope, however, such as when  returned from the default callback then provided to the visitor function within an argument pack.  
  
Certainly, I'll look at other approaches for this, probably focusing  more on tagged args than on arbitrary keywords lol. Thanks for the reference illustrating the `||` operator here.
