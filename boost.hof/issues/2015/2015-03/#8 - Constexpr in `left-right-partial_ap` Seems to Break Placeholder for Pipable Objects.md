# #8 - Constexpr in `left/right/partial_ap` Seems to Break Placeholder for Pipable Objects [Closed]

> Username: therealgymmy  
> Created at: 2015-03-18 00:54:20 UTC  
> Updated at: 2015-03-18 22:34:43 UTC  
> Closed at: 2015-03-18 22:34:43 UTC  
> Url: https://github.com/boostorg/hof/issues/8  

Hi, first of all, thanks so much for your speedy response on my previous issue!  
  
It seems like the newest commit 65c8d3f8b9029423725e0ef3c6887a760a9419de broke the following code that tries to pass expression made by placeholders as a function object to a pipable object.  
  
Removing the `constexpr` from the constructor and `operator ()` in `left`, `right`, and `partial_ap` seems to fix it.  
  
The code:  
  
```  
    vector<int> vectorValues = { 1, 2, 3, 4, 5 };  
    auto filter =  
        pipable(  
            [] (auto input, auto pred) {  
                decltype(input) output(input.size());  
                output.erase(  
                    ::std::copy_if(  
                        ::std::begin(input),  
                        ::std::end(input),  
                        ::std::begin(output),  
                        pred  
                    ),  
                    ::std::end(output)  
                );  
                return output;  
            }  
        );  
  
    vectorValues  
        | filter(_1 > 1);  
```  
  
I get the following error message on Clang3.5  
  
```  
./fit/placeholders.h:245:40: error: call to implicitly-deleted copy constructor of 'fit::detail::pipe_closure<(lambda at test.cpp:103:13), fit::detail::pack_base<fit::detail::seq<0>,  
      fit::detail::lazy_invoker<fit::operators::greater_than, fit::detail::pack_base<fit::detail::seq<0, 1>, fit::detail::simple_placeholder<1>, int> > &&> >'  
    constexpr partial_ap(const T& x) : val(x)  
                                       ^   ~  
./fit/placeholders.h:294:23: note: in instantiation of member function 'fit::detail::unamed_placeholder::partial_ap<fit::detail::pipe_closure<(lambda at test.cpp:103:13),  
      fit::detail::pack_base<fit::detail::seq<0>, fit::detail::lazy_invoker<fit::operators::greater_than, fit::detail::pack_base<fit::detail::seq<0, 1>, fit::detail::simple_placeholder<1>, int> > &&> >,  
      fit::detail::unamed_placeholder::right<fit::operators::bit_or> >::partial_ap' requested here  
FIT_FOREACH_BINARY_OP(FIT_UNAMED_PLACEHOLDER_BINARY_OP)  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-03-18 16:07:55 UTC  
> Url: https://github.com/boostorg/hof/issues/8#issuecomment-83036761  

i'll look into this.

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-03-18 18:18:22 UTC  
> Url: https://github.com/boostorg/hof/issues/8#issuecomment-83108086  

Ok so it seems to be a bug in clang. It works on gcc. Anyways I added a fix for it, and added this to the tests. If all the tests pass in travis, I'll merge it in.

---

## Comment 3

> Username: therealgymmy  
> Created at: 2015-03-18 20:49:44 UTC  
> Url: https://github.com/boostorg/hof/issues/8#issuecomment-83167190  

@pfultz2 thank you so much!
