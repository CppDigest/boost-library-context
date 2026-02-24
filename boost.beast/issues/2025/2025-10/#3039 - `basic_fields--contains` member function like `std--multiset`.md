# #3039 - `basic_fields::contains` member function like `std::multiset` [Closed]

> Username: dvtate  
> Created at: 2025-10-07 14:57:34 UTC  
> Updated at: 2025-10-12 08:38:37 UTC  
> Closed at: 2025-10-12 08:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3039  

With the addition of [`std::multiset::contains`](https://en.cppreference.com/w/cpp/container/multiset/contains.html) in C++20 it would make sense for `basic_fields` to add this functionality as well. [The documentation](https://www.boost.org/doc/libs/1_89_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields.html) says that "\[basic_fields\] behaves as a `std::multiset`".  
  
I was making an example demonstrating how much easier it is to do things with Boost.Beast versus some over-hyped Rust library and the `request.find("...") == request.end()` line stood out as the least intuitive part of the demo.  
  
I could send a PR if that's the right course of action to get this feature added.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-10-07 15:26:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3039#issuecomment-3377435796  

I think we should add them since they're already implemented in `http_proto` (a sans-io library we're working on):  
https://develop.http-proto.cpp.al/http_proto/reference/boost/http_proto/fields_base/exists-0e.html  
  
If you'd like to give it a try, please go ahead. Use `exists` as the function name for consistency (or should we rename `exists` to `contains` in `http_proto`? @vinniefalco)  
You can add the required test cases after this line:  
https://github.com/boostorg/beast/blob/develop/test/beast/http/fields.cpp#L478

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-10-07 16:48:48 UTC  
> Url: https://github.com/boostorg/beast/issues/3039#issuecomment-3377726015  

Adding `contains` sounds like a fine idea. If you want to remove `exists` you have to give a warning for 2 releases.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-10-07 16:58:45 UTC  
> Updated at: 2025-10-07 18:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/3039#issuecomment-3377756839  

> Adding `contains` sounds like a fine idea. If you want to remove `exists` you have to give a warning for 2 releases.  
  
There's no `exists` function in Beast at the moment, but we are about to add one. My question is: should we rename `fields_base::exists` in `http_proto` to `fields_base::contains` for consistency with STL containers?

---

## Comment 4

> Username: sehe  
> Created at: 2025-10-07 21:02:24 UTC  
> Updated at: 2025-10-07 21:04:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3039#issuecomment-3378710913  

> for consistency with STL containers?  
  
not to mention the algorithm/free functions https://en.cppreference.com/w/cpp/algorithm/ranges/contains. I would 100% expect `contains()`. The pattern is that anywhere where `.count()` used to be "abused" in boolean context, `.contains()` is the more expressive standard-sanctioned accessor (boost::program_options::variable_map, boost::flat_set, boost::url::params_base¹), similar to `size()==0 -> empty()` (and the similar free functions https://en.cppreference.com/w/cpp/iterator/empty.html and https://en.cppreference.com/w/cpp/ranges/empty.html).  
  
¹ [Live On Coliru](https://coliru.stacked-crooked.com/a/80bb7d1f17b324cd)  
  
```c++  
    boost::urls::url_view u("https://www.example.com/path?query#fragment");  
    return u.params().contains("query") ? 0 : 1;  
```
