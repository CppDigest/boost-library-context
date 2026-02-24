# #451 - User-selectable hashes for hana::map [Open]

> Username: iolojz  
> Created at: 2019-07-08 18:22:03 UTC  
> Updated at: 2020-06-13 21:48:06 UTC  
> Url: https://github.com/boostorg/hana/issues/451  

I am currently implementing a container that models elements in polynomial rings. To this end, it makes sense to use a `hana::map` providing a mapping from tuples of _variables_ to _coefficients_ (in the mathematical sense, we have chosen a basis). Now, the most reasonable way of implementing tuples of variables is to put variables into a `hana::tuple`, but these cannot be used as keys of a `hana::map` because they are not `Hashable`.  
What is the rationale for not making `hana::tuple` `Hashable`? I suspect, because there would be many ways to do so and in different contexts, different ways might be superior. Hence, I also do not want to roll my own hashing function and essentially make it the only one available.  
This is probably a general issue though! Thus, to me it makes sense to allow a user to _choose_ which hashing function to use, in the case there are multiple to choose from.  
  
Granted, this does not play well with the current interface. Hence, it might be a bad idea after all!  
Another solution to my issues would be to roll my own tuple or to provide specializations to `hana::tuple`s containing my specific kind of elements. Both of these ideas are bad, though, because they just obfuscate things. Also, quite an amount of code is required to make a user-defined type a `Sequence` or `MonadPlus` (as in my case). Thus wrapping the tuple or the elements amounts to big code chunks for just getting a hashing function to work :/

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-07-08 18:35:15 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-509341914  

Could you just use the type of the tuple? e.g. `hana::type<hana:tuple<x, y, z>>`  
  
`hana::type` is `Hashable`.  
  
Note that the lookup for the values in `hana::map` pay no consideration to any run-time state.

---

## Comment 2

> Username: iolojz  
> Created at: 2019-07-08 18:42:53 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-509344671  

That sounds like an awfully simple solution! Thanks :)

---

## Comment 3

> Username: vinipsmaker  
> Created at: 2020-06-13 16:39:26 UTC  
> Updated at: 2020-06-13 16:40:02 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-643647418  

> to put variables into a `hana::tuple`, but these cannot be used as keys of a `hana::map` because they are not `Hashable`.  
  
I just stumbled upon this problem exactly.  
  
I wrote a “`json::scanf()`” function that works like this:  
  
```cpp  
auto input = R"({"foo": {"bar": 3, "baz": 4}, "bar": 5})";  
int bar = 0;  
int baz = 0;  
int qux = 0;  
json::scan(  
    input,  
    hana::make_map(  
        hana::make_pair(  
            "foo"_s,  
            hana::make_map(  
                hana::make_pair("bar"_s, std::ref(bar)),  
                hana::make_pair("baz"_s, std::ref(baz))  
            )),  
        hana::make_pair("bar"_s, std::ref(qux))  
    )  
);  
TRIAL_PROTOCOL_TEST_EQUAL(bar, 3);  
TRIAL_PROTOCOL_TEST_EQUAL(baz, 4);  
TRIAL_PROTOCOL_TEST_EQUAL(qux, 5);  
```  
  
This interface works very well if you want to reuse the implementation of `json::scan()` inside other metaprograms that do something similar while exposing a different interface. One of the interfaces I want to provide is:  
  
```cpp  
json::scan(  
    input,  
    hana::make_tuple("foo"_s, "bar"_s), bar,  
    "bar"_s, qux);  
```  
  
As it can be noted, it'd be straight-forward to implement this function on top of the first one (which already works) by just inserting each pair from the variadic arguments into a map before the call forward.  
  
I did write a snippet of code to normalize the following map:  
  
```cpp  
hana::make_map(  
    hana::make_pair(hana::make_tuple("foo"_s, "bar"_s), std::ref(bar)),  
    hana::make_pair(hana::make_tuple("foo"_s, "baz"_s), std::ref(baz)),  
    hana::make_pair(  
        "foo"_s,  
        hana::make_map(  
            hana::make_pair("qux"_s, std::ref(qux))  
        )  
    ),  
    hana::make_pair(  
        hana::make_tuple("foobar"_s),  
        std::ref(foobar)  
    )  
)  
```  
  
Into:  
  
```cpp  
hana::make_map(  
    hana::make_pair(  
        "foo"_s,  
        hana::make_map(  
            hana::make_pair("bar"_s, std::ref(bar)),  
            hana::make_pair("baz"_s, std::ref(baz)),  
            hana::make_pair("qux"_s, std::ref(qux))  
        )),  
    hana::make_pair("foobar"_s, std::ref(foobar))  
)  
```  
  
But Boost.Hana doesn't even accept the source input because a tuple of `Hashable` values is not itself `Hashable`. For me, that's not just an implementation detail of my algorithm as it may be easier for my users to write their metaprograms by producing the input spec in the first format and I want to have them covered.  
  
Therefore, I also would like to know:  
  
> What is the rationale for not making `hana::tuple` `Hashable`?  
  
Can we make `Hashable` the `tuple`s whose elements are already `Hashable`?

---

## Comment 4

> Username: ricejasonf  
> Created at: 2020-06-13 19:54:42 UTC  
> Updated at: 2020-06-13 19:56:21 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-643670261  

There is no real reason for `hana::tuple` to not be Hashable, but how would it be implemented?  
Would you hash it based on its type? You can use already use `hana::typeid_` which idempotently wraps it in a `hana::type` stripping reference and const qualifiers.  
```  
hana::make_pair(hana::typeid_(anything), value));  
```  
  
If that is too cumbersome for your users, I recommend hiding it in your own interface. (if that is what you are talking about)  
  
Edit:   
>Can we make Hashable the tuples whose elements are already Hashable?  
  
Wouldn't that be an expensive check? Why not just use its type?

---

## Comment 5

> Username: vinipsmaker  
> Created at: 2020-06-13 20:06:15 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-643671532  

> Why not just use its type?  
  
I saw your hint before raising my concern, but, as I stated previously:  
  
> For me, that's not just an implementation detail of my algorithm as it may be easier for my users to write their metaprograms by producing the input spec in the first format and I want to have them covered.  
  
---  
  
> I recommend hiding it in your own interface. (if that is what you are talking about)  
  
My interface consumes a map. I can't hide cumbersome map creation from my users.  
  
> There is no real reason for `hana::tuple` to not be Hashable, but how would it be implemented?  
  
It's a hash. It doesn't need to be perfect nor does it need to have the same implementation among different Boost.Hana releases. Why not just forward to the hash implementation from the head/back of the tuple?

---

## Comment 6

> Username: ricejasonf  
> Created at: 2020-06-13 21:48:05 UTC  
> Url: https://github.com/boostorg/hana/issues/451#issuecomment-643682542  

I think it does have to be a perfect hash if you want to use `make_map`.   
  
From the [documentation](https://www.boost.org/doc/libs/1_73_0/libs/hana/doc/html/structboost_1_1hana_1_1map.html#a409d52de54cc7e55eca1a7b5dda4ce30):  
  
>make<map_tag> requires all the keys to be unique and to have different hashes. If you need to create a map with duplicate keys or with keys whose hashes might collide, use hana::to_map or insert (key, value) pairs to an empty map successively. However, be aware that doing so will be much more compile-time intensive than using make<map_tag>, because the uniqueness of keys will have to be enforced.
