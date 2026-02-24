# #185 - [Feature request] Integrate boost::pfr for static interface [Closed]

> Username: denzor200  
> Created at: 2023-11-25 22:53:59 UTC  
> Updated at: 2024-04-18 13:52:02 UTC  
> Closed at: 2024-04-18 13:52:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/185  

The Boost.MySQL library already introduced using boost::describe for it. There is also another one reflection library which provides everything we need for static interface: https://github.com/boostorg/pfr  
  
And the key benefit is no more need for `BOOST_DESCRIBE_STRUCT`  
  
Don't hesitate to ask any question regarding PFR, my pleasure to provide as much help with development as it required.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-11-27 16:52:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1828239186  

Hi!  
  
Yes, this is definitely possible.  
  
I haven't used PFR yet, but from what I've seen, you can get struct fields by index (C++14) and field names (C++20). Unless I missed something, there is no metafunction to query whether a type `T` can be reflected by PFR except by using [`is_aggregate`](https://en.cppreference.com/w/cpp/types/is_aggregate) (C++17).  
  
The static interface currently supports two models to match query results to struct fields: by index (this is used by `std::tuple`) and by field name (this is used by Boost.Describe). Matching fields by name requires reflecting field names. With PFR we could do both, but with different standards requirements. So I see some different ways of doing this:  
* We could support field matching by index only. `static_results<RowType>` would use PFR if `RowType` hasn't Boost.Describe metadata and is an aggregate. This would work in C++14+. But it may be too limiting. We would need a C++14 way of determining if `RowType` is PFR-reflectable.  
* We could support field matching by name only. The logic would be similar to the above. This is simpler, but C++20 only. We can use `std::is_aggregate` here.  
* We can try to support both. I don't like the idea of matching by index in C++14 and by name in C++20 because it's error-prone. So we need a way to differentiate. We could go for something like `static_results<pfr_by_index<RowType>>` (C++14) and `static_results<pfr_by_name<RowType>>` (C++20). This is a little bit more typing, but more explicit. It would also prevent Describe users from getting the PFR behavior by mistake. It would also save Describe users from including PFR headers. `pfr_by_xxx` would just be tag types - `static_results<pfr_by_xxx<RowType>>::rows()` should still return a `RowType` collection. We would need some trait to do this, but it shouldn't be too difficult, and opens the doors for some interesting nice-to-have behavior.  
  
Please let me know what you think of these interfaces.  
  
Do you have the time and energy to code this yourself and submit a PR? If it's the case, I can provide additional guidance on how to do it.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: denzor200  
> Created at: 2023-11-28 00:05:19 UTC  
> Updated at: 2023-11-28 00:07:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1828841163  

Hi, Ruben! Thanks for so quick answer.  
  
Sure, I can create PR should we negotiate the interface once later. Regarding three variant you suggested:  
  
- Definitely NOT, but this sentence `static_results<RowType> would use PFR if RowType hasn't Boost.Describe metadata and is an aggregate.` sounds really nice, PFR is good candidate to be used as fallback for a probally unknown RowType.  
- Pretty nice, except suggestion to use `std::is_aggregate`. We will have a lot of false-positives should we use this trait, due to limitations(PFR not able to work with every aggretate type, for example structrures with bases are not supported). We will definitely NOT use only `std::is_aggregate` strictly relying on it. But we still can use fallback-strategy in this variant, as it was discussed in previous one, why not?  
- Pretty nice, not sure should we discuss a way to differentiate `by index` and `by name` in this topic, I believe it might be decomposed to another issue that completely not related with PFR integration. Also I see here you suggest not to use `std::is_aggregate` nor to use fallback-strategy, you suggest to let library's user control the answer of the question "whether a type `T` can be reflected by PFR" via view-like manipulator. Well, such way to query possibility of `T` to be reflected looks really attractive, here I understand you :) But it will be ended up collided by case with recursive reflection. It's impossible in the current version of Boost.MySQL, but we should not ignore that it's quite possible in the future(For example, should this library introduce json fields).  
  
Concluded it all, I still belive that fallback-based way is the best way to integrate PFR into Boost.MySQL. How could I see it in short example:  
```  
struct A { ... };  
BOOST_DESCRIBE_STRUCT(A, ... );  
  
using B = std::tuple< ... >;  
  
struct C { ... };  
// no need for BOOST_DESCRIBE_STRUCT  
  
struct E : C { ... };  
  
struct D {  
 explicit D( ... );  
};  
  
static_results<A>; // OK, boost::describe struct is acceptable as always  
static_results<B>; // OK, std::tuple is acceptable too  
static_results<C>; // OK(for C++20), not describe struct nor a tuple, so we ask boost::pfr to reflect it  
static_results<E>; // ERROR, we ask pfr as before, but it failed due to inheritance  
static_results<D>; // ERROR, we even don't ask pfr, since it's not an aggregate  
  
static_results<by_index<A>>; // OK, why not? looks redundant, but might be helpful in generic code  
static_results<by_name<A>>; // OK, as default matching for describe  
static_results<by_index<B>>; // OK, as default matching for tuple  
static_results<by_name<B>>; // ERROR, matching by name is not supported by tuple  
static_results<by_index<C>>; // OK  
static_results<by_name<C>>; // ERROR(for C++14), OK(for C++20)  
  
```  
  
Let me know if I missed something. And thanks for the attention

---

## Comment 3

> Username: anarthal  
> Created at: 2023-11-28 18:57:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1830490014  

The `by_name` and `by_index` constructs you propose are definitely interesting.  
  
This is confusing though:  
  
```  
struct A { ... };  
BOOST_DESCRIBE_STRUCT(A, ... );  
  
struct C { ... };  
// no need for BOOST_DESCRIBE_STRUCT  
  
static_results<A>; // matches by field name  
static_results<C>; // matches by index  
```  
  
They're both structs, so they must have the same behavior, regardless of the library being used. This is prone to error for Describe users: forgetting the `BOOST_DESCRIBE_STRUCT` will cause by-index field matching, which will result in an error only detectable at runtime (where previously it was detected at compile-time).  
  
So either we default to matching by name for PFR, too, or, when passing a type `T` to `static_results`, we mark it somehow to use PFR reflection (something like `static_results<use_pfr<C>>`). The fact that there is no way to check whether `T` is PFR-reflectable makes me lean towards using some sort of explicit marking.

---

## Comment 4

> Username: denzor200  
> Created at: 2023-11-29 19:29:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1832571011  

Of course, default matching for PFR have to be "by name", as it's already for Describe.   
No reason to make confusion for MySQL users

---

## Comment 5

> Username: denzor200  
> Created at: 2023-11-29 22:18:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1832789882  

BTW regarding view-like manipulator which allows explicitly mark `T` as reflectable.  
I was already trying to introduce such way for Boost Fusion (and thus for Boost Spirit), you could see it here https://github.com/boostorg/pfr/pull/107 if it's interested. But it was ended up due to recursivness inablity, so then we decided that integrating PFR as implicit fallback is a nice way for Spirit.  
  
I see nested structures are unlikely MySQL case. Well, we can move forward sticking explicit marking way, why not?.  
But it definitely will not be `use_pfr` on the Boost MySQL side, something like this `static_results<boost::pfr::view_t<C>>` would be better, and reusable. Think I should recreate that ancient PR regarding `boost::pfr::view_t` and then we will moving here, what do you think?

---

## Comment 6

> Username: anarthal  
> Created at: 2023-11-30 10:59:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1833533256  

> Of course, default matching for PFR have to be "by name", as it's already for Describe.  
Great, we agree.  
  
> `static_results<boost::pfr::view_t<C>>`  
`static_results<T>` internally creates a `std::vector<T>` to store rows. `boost::pfr::view_t<T>` inspires me that we'd be creating a vector of views, while we'd need actually a `std::vector<T>`. I think the idea of implementing the type marker in PFR is good (although if it's not possible, it can go in MySQL), but having the word "view" here is misleading.  
  
If done in MySQL, we can do something like:  
  
```  
// Trait to get the actual row type from the template arg passed to static_results  
template <class T>  
struct get_row_type { using type = T; };  
  
// Marker for PFR types  
template <class T>  
struct pfr_enabled { using type = T; };  
  
// Trait specialization  
template <class T>  
struct get_row_type<pfr_enabled<T>> { using type = T; };  
  
// In static_results. This is actually a variadic template and more complex,  
// I'm providing this snippet to illustrate  
template <class RowType>  
class static_results {  
   //  ....  
   std::vector<get_row_type<RowType>::type> rows_;  
};  
```  
  
Let me know what you think.

---

## Comment 7

> Username: denzor200  
> Created at: 2023-11-30 23:06:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1834697623  

You completely right, I agree with you. Nobody wants vector of views or vector of references, it should be `std::vector<T>`, at least in public interface. Need to think about better name for `boost::pfr::view_t`. Maybe `boost::pfr::manipulator_t`, or `boost::pfr::marker_t`?.

---

## Comment 8

> Username: denzor200  
> Created at: 2023-11-30 23:08:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1834699644  

> although if it's not possible, it can go in MySQL  
  
It's more than likely possible, of course otherwise `boost::pfr::view_t` will be implemented on the MySQL side

---

## Comment 9

> Username: denzor200  
> Created at: 2023-11-30 23:11:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1834701980  

Well, we are not interested to integrate PFR as implicit fallback anymore? Only something like `boost::pfr::view_t` would be enough?

---

## Comment 10

> Username: anarthal  
> Created at: 2023-12-01 10:07:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1835816437  

Sorry for the late answer. `boost::pfr::marker_t` looks good to me.  
  
I'd go with the explicit marker for now and see how it goes. If we find it cumbersome, we go with the fallback. Does that sound right to you? Would you like me to provide you some implementation info to get you on the right track when writing the PR?

---

## Comment 11

> Username: anarthal  
> Created at: 2023-12-01 10:07:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1835816885  

Sorry for the late answer. `boost::pfr::marker_t` looks good to me.  
  
I'd go with the explicit marker for now and see how it goes. If we find it cumbersome, we go with the fallback. Does that sound right to you? Would you like me to provide you some implementation info to get you on the right track when writing the PR?

---

## Comment 12

> Username: denzor200  
> Created at: 2023-12-01 19:36:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1836666268  

> I'd go with the explicit marker for now and see how it goes. If we find it cumbersome, we go with the fallback.  
  
Sure  
  
> Would you like me to provide you some implementation info to get you on the right track when writing the PR?  
  
Yes, it would be great, thanks

---

## Comment 13

> Username: anarthal  
> Created at: 2023-12-03 14:16:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1837496708  

You will need to add PFR support for both `static_results` (the normal interface) and `static_execution_state` (the multi-function interface). As you can image, they share a lot of code.  
  
Most of what you will be changing is located under `include/boost/mysql/detail/typing/` (support types for the static interface). Have a look at `include/boost/mysql/detail/typing/static_results_impl.hpp` and `include/boost/mysql/detail/typing/static_execution_state_impl.hpp`, too, as these are the files implementing `static_results` and `static_execution_state`.  
  
Some general comments about the infrastructure:  
* The static interface performs parsing in two steps: a metadata check, performed when MySQL sends row metadata. This ensures that the types in your struct match what the query returned, and matches field names to positions; and actual row parsing, which transforms a set of `field_view`s into the struct that the user defined.  
* `readable_field_traits.hpp` contains type traits for individual struct fields (i.e. ints, floats...). These are currently not public. You shouldn't need to touch this.  
* `pos_map.hpp` contains the structure we use to map field names to positions. We use `name_table_t`, which is a constexpr span of string views, to hold the field names in the user-supplied type, in order. An empty span means that the type doesn't support name matching (e.g. a tuple). `pos_map` is an array of `std::size_t` containing a mapping from C++ field index in the struct to field index in the query. You won't need to update these but you need to be aware of them.  
* `meta_check_context.hpp` contains utilities to perform the meta check step.  
* `row_traits.hpp` contains the `row_traits` (also private), defining what a row is. You will need to create a specialization for the PFR wrapper type you define. `row_traits` must provide:  
    * A `types` type alias, containing a type list with all the type members of your struct (used for meta checking).  
    * A `size` function containing the number of fields.  
    * A `name_table` returning a name table, as described above.  
    * A `parse` function that iterates over all struct fields, in order, and calls `parse_functor` for each of them.  
* `static_execution_state_impl` and `static_results_impl` implement the `execution_processor` interface, with a set of functions that map to protocol packets. To reduce code bloat, these are split into two: `static_<xxx>_erased_impl` (which doesn't depend on template arguments) and `static_<xxx>_impl`, which does. You may have to make minor modifications to the templated ones.  
  
You will need to create a type trait to map from a `StaticRow` type to the actual, underlying row type to be to store rows. The trait needs to be public because it will appear in some functions in the public interface. You will have to modify some of the functions described above to use this type trait.  
  
If you happen to implement the PFR type marker in MySQL, I'd advise to place all specializations in a separate file, rather than in `row_traits.hpp`, as this would allow users to selectively include the code they need.  
  
Regarding testing:  
* There are unit tests under `test/unit/test/detail/typing/`, which you will need to add to.  
* There are integration tests under `test/integration/test/static_interface.cpp`. I advise to add some spotchecks like the ones already there, checking that everything works. Note that you need a real server to run this - have a look at [this](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/tests.html).  
* Feel free to add any other extra tests you may think are useful. Testing is one of the strong points of this library.  
* We have CIs (Drone and GHA) that run on pull request. You should be able to see their results, let me know if you have issues with it.  
  
Regarding docs:  
* You'll need to update the discussion, which is quickbook. Mainly [this page](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/static_interface.html), but have a look at others that also reference Describe and add something to them, too.  
* An example could be nice, too. We already have similar stuff under `example/order_management`, so maybe you could duplicate `prepared_statements_cpp14.cpp` but using PFR.  
  
Please let me know if you have any questions or any problems arise.  
  
Thanks,  
Ruben.

---

## Comment 14

> Username: denzor200  
> Created at: 2023-12-04 21:49:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-1839540168  

Thanks for the detailed information! Give me some time to configure my workstation to work with MySQL, and then I will start working on the PR

---

## Comment 15

> Username: anarthal  
> Created at: 2024-03-27 15:37:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/185#issuecomment-2023085359  

Hi @denzor200, I've implemented this. Could you please take a look at the documentation (https://anarthal.github.io/pfr-integration/libs/mysql/doc/html/mysql/static_interface.html) and share with me your feedback?  
  
I finally implemented this as `pfr_by_name` and `pfr_by_position` because it was far simpler than `pfr<by_name<T>>`.
