# #4 - not possible to return different types from hana::if_ statement? [Closed]

> Username: kris-jusiak  
> Created at: 2014-08-05 14:19:59 UTC  
> Updated at: 2014-09-16 15:30:27 UTC  
> Closed at: 2014-09-16 15:30:27 UTC  
> Url: https://github.com/boostorg/hana/issues/4  

```  
struct fold_and_add_type_list {  
    using type = decltype(  
        hana::foldl(  
            [](auto result, auto t) {  
                return hana::if_(  
                    true  
                  , hana::snoc(result, t)  
                  , result  
                );  
            }  
          , hana::type_list<>  
          , hana::type_list<int, double>  
        )  
    );  
};  
```  
  
../include/boost/hana/logical/logical.hpp:215:25: error: incompatible operand types ('boost::hana::tlist_detail::type_list<int>::type' and 'boost::hana::tlist_detail::type_list<>::type')  
            return cond ? t(id) : e(id);

---

## Comment 1

> Username: ldionne  
> Created at: 2014-08-05 15:23:12 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51213047  

If you think about it for a moment, what happens here is a limitation of the language, not a limitation of Hana. Your problem lies in the following code:  
  
```  
[](auto result, auto t) {  
    return hana::if_(  
        true  
      , hana::snoc(result, t)  
      , result  
    );  
}  
```  
  
You're trying to write a function returning objects of different types depending on a runtime condition, i.e. `true`. Of course, this can't possibly work, just like the following can't work:  
  
```  
[](auto result, auto t) {  
    return true ? hana::snoc(result, t) : result;  
}  
```  
  
For this to work, both sides of the `?:` operator would need a common type, which is not the case here. That's why you get that error from Clang. That being said, you can encode the fact that your condition is known at compile-time by using `hana::true_`, which is some kind of boolean `std::integral_constant`. Note that I'm about to push some improvements to the documentation which makes this much clearer, so you might want to have a look.  
  
Now, there's one last thing; lambdas can't appear in unevaluated contexts (e.g. decltype, sizeof, etc..), so you have to create a variable to hold the result. The following code compiles:  
  
```  
auto fold_and_add_type_list_impl = hana::foldl(  
    [](auto result, auto t) {  
        return hana::if_(  
            hana::true_  
          , hana::snoc(result, t)  
          , result  
        );  
    }  
  , hana::type_list<>  
  , hana::type_list<int, double>  
);  
  
struct fold_and_add_type_list {  
    using type = decltype(fold_and_add_type_list_impl);  
};  
```  
  
A couple of notes:  
1. I suspect this is not what you want, because `decltype(fold_and_add_type_list_impl)` is not something very meaningful; it's the type of some `type_list<...>`, which is unspecified.  
2. I agree that it is very annoying to have to use a dummy variable just to create a type. In fact, it is pretty much inacceptable if you ask me. However, type computations in Hana are meant to be done with variables from top to bottom. You should use `decltype(...)::type` only when you actually need the result of a type computation as a C++ type, i.e. at a library boundary.  
  
Could you provide more context about what you're trying to achieve?

---

## Comment 2

> Username: kris-jusiak  
> Created at: 2014-08-05 17:51:12 UTC  
> Updated at: 2014-08-05 18:34:09 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51233916  

thanks for the great explanation, still a bit tricky for me to remember about 'bool_' types without '<>',   
especially with if_. What I assumed was that if_ statement type is the type returned itself and its lazy evaluated afterwards, but yea, I totally understand your explanation, no issues here. Anyway overall experience is so much better already, although the are tricky things as well :)  
  
> Could you provide more context about what you're trying to achieve?  
  
So, I do have this nasty - probably not the best either - code to detects if class has constructor with given number of parameters (from 1 to 10) and returns vector with 'any_type' types accordingly   
  
```  
  
template<typename T>  
struct ctor_traits  
    : mpl::second<  
          typename mpl::fold<  
              mpl::range_c<int, 1, 10>  
            , mpl::pair<  
                  mpl::vector<any_type<T> >  
                , mpl::vector<>  
              >  
            , mpl::if_<  
                  type_traits::has_ctor<  
                      T  
                    , mpl::normalize_vector<mpl::first<mpl::_1> >  
                  >  
                , mpl::pair<  
                      mpl::push_back<mpl::first<mpl::_1>, any_type<T> >  
                    , mpl::first<mpl::_1>  
                  >  
                , mpl::pair<  
                      mpl::push_back<mpl::first<mpl::_1>, any_type<T> >  
                    , mpl::second<mpl::_1>  
                  >  
              >  
          >::type  
      >  
{ };  
```  
  
and hana equivalent below:  
  
```  
template<typename T>  
struct ctor_traits {  
    using type = decltype(  
        hana::foldl(  
            [](auto result, auto t) {  
                return hana::if_(  
                    hana::trait<has_ctor>(hana::type<T>, hana::first(result))  
                  , hana::pair(hana::snoc(hana::first(result), hana::type<any_type<T>>), hana::first(result))  
                  , hana::pair(hana::snoc(hana::first(result), hana::type<any_type<T>>), hana::second(result))  
                );  
            }  
          , hana::pair(hana::type_list<any_type<T>>, hana::type_list<>)  
          , hana::range(hana::int_<1>, hana::int_<10>)  
        )  
    );  
};  
```  
  
basically it works, but parameters passed to has_ctor are not exactly the ones which should be, guess have to unpack them, still learning...  
  
```  
has_ctor<some_type, boost::hana::tlist_detail::type_list<any_type<some_type>>::type>'  
```  
  
still not sure, if with hana I should have different approach, for now just trying to port it to hana and then will try to figure out better solutions

---

## Comment 3

> Username: ldionne  
> Created at: 2014-08-05 18:37:33 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51240407  

I'm very interested at your use case. What should `has_ctor` receive as arguments? Is it equivalent to `std::is_constructible`?  
  
As a side note, I'm very surprised that Clang compiles your example even though it contains a lambda in an unevaluated operand. I think that's a bug, but I really like it because then we don't have to create a dummy variable to hold the lambda.

---

## Comment 4

> Username: ldionne  
> Created at: 2014-08-05 19:10:05 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51244672  

Also, here's a small refactoring taking advantage of the fact that we can have local variables in lambdas:  
  
``` cpp  
template<typename T>  
struct ctor_traits {  
    using type = decltype(  
        hana::foldl(  
            [](auto result, auto t) {  
                auto which = hana::if_(  
                    hana::trait<has_ctor>(hana::type<T>, hana::first(result)),  
                    hana::first,  
                    hana::second  
                );  
                return hana::pair(  
                    hana::snoc(hana::first(result), hana::type<any_type<T>>),  
                    which(result)  
                );  
            }  
          , hana::pair(hana::type_list<any_type<T>>, hana::type_list<>)  
          , hana::range_c<int, 1, 10>  
        )  
    );  
};  
```  
  
Note that I just introduced `hana::range_c` in a7b38d3, so you'd have to update your local copy.

---

## Comment 5

> Username: kris-jusiak  
> Created at: 2014-08-05 20:01:59 UTC  
> Updated at: 2014-08-05 20:28:56 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51251344  

> I'm very interested at your use case. What should has_ctor receive as arguments? Is it equivalent to std::is_constructible?  
  
actually it should be an equivalent since C++11, so yea, it should takes:  
  
```  
std::is_constructible<some_type, any_type<some_type>, any_type<some_type>...>  
```  
  
> As a side note, I'm very surprised that Clang compiles your example even though it contains a lambda   
> in an unevaluated operand. I think that's a bug, but I really like it because then we don't have to   
> create a dummy variable to hold the lambda.  
  
Yea, its handy, no idea if thats should compile or not tho.  
  
> Also, here's a small refactoring taking advantage of the fact that we can have local variables in   
> lambdas:  
  
Yea, I have noticed that today, lambdas are so amazing, love the experience with hana so far :)

---

## Comment 6

> Username: kris-jusiak  
> Created at: 2014-08-06 11:05:06 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51320738  

Below version seems to be working, unfortunately clang is crashing when upper range value is higher than 5 :/ but oh well, going to adopt other mpl classes to use hana and hopefully clang whole project in the end, but we shall see.  
  
```  
template<typename T>  
struct ctor_traits {  
    using type = decltype(  
        hana::second(  
            hana::foldl(  
                hana::range_c<int, 1, 5>  
              , hana::pair(hana::type_list<any_type<T>>, hana::type_list<>)  
              , [](auto result, auto) {  
  
                    auto is_construcrible =  
                        hana::partial(hana::flip(hana::unpack), hana::trait<std::is_constructible>);  
  
                    auto which = hana::if_(  
                        is_construcrible(hana::cons(hana::type<T>, hana::first(result)))  
                      , hana::first  
                      , hana::second  
                    );  
  
                    return hana::pair(  
                        hana::snoc(hana::first(result), hana::type<any_type<T>>)  
                      , which(result)  
                    );  
                }  
            )  
        )  
    );  
};  
```

---

## Comment 7

> Username: ldionne  
> Created at: 2014-08-06 15:23:58 UTC  
> Updated at: 2014-08-06 15:30:23 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-51350252  

It's curious, because it works for values up to 40 for me (it's too slow though).  
That being said, I'm not sure why you're storing the type of the result. Since `type_list` has an unspecified type, it's pretty much useless. Instead, here's what I would do:  
  
``` cpp  
template <typename T>  
auto ctor_traits = hana::second(  
    hana::foldl(  
        hana::range_c<int, 1, 10>  
      , hana::pair(hana::type_list<any_type<T>>, hana::type_list<>)  
      , [](auto result, auto) {  
            auto which = hana::if_(  
                is_constructible(hana::cons(hana::type<T>, hana::first(result)))  
              , hana::first  
              , hana::second  
            );  
  
            return hana::pair(  
                hana::snoc(hana::first(result), hana::type<any_type<T>>)  
              , which(result)  
            );  
        }  
    )  
);  
```  
  
Note, however, that this about 1.8x slower, probably because the compiler has to do more work than when it only has to `decltype`. I'm adding a TODO to fix this performance issue.

---

## Comment 8

> Username: ldionne  
> Created at: 2014-09-16 15:30:27 UTC  
> Url: https://github.com/boostorg/hana/issues/4#issuecomment-55761213  

Closing this since it's not really an issue, but rather a question that I feel has been answered.
