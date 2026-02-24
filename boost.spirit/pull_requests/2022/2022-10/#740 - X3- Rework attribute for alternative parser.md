# #740 X3: Rework attribute for alternative parser [Closed]

> Username: eido79  
> Created at: 2022-10-30 21:43:56 UTC  
> Updated at: 2025-06-28 01:09:45 UTC  
> Closed at: 2025-06-28 01:09:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/740  

Implement the following behavior, as described in #610   
* Remove duplicates  
* Simplify `variant<X>` to `X`  
* Make `unused_type` the first type of the variant  
* Transform `variant<unused_type, T>` to `optional<T>`

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2022-11-01 01:48:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/740#pullrequestreview-1162736536  

Thanks for the PR. A few things need to be resolved:  
1) I haven't made deep inspection of what could go wrong with doing `variant<unused_type, ...>`, have you? At least the question about how crude is `unused_type` in visitor will be, since it has implicit constructor from everything.  
2) A trait to configure monostate type is needed.  
3) No need to use boost type traits which are available in standard library.  
4) Put the tests in alternative.cpp/sequence.cpp please.

---

## Comment 2

> Username: eido79  
> Created_at: 2022-11-01 08:41:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/740#issuecomment-1298218712  

Hi @Kojoley,  
  
> I haven't made deep inspection of what could go wrong with doing variant<unused_type, ...>, have you? At least the question about how crude is unused_type in visitor will be, since it has implicit constructor from everything.  
  
Not really, admittedly. The fact that unused_type is constructible from everything might indeed create problems. I'll do some more analysis and try to come up with some meaningful use cases.  
  
In case this turn out to be ugly, it is anyway easy to make the behavior of `attribute_of_alternative` match the currently documented rules, i.e. use `optional<variant<...>>` when `unused_type` is involved.  
  
> A trait to configure monostate type is needed.  
  
Agreed. How do you think that should look like?  
  
> No need to use boost type traits which are available in standard library.  
> Put the tests in alternative.cpp/sequence.cpp please.  
  
OK.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-11-02 01:54:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/740#issuecomment-1299450052  

> > I haven't made deep inspection of what could go wrong with doing variant<unused_type, ...>, have you? At least the question about how crude is unused_type in visitor will be, since it has implicit constructor from everything.  
>   
> Not really, admittedly. The fact that unused_type is constructible from everything might indeed create problems. I'll do some more analysis and try to come up with some meaningful use cases.  
>   
> In case this turn out to be ugly, it is anyway easy to make the behavior of `attribute_of_alternative` match the currently documented rules, i.e. use `optional<variant<...>>` when `unused_type` is involved.  
  
I am afraid `optional<variant<...>>` might actually be not supported currently by alternative parser/is_subsitute, I couldn't find tests for it either.  
  
> > A trait to configure monostate type is needed.  
>   
> Agreed. How do you think that should look like?  
  
Something like https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/support/traits/optional_traits.hpp  
```cpp  
template <typename Variant> struct variant_monostate { using type = unused_type; };  
```

---

## Comment 4

> Username: eido79  
> Created_at: 2022-11-02 19:20:52 UTC  
> Updated_at: 2022-11-02 22:21:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/740#issuecomment-1301105602  

It's indeed ugly. Since anything is convertible to `unused_type`, things like this compile just fine  
```  
char input[] = "Whoops";  
boost::variant<x3::unused_type, int> v;  
auto first = std::begin(input);  
auto last = std::end(input) - 1;  
bool success = x3::parse(first, last, x3::int_ | +x3::char_, v);  
assert(success && first == last); // OK!  
```  
hiding potential mistakes that should be caught at compile time.  
  
At this point, I'm thinking that using `boost::blank` or another empty type would be a better choice.  
Alternatively, it could be investigated what it would take to make `optional<variant<...>>` work properly.  
  
Thoughts?

---

## Comment 5

> Username: saki7  
> Created_at: 2025-05-09 15:18:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/740#issuecomment-2866963898  

@Kojoley Is this issue still valid on current codebase?

---

## Comment 6

> Username: saki7  
> Created_at: 2025-06-28 01:09:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/740#issuecomment-3014792391  

Closing this for several reasons:  
  
1. The PR is stale, and no improvements were made since 2022.  
2. See my comments on https://github.com/boostorg/spirit/issues/610#issuecomment-3014781535

---
