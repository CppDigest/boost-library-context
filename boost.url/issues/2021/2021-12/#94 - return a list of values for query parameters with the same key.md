# #94 - return a list of values for query parameters with the same key [Closed]

> Username: AlexandreBossard  
> Created at: 2021-12-29 16:51:55 UTC  
> Updated at: 2022-03-16 17:22:03 UTC  
> Closed at: 2022-03-16 17:22:02 UTC  
> Url: https://github.com/boostorg/url/issues/94  

There is no way to get the whole list of values when you have query like `key1=a&key1=b&key1=c`.  
The only cumbersome way to retrieve all the values is to use a snippet of the like  
```  
for (auto it = params.find(key); it != params.end(); it = params.find(it, key))  
{  
  myVector.push_back(*it);  
}  
```  
Which is cumbersome.  
It would be nice to have an API that make possible to return all values associated with a key in a list/container  
```  
std::vector<string_view> url::get_all(string_view key);  
```  
I understand there are allocation problems, but I'm sure there is an acceptable solution with some trade off (provide an allocator, or templated/conformant container) to make the library easier and more accessible to use (sometimes, you need it to work).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-29 17:03:47 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1002691233  

This would be a nice example

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-12-29 17:11:50 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1002694909  

If we do this in the library it would be something like  
```  
subrange params_encoded_view::find_all( string_view key );  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-02-22 06:16:41 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1047461571  

> It would be nice to have an API that make possible to return all values associated with a key in a list/container  
  
I don't think the library should provide this explicitly. Instead, with the proposed `find_all` above, you could use `std::back_insert_iterator` (C++11) or one of the new fancy range algorithms to fill in the container of your choosing, even supporting custom allocators.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-02-22 06:17:21 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1047461919  

boost.http.proto supports this:  
https://github.com/CPPAlliance/http_proto/blob/dc92ac4e05ad8b1b4b60ec8924b2f0cfa1167598/include/boost/http_proto/fields_view.hpp#L243

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-16 15:01:16 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1069224142  

Cons:  
  
1) It would be like emulating [map::equal_range](`https://en.cppreference.com/w/cpp/container/map/equal_range`) to a container where equal keys are not in order. We also have [`unordered_map::equal_range`](https://en.cppreference.com/w/cpp/container/unordered_map/equal_range) but the _equal_ keys are also in sequence in these containers.  
2) To emulate something that's not there, we would need a view that would be a lot like [`ranges::views::filter`](https://ericniebler.github.io/range-v3/filter_8hpp.html) and pretend this is native. This would need a reasonable API, etc... which could involve a lot of work to get right.  
3) There are many possible designs for the API, like a `std::back_insert_iterator`. Each design would serve only a subset of users.  
4) Doing that implicitly might indicate to the user this happens in constant time, like some kind of weird `vector::push_front` would.  
5) This is useful for avoiding a for-loop. But seems like a lot of work for just avoiding a for-loop. Users would need to understand the design.  
  
Pros:  
  
On the other hand, `params::find` does the same thing, assuming linear time is justifiable for URLs. Since `params::find_all` would also be linear time, it's not something completely without a precedent.   
  
However, `params::find` has a much simpler interface, since we just return one iterator that already exists, so the precedent is not completely there.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-03-16 17:22:02 UTC  
> Url: https://github.com/boostorg/url/issues/94#issuecomment-1069378294  

we can just wait until a user requests it.
