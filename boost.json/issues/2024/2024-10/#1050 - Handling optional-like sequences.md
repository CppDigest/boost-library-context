# #1050 - Handling optional-like sequences [Closed]

> Username: brevzin  
> Created at: 2024-10-07 16:56:08 UTC  
> Updated at: 2025-07-02 20:38:02 UTC  
> Closed at: 2025-07-02 20:38:02 UTC  
> Url: https://github.com/boostorg/json/issues/1050  

As Andrzej pointed out recently in a draft paper on the Library Evolution reflector, the JSON conversion heuristics currently prioritize checking sequence-like before checking optional-like. This means that any optional-like types that are ranges will serialize like a sequence (`[42]` and `[]` instead of `42` and `null`) and fail to deserialize (due to lack of `push_back` or `insert`), and, more importantly, an optional type that later adopts a range interface will break any users that use Boost.JSON (without even knowing if any such users exist).   
  
There are [multiple](https://www.scala-lang.org/api/2.13.3/scala/Option.html) [other](https://hackage.haskell.org/package/base-4.20.0.1/docs/Data-Maybe.html#t:Maybe) [languages](https://doc.rust-lang.org/std/option/enum.Option.html) whose optional type is a sequence, so it's not far-fetched to suggest that a C++ one might exist also (regardless of whether `std::optional` ends up being one or not).  
  
There are at least two ways to preemptively address this issue that I can think of (though there are probably more solutions):  
  
1. Prioritize checking optional-like over sequence-like.   
2. Have sequence-like just assert against optional-like (on the premise that you meet two criteria we don't know what you mean, so we're not going to guess - this would make (de)serialization ill-formed, but ill-formed is better than well-formed and wrong)  
  
I think an `optional<pair<string, T>>` that is a range is also [very nearly map-like](https://godbolt.org/z/MWors36Pc), only rejected by `emplace` probably not returning tuple-like. But it hypothetically could also (maybe you return the previous state as a `bool`?), so some care needs to be taken there too.   
  
---  
  
I think a similar question might be asked of described classes that are also ranges. How should those be serialized, as objects or as arrays?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-07 17:06:23 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2397459362  

> I think a similar question might be asked of described classes that are also ranges. How should those be serialized, as objects or as arrays?  
  
Today as described classes, because description is inherently opt-in, but tomorrow, when reflection arrives, the answer may need to change.

---

## Comment 2

> Username: brevzin  
> Created at: 2024-10-07 17:12:50 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2397471194  

> > I think a similar question might be asked of described classes that are also ranges. How should those be serialized, as objects or as arrays?  
>   
> Today as described classes, because description is inherently opt-in, but tomorrow, when reflection arrives, the answer may need to change.  
  
[That's not the case right now](https://godbolt.org/z/c1d86areE):  
  
```cpp  
template <bool B>  
struct X {  
    int m;  
  
    // admittedly this is silly, but it's just an example  
    int* begin() requires B { return &m; }  
    int* end() requires B { return &m + 1; }  
  
    BOOST_DESCRIBE_CLASS(X, (), (m), (), ());  
};  
  
int main() {  
    // {"m":1}  
    SHOW(boost::json::value_from(X<false>{.m=1}));  
    // [1]  
    SHOW(boost::json::value_from(X<true>{.m=1}));  
}  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-10-07 17:18:46 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2397481700  

Since we require all members public for described classes, this basically leads us again to `std::array`. Not only is it both a tuple and a sequence, but a described class too. :-)

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-10-07 17:18:47 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2397481733  

Thank you for the issue. I'll review the conversion category ranking.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-10-07 17:21:26 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2397486796  

The more I think about this, the more it seems to me that when a type matches more than one of these categories, the result should be a compile error. Choosing one of them is rarely the best course of action, all things considered.

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-09 13:43:12 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2402390307  

So, if a type matches several categories, the user would have to define all but one to derive from `false_type`?

---

## Comment 7

> Username: brevzin  
> Created at: 2024-10-09 13:56:08 UTC  
> Updated at: 2024-10-09 13:57:07 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2402422542  

> So, if a type matches several categories, the user would have to define all but one to derive from `false_type`?  
  
I think instead of having N independent checks that are either true or false, you could have one variable template whose value is an enum that specifies the conversion kind. By default `conversion_kind<T>` walks through that ladder of heuristics.   
  
Then the user can just specialize the enum directly to do what they want.   
  
e.g. for the [ip address example](https://www.boost.org/doc/libs/1_86_0/libs/json/doc/html/json/conversion/custom_conversions.html), which already demonstrates exactly the problem this issue is talking about... instead of specializing `is_sequence_like<ip_address>` to `false` (which doesn't really convey user intent at all), they would specialize `conversion_category<ip_address>` to `conversion_kind::tuple_like` (which does). Also as a bonus the user is informed they have to do this by having an ambiguous json category, rather than by seeing wrong output at runtime (or a compile error on deserialization).

---

## Comment 8

> Username: pdimov  
> Created at: 2024-10-09 14:20:26 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2402486213  

> I think instead of having N independent checks that are either true or false, you could have one variable template whose value is an enum that specifies the conversion kind.  
  
It's not really "instead of" because specializing an independent check means something different; it means "my type is detected as X, but it isn't X" (or vice versa).  
  
Whereas what we have here is that the individual checks are correct, the type is detected as X and Y because it's really X and Y, and we have to choose one for the representation.

---

## Comment 9

> Username: brevzin  
> Created at: 2024-10-09 14:23:34 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2402494253  

> > I think instead of having N independent checks that are either true or false, you could have one variable template whose value is an enum that specifies the conversion kind.  
>   
> It's not really "instead of" because specializing an independent check means something different; it means "my type is detected as X, but it isn't X" (or vice versa).  
>   
> Whereas what we have here is that the individual checks are correct, the type is detected as X and Y because it's really X and Y, and we have to choose one for the representation.  
  
Right, the enum approach would be the answer to how you "choose one" for the representation. I'd rather affirmatively say "my type should be represented as an X" rather than say the negative "my type is not a Y" and continue to have nowhere in code that indicates that it's an X.

---

## Comment 10

> Username: beached  
> Created at: 2024-10-17 12:58:16 UTC  
> Url: https://github.com/boostorg/json/issues/1050#issuecomment-2419479713  

> Since we require all members public for described classes, this basically leads us again to `std::array`. Not only is it both a tuple and a sequence, but a described class too. :-)  
  
std::array is why serialization via reflection should generally be opt-in and/or happen after other category checks; array was the first gotcha I ran into when doing unrestricted reflection.   There's no real way to be ergonomic and not do category checks/requirements as things like variants have at least 3 ways to be represented in JSON that are common(submember like type, other member to determine type, the shape of the variant in serialized form(e.g. existence of members or just the basic JSON type string/bool/class/array).  Enum's as int's has been my default without help because that is what we have in a pre-C++26 world(the underlying type).  For reflection I added a mapping that defaults the to and from string conversions to the reflected names of the enumerators.     
  
But even simple things like numbers are not going to be easy for the user when half the numbers are encoded as strings for various reasons.  There is loads of JSON in the wild like `[1,2,"3", "42", 52]` where the intent will be to generate a container of integers, not a heterogenous container of integers and strings.  Or some combination of strings/numbers.  
  
Regarding the optional, it fits into a bigger category of Nullable/Option types that are close but not the same.  Specifically, constructing the result is different for things like shared_ptr/optional/raw pointers/... and I had to create a trait for this.  I did prioritize it over other categories because that is how JSON is normally reflected here.   Changing that is easy though as the categories can be specified by the user through the mapping and in 26 via attributes too.   `[]` and `null` really are not the same thing and encoding an empty optional like that is just weird.  seeing `"member": 1`/`"member": []` or `"member": [1]`/"member": []` would be surprising.  People will want `"member": 1`/`"member": null` or `"member": 1`/no member generated for empty with an option to choose which encoding.  I have seen orgs that want everything encoded, null or not.   
  
I have a trait for containers, but things mostly we can check that with begin/end/insert and that covered most cases; std::array stands out here.  But this comes back to how to construct things is a common enough question that I had to make it a part of the api with some good defaults.
