# #105 - Provide a proper equivalent of mpl::eval_if [Open]

> Username: Lastique  
> Created at: 2025-01-28 22:20:27 UTC  
> Updated at: 2025-01-30 17:35:38 UTC  
> Url: https://github.com/boostorg/mp11/issues/105  

There are cases when I find `mpl::eval_if` much easier to use than `mp11::mp_eval_if`, `mp11::mp_if`, `mp11::mp_cond` and `mp11::mp_defer`. For example, converting something like [this](https://github.com/boostorg/iterator/blob/d6297a553be066043108f307b1d10c2e42fcacd6/include/boost/iterator/detail/facade_iterator_category.hpp#L79-L103) to Boost.MP11 is rather painful if one wants to preserve lazy evaluation of all `eval_if` branches in the tree. One particular pain point is that `mp_defer` does not compose with anything that takes template template parameters, including `mp_eval_if` and `mp_defer` itself.  
  
It would be helpful if Boost.MP11 provided a direct equivalent of `mpl::eval_if`. Specifically, a metafunction that would lazily instantiate each of the branches depending on the condition, where the branches implement the `::type` protocol. In general, I think the library could use a better support for the `::type` protocol.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-29 00:57:23 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2620362884  

As far as I can see that's just  
```  
template<class C, class T, class E> using mpl_eval_if = typename mp_if<C, T, E>::type;  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2025-01-29 01:15:16 UTC  
> Updated at: 2025-01-29 01:18:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2620382166  

No, that won't retain laziness when composed. See the linked code snippet, it goes like this:  
  
```  
eval_if<  
    cond1,  
    eval_if<  
        cond1_1,  
        true1_1,  
        false1_1  
    >,  
    eval_if<  
        cond1_2,  
        true1_2,  
        false1_2  
    >  
>::type  
```  
  
If `eval_if` is defined the way you suggest then both branches are instantiated. The intention is that only the following is instantiated:  
  
- `cond1`,  
- `cond1_1` or `cond1_2`,  
- exactly one of the `trueX_Y` or `falseX_Y` branches

---

## Comment 3

> Username: pdimov  
> Created at: 2025-01-29 01:46:53 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2620458053  

Ah, right, it needs to be  
```  
mp_if<  
    cond1,  
    mp_if<  
        cond1_1,  
        true1_1,  
        false1_1  
    >,  
    mp_if<  
        cond1_2,  
        true1_2,  
        false1_2  
    >  
>  
```  
  
Mp11 has `mp_cond` that implements the equivalent of an `if-else` chain, or `mpl::eval_if<C1, T1, mpl::eval_if<C2, T2, mpl::eval_if<C3, T3, ...`, but it can't do tree-like structures.  
  
Also, the conditions are evaluated in both cases.  
  
Although in our specific example there doesn't seem to be any need for laziness, as all the conditions and results can be instantiated without fear.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-01-29 09:56:55 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2621171429  

I would like the conditions to be instantiated only on demand, as it happens with `mpl::eval_if`. That is, if `cond1` evaluates to `true`, `cond1_2` should not be instantiated. This is not the case with `mp_if`.

---

## Comment 5

> Username: Lastique  
> Created at: 2025-01-29 10:06:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2621195181  

I managed to convert the linked snippet to [this](https://github.com/boostorg/iterator/blob/a89865752f8f220b509f9912109f24c0465913a3/include/boost/iterator/detail/facade_iterator_category.hpp#L69-L91), but it took me about half an hour to get to this, and the solution doesn't scale as you can't nest `mp_defer`. I.e. it worked in this case, but it won't work when there are deeper trees of `eval_if` conditions. And the converted code seems a bit more obscure than the `eval_if` tree.

---

## Comment 6

> Username: pdimov  
> Created at: 2025-01-29 14:00:40 UTC  
> Updated at: 2025-01-29 14:01:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2621736348  

Not sure why nesting mp_defer wouldn't work. E.g. here you aren't SFINAE-ing properly at top level because of the unconditional nested type, but I don't see why an mp_defer there wouldn't work. Instead of  
```  
struct iterator_facade_default_category  
{  
    using type = typename mp11::mp_if<...>::type;  
};  
```  
do  
```  
struct iterator_facade_default_category: mp11::mp_defer<mp11::mp_if, ...>  
{  
};  
```  
  
But all this deferral is unnecessary here; the conditions, and the results, don't fail, so there's no problem if they are instantiated eagerly. You can just do this  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = mp11::mp_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mp11::mp_cond<  
                std::is_convertible<Traversal, random_access_traversal_tag>, std::random_access_iterator_tag,  
                std::is_convertible<Traversal, bidirectional_traversal_tag>, std::bidirectional_iterator_tag,  
                std::true_type, std::forward_iterator_tag  
        >,  
        mp11::mp_if<  
                detail::conjunction<  
                    std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                    // check for readability  
                    std::is_convertible<Reference, ValueParam>  
                >,  
                std::input_iterator_tag,  
                Traversal  
        >  
    >;  
};  
```  
maybe with the conjunctions replaced with `mp_and` or `mp_any` as you already have mp11.

---

## Comment 7

> Username: Lastique  
> Created at: 2025-01-29 17:19:44 UTC  
> Updated at: 2025-01-29 17:42:22 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2622303543  

In this case, I want to avoid unnecessary instantiations for performance reasons rather than correctness. That's how MPL code works and I definitely don't want to regress compared to that. But ensuring correctness (i.e. not breaking compilation due to unwanted template instantiation) may also be a goal in other use cases. Let's assume that minimizing instantiations is a hard requirement from user.  
  
> Not sure why nesting mp_defer wouldn't work. E.g. here you aren't SFINAE-ing properly at top level because of the unconditional nested type, but I don't see why an mp_defer there wouldn't work. Instead of  
>   
> ```  
> struct iterator_facade_default_category  
> {  
>     using type = typename mp11::mp_if<...>::type;  
> };  
> ```  
>   
> do  
>   
> ```  
> struct iterator_facade_default_category: mp11::mp_defer<mp11::mp_if, ...>  
> {  
> };  
> ```  
  
This is not the issue I'm having. The issue is how to write multiple nested `eval_if`s so that only the necessary conditions and branches are instantiated. Maybe I'm missing something, so let me post what I've tried. Sorry, this is going to be a long post.  
  
Here is the original code, after converting the inner `std::conditional` to `mpl::if_` (which it should have been, to avoid instantiating its condition when not necessary) and putting it into the class body:  
  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = typename mpl::eval_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mpl::eval_if<  
            std::is_convertible<Traversal, random_access_traversal_tag>,  
            mpl::identity<std::random_access_iterator_tag>,  
            mpl::if_<  
                std::is_convertible<Traversal, bidirectional_traversal_tag>,  
                std::bidirectional_iterator_tag,  
                std::forward_iterator_tag  
            >  
        >,  
        mpl::eval_if<  
            detail::conjunction<  
                std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                // check for readability  
                std::is_convertible<Reference, ValueParam>  
            >,  
            mpl::identity<std::input_iterator_tag>,  
            mpl::identity<Traversal>  
        >  
    >::type;  
};  
```  
  
This code fulfills the requirements that I listed above - each condition and branch is only evaluated on demand. Now I try to convert it to Boost.MP11. First, let's try the direct approach, by converting each `mpl::eval_if` to `mp11::mp_defer<mp11::mp_if` compound:  
  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = typename mp11::mp_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mp11::mp_defer<  
            mp11::mp_if,  
                std::is_convertible<Traversal, random_access_traversal_tag>,  
                std::random_access_iterator_tag,  
                mp11::mp_if<  
                    std::is_convertible<Traversal, bidirectional_traversal_tag>, // (1)  
                    std::bidirectional_iterator_tag,  
                    std::forward_iterator_tag  
                >  
        >,  
        mp11::mp_defer<  
            mp11::mp_if,  
                detail::conjunction<  
                    std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                    // check for readability  
                    std::is_convertible<Reference, ValueParam>  
                >,  
                std::input_iterator_tag,  
                Traversal  
        >  
    >::type;  
};  
```  
  
This works, but the condition marked with (1) is always instantiated because its associated `mp_if` is eager. How do we fix it? My first idea was to nest `mp_defer` for that `mp_if`, like this:  
  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = typename mp11::mp_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mp11::mp_defer< // (2)  
            mp11::mp_if,  
                std::is_convertible<Traversal, random_access_traversal_tag>,  
                std::random_access_iterator_tag,  
                mp11::mp_defer< // (3)  
                    mp11::mp_if,  
                        std::is_convertible<Traversal, bidirectional_traversal_tag>,  
                        std::bidirectional_iterator_tag,  
                        std::forward_iterator_tag  
                >  
        >,  
        mp11::mp_defer<  
            mp11::mp_if,  
                detail::conjunction<  
                    std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                    // check for readability  
                    std::is_convertible<Reference, ValueParam>  
                >,  
                std::input_iterator_tag,  
                Traversal  
        >  
    >::type;  
};  
```  
  
And that doesn't work because the outer `mp_defer::type` (2) now produces either `std::random_access_iterator_tag` or the *unexpanded* inner `mp_defer` (3). This is the `mp_defer` composition problem that I was talking about. We could work around the problem by wrapping the other branches with `mp_identity` and adding an extra `::type` in the end:  
  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = typename mp11::mp_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mp11::mp_defer<  
            mp11::mp_if,  
                std::is_convertible<Traversal, random_access_traversal_tag>,  
                mp11::mp_identity< std::random_access_iterator_tag >, // <- added mp_identity  
                mp11::mp_defer<  
                    mp11::mp_if,  
                        std::is_convertible<Traversal, bidirectional_traversal_tag>,  
                        std::bidirectional_iterator_tag,  
                        std::forward_iterator_tag  
                >  
        >,  
        mp11::mp_identity<  // <- added mp_identity  
            mp11::mp_defer<  
                mp11::mp_if,  
                    detail::conjunction<  
                        std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                        // check for readability  
                        std::is_convertible<Reference, ValueParam>  
                    >,  
                    std::input_iterator_tag,  
                    Traversal  
            >  
        >  
    >::type::type;  // <- added ::type  
};  
```  
  
But this is getting even more obscure and difficult to maintain. Let's try `mp_eval_if` instead of inner `mp_if`+`mp_defer`:  
  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using type = typename mp11::mp_if<  
        detail::conjunction<  
            std::is_reference<Reference>,  
            std::is_convertible<Traversal, forward_traversal_tag>  
        >,  
        mp11::mp_defer<  
            mp11::mp_eval_if, // <- replaced mp_if with mp_eval_if  
                std::is_convertible<Traversal, random_access_traversal_tag>,  
                std::random_access_iterator_tag,  
                mp11::mp_if,  // <- removed mp_defer, as it should be implemented by mp_eval_if now  
                    std::is_convertible<Traversal, bidirectional_traversal_tag>,  
                    std::bidirectional_iterator_tag,  
                    std::forward_iterator_tag  
        >,  
        mp11::mp_defer<  
            mp11::mp_if,  
                detail::conjunction<  
                    std::is_convertible<Traversal, single_pass_traversal_tag>,  
  
                    // check for readability  
                    std::is_convertible<Reference, ValueParam>  
                >,  
                std::input_iterator_tag,  
                Traversal  
        >  
    >::type;  
};  
```  
  
But this explodes because `mp_defer` does not support wrapping `mp_eval_if` because `mp_eval_if` has a template template parameter instead of list of types. (BTW, `mp_defer<mp_defer, ...>` also doesn't work for the same reason, though this can be worked around with `mp_identity`, as shown above.)  
  
In the end I figured out to use `mp_cond` under `mp_defer` for the first branch, but as you know, it cannot be nested, so the solution doesn't scale.  
  
But do you see the problem? Most Boost.MP11 constructs are eager to instantiate their template parameters, and trying to prevent that is really painful. And `mp_defer` doesn't solve the problem as it can't compose well. So this issue asks to improve the library on this front. I'm using `mpl::eval_if` as an example here, where I think Boost.MPL does better. It would be useful to have a construct that lazily instantiates the condition and the branches and doesn't require the user to spell `mp_defer` in the process.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-01-29 17:55:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2622450296  

> Most Boost.MP11 constructs are eager to instantiate their template parameters, and trying to prevent that is really painful.  
  
Pretty much all. Mp11 is alias based, and aliases are always eager. This generally delivers better performance despite doing more instantiations. E.g. `mp_all<Cond...>` instantiates all of Cond... even if there are hundreds, but is typically still faster than the corresponding `std::conjunction`. (`mp_and` is one special case where Mp11 cares about not instantiating unless needed, but it's generally slower than `mp_all` and is only useful when you actually need to prevent errors.)  
  
Or for another example, `mp_find<L, V>` where the size of L is 100 will instantiate `std::is_same<T, V>` a hundred times, even if the first element of L is V, and is still going to be faster on average in general than the lazy implementation.  
  
The purpose of `mp_defer` isn't actually to skip instantiations. It's to convert immediate context (SFINAE) errors into a missing `::type` instead of a compile error.  
  
> But this explodes because mp_defer does not support wrapping mp_eval_if because mp_eval_if has a template template parameter instead of list of types.  
  
This is generally solved by using `mp_eval_if_q` and replacing `mp_if` with `mp_quote<mp_if>`, but it's not worth doing here.  
  
What I would do here is the "stupid" and straightforward  
```  
template< typename Traversal, typename ValueParam, typename Reference >  
struct iterator_facade_default_category  
{  
    using is_ref = std::is_reference<Reference>;  
    using is_val = std::is_convertible<Reference, ValueParam>;  
  
    template<class Tag> using is_tag = std::is_convertible<Traversal, Tag>;  
  
    using type = mp11::mp_cond<  
        mp11::mp_all<is_ref, is_tag<random_access_traversal_tag>>, std::random_access_iterator_tag,  
        mp11::mp_all<is_ref, is_tag<bidirectional_traversal_tag>>, std::bidirectional_iterator_tag,  
        mp11::mp_all<is_ref, is_tag<forward_traversal_tag>>, std::forward_iterator_tag,  
        mp11::mp_all<is_val, is_tag<single_pass_traversal_tag>>, std::input_iterator_tag>,  
        mp11::mp_true, Traversal  
    >;  
};  
```

---

## Comment 9

> Username: Lastique  
> Created at: 2025-01-30 17:35:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/105#issuecomment-2625144520  

I tried benchmarking 100 instantiations of `iterator_facade_default_category` implemented with MPL (the original version I posted in https://github.com/boostorg/mp11/issues/105#issuecomment-2622303543), my MP11 version [here](https://github.com/boostorg/iterator/blob/a89865752f8f220b509f9912109f24c0465913a3/include/boost/iterator/detail/facade_iterator_category.hpp#L69-L91) and your "straightforward" version in gcc 13.3 with `-ftime-report` and they all perform basically the same, with MPL being maybe slightly faster than the other two. Here are sample compiler reports:  
  
MPL:  
  
```  
Time variable                                   usr           sys          wall           GGC  
 phase setup                        :   0.00 (  0%)   0.01 (  8%)   0.01 (  4%)  1882k (  4%)  
 phase parsing                      :   0.13 ( 87%)   0.10 ( 83%)   0.24 ( 92%)    42M ( 89%)  
 phase lang. deferred               :   0.01 (  7%)   0.01 (  8%)   0.01 (  4%)  3227k (  7%)  
 phase opt and generate             :   0.01 (  7%)   0.00 (  0%)   0.00 (  0%)   137k (  0%)  
 |name lookup                       :   0.02 ( 13%)   0.01 (  8%)   0.07 ( 27%)  1903k (  4%)  
 |overload resolution               :   0.01 (  7%)   0.00 (  0%)   0.00 (  0%)  1828k (  4%)  
 preprocessing                      :   0.04 ( 27%)   0.04 ( 33%)   0.12 ( 46%)  5351k ( 11%)  
 parser (global)                    :   0.04 ( 27%)   0.02 ( 17%)   0.02 (  8%)    15M ( 33%)  
 parser struct body                 :   0.01 (  7%)   0.03 ( 25%)   0.02 (  8%)  7935k ( 16%)  
 parser function body               :   0.01 (  7%)   0.00 (  0%)   0.00 (  0%)   467k (  1%)  
 parser inl. func. body             :   0.02 ( 13%)   0.00 (  0%)   0.02 (  8%)  1554k (  3%)  
 parser inl. meth. body             :   0.00 (  0%)   0.00 (  0%)   0.03 ( 12%)  2863k (  6%)  
 template instantiation             :   0.02 ( 13%)   0.02 ( 17%)   0.04 ( 15%)    11M ( 25%)  
 initialize rtl                     :   0.01 (  7%)   0.00 (  0%)   0.00 (  0%)    12k (  0%)  
 TOTAL                              :   0.15          0.12          0.26           47M  
```  
  
My MP11:  
  
```  
Time variable                                   usr           sys          wall           GGC  
 phase setup                        :   0.01 (  6%)   0.00 (  0%)   0.02 (  6%)  1882k (  3%)  
 phase parsing                      :   0.15 ( 83%)   0.13 (100%)   0.28 ( 88%)    47M ( 90%)  
 phase lang. deferred               :   0.01 (  6%)   0.00 (  0%)   0.02 (  6%)  3354k (  6%)  
 phase opt and generate             :   0.01 (  6%)   0.00 (  0%)   0.00 (  0%)   147k (  0%)  
 |name lookup                       :   0.03 ( 17%)   0.02 ( 15%)   0.04 ( 13%)  1987k (  4%)  
 |overload resolution               :   0.01 (  6%)   0.00 (  0%)   0.02 (  6%)  5569k ( 10%)  
 preprocessing                      :   0.04 ( 22%)   0.05 ( 38%)   0.10 ( 31%)  5351k ( 10%)  
 parser (global)                    :   0.03 ( 17%)   0.04 ( 31%)   0.07 ( 22%)    15M ( 30%)  
 parser struct body                 :   0.00 (  0%)   0.01 (  8%)   0.02 (  6%)  7935k ( 15%)  
 parser function body               :   0.02 ( 11%)   0.01 (  8%)   0.00 (  0%)   467k (  1%)  
 parser inl. func. body             :   0.00 (  0%)   0.00 (  0%)   0.00 (  0%)  1554k (  3%)  
 parser inl. meth. body             :   0.02 ( 11%)   0.01 (  8%)   0.04 ( 12%)  2863k (  5%)  
 template instantiation             :   0.05 ( 28%)   0.01 (  8%)   0.07 ( 22%)    17M ( 33%)  
 initialize rtl                     :   0.01 (  6%)   0.00 (  0%)   0.00 (  0%)    12k (  0%)  
 TOTAL                              :   0.18          0.13          0.32           53M  
```  
  
Your MP11:  
  
```  
Time variable                                   usr           sys          wall           GGC  
 phase setup                        :   0.00 (  0%)   0.00 (  0%)   0.02 (  7%)  1882k (  3%)  
 phase parsing                      :   0.15 ( 88%)   0.12 (100%)   0.27 ( 90%)    51M ( 91%)  
 phase lang. deferred               :   0.01 (  6%)   0.00 (  0%)   0.01 (  3%)  3339k (  6%)  
 phase opt and generate             :   0.01 (  6%)   0.00 (  0%)   0.00 (  0%)   272k (  0%)  
 |name lookup                       :   0.02 ( 12%)   0.00 (  0%)   0.07 ( 23%)  2032k (  3%)  
 |overload resolution               :   0.02 ( 12%)   0.00 (  0%)   0.02 (  7%)  3889k (  7%)  
 preprocessing                      :   0.04 ( 24%)   0.06 ( 50%)   0.07 ( 23%)  5351k (  9%)  
 parser (global)                    :   0.02 ( 12%)   0.02 ( 17%)   0.06 ( 20%)    15M ( 28%)  
 parser struct body                 :   0.00 (  0%)   0.01 (  8%)   0.04 ( 13%)  7937k ( 14%)  
 parser inl. func. body             :   0.01 (  6%)   0.01 (  8%)   0.02 (  7%)  1554k (  3%)  
 parser inl. meth. body             :   0.04 ( 24%)   0.01 (  8%)   0.02 (  7%)  2863k (  5%)  
 template instantiation             :   0.05 ( 29%)   0.01 (  8%)   0.07 ( 23%)    20M ( 37%)  
 initialize rtl                     :   0.01 (  6%)   0.00 (  0%)   0.00 (  0%)    12k (  0%)  
 TOTAL                              :   0.17          0.12          0.30           56M  
```
