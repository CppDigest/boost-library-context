# #604 - Feature: Add at(idx) and/or subscript / array index operator for the segments_view range [Closed]

> Username: meastp  
> Created at: 2022-10-19 07:02:11 UTC  
> Updated at: 2022-10-20 21:14:12 UTC  
> Closed at: 2022-10-19 18:52:43 UTC  
> Url: https://github.com/boostorg/url/issues/604  

Thank you for a great library! Just in time for our move to Boost.Beast :)  
  
I think it is a common operation to extract or check certain parts of the path/segment, at least we do (e.g. extracting the second segment which should be an id, verify and use this id).  
  
Is it possible to extend the segments_view range with either  
- an at() method to extract a segment at a certain index  
- subscript / array index operator [] to extract a segment at a certain index  
  
perhaps both?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-10-19 14:14:24 UTC  
> Updated at: 2022-10-19 14:16:06 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1284087942  

Thanks for the kind words!  
  
What about using `std::next`? Because that is what we would basically be doing. The segments views do not offer O(1) random access, so we do not provide index-based operations. If you really need that, the guidance is to copy the strings into your own container, e.g. `vector<string>` or `vector<pct_string_view>`. What do you think?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-10-19 18:34:41 UTC  
> Updated at: 2022-10-19 18:37:19 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1284419267  

> Is it possible to extend the segments_view range with either   
>  
> an at() method to extract a segment at a certain index  
> subscript / array index operator [] to extract a segment at a certain index perhaps both?  
  
The segment and parameter references and views are bidirectional, so the `at`/`operator[]` would be misleading as they might lead users to believe this operation has constant cost.   
  
The biggest problem is users might try to use this operator to iterate the segments with indices:  
  
```cpp  
auto ss = u.segments();  
// this loop has a quadratic cost  
for (std::size_t i = 0; ss.size(); ++i) {  
        std::cout << ss[i] << "\n"; // because this operation has linear cost  
}  
```  
  
which is misleading because this has quadratic cost in bidirectional iterators. This is problematic and a safety issue in many times. On the other hand, range-based loops are fine:  
  
```cpp  
auto ss = u.segments();  
// this loop has a linear cost  
for (auto s: ss) {  
        std::cout << ss[i] << "\n";   // because this operation has constant cost  
}  
```  
  
In other words, these views are less like `std::vector` and more like `std::list`, and these operators are not available for the same reasons.  
  
Now considering your use case:  
  
> extract or check certain parts of the path/segment  
  
This might refer to one, many, or all parts. But let's consider  
  
> extracting the second segment which should be an id, verify and use this id  
  
The best alternative here, as Vinnie said, is `std::next`:  
  
```cpp  
auto ss = u.segments();  
// validate # of segments  
if (ss.size() < 2)   
    handle_error();  
// get the id  
auto id_str = *std::next(ss.begin());  
validate_id(id_str);  
```  
  
Or if you want the third segment:  
  
```cpp  
auto ss = u.segments();  
// validate # of segments  
if (ss.size() < 3)   
    handle_error();  
// get the id  
auto id_str = *std::next(ss.begin(), 2); // 1 segment + 2 positions  
validate_id(id_str);  
```  
  
In practice, you probably want to generalize that to something like "extracting the second, forth, and sixth segment" in a real application. In that case, you case reuse the iterator, which avoids the quadratic cost:  
  
```cpp  
auto ss = u.segments();  
if (ss.size() < 6)   
    handle_error();  
// get the second, forth, and sixth segments  
auto it = std::next(ss.begin());         // iterator to second  
auto second_str = *it;  
std::advance(it, 2);                         // move iterator to forth with std::advance  
auto fourth_str = *it;  
++it; ++it;                                        // or move iterator to sixth without std::advance  
auto sixth_str = *it;  
validate_segments(second_str, fourth_str, sixth_str);  
```  
  
At last, we have the case where you want to use all segments. Then you can just use the example above:  
  
```cpp  
auto ss = u.segments();  
for (auto s: ss) {  
        std::cout << ss[i] << "\n";  // this has linear cost  
}  
```  
  
or use iterators:  
  
```cpp  
auto ss = u.segments();  
for (auto it = ss.begin(); it != ss.end(); ++it) {  
        std::cout << *it << "\n";  // this has linear cost  
}  
```

---

## Comment 3

> Username: meastp  
> Created at: 2022-10-20 06:32:27 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1285011799  

Thanks @vinniefalco  and @alandefreitas for the thorough explanation. I do get the forward vs bidirectional issue.  
  
> The segments views do not offer O(1) random access, so we do not provide index-based operations. If you really need that, the guidance is to copy the strings into your own container, e.g. vector<string> or vector<pct_string_view>. What do you think?  
  
This is fine - perhaps you could spell it out explicitly in the documentation, recommending `std::vector all_segments_vec{segments.begin(), segments.end()}` or (C++23) `auto all_segments_vec = std::ranges::to<std::vector>(segments)` (perhaps with explicit types, I don't know whats better)?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-20 13:42:38 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1285564777  

Why do you want random access?

---

## Comment 5

> Username: meastp  
> Created at: 2022-10-20 13:55:47 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1285588778  

It's just more convenient for me to access some of the segments directly by index. I guess it's a pattern kept from another library, before moving to boost.url (and boost.beast). I'm happy with making a vector with the segment_views.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-10-20 15:27:55 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1285746889  

> recommending std::vector  
  
In my case, I can't recommend `std::vector` because I don't think it's not my recommendation in general.   
  
Unless there's a very specific reason to use `std::vector`, I would recommend sticking to the provided ranges and iterators to avoid extra N+1 dynamic memory allocations.   
  
Allocating memory is expensive. Most of the time, these allocations to create a `std::vector` are even worse than the quadratic cost of iterating the list from the start every time, so even this would be better:  
  
```cpp  
auto ss = u.segments();  
for (std::size_t i = 0; ss.size(); ++i) {  
        std::cout << *std::next(ss[i], i) << "\n";  
}  
```  
  
I would avoid `std::vector`, especially in a server and especially if the main motivation is convenience for the developer.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-10-20 17:29:55 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1285909012  

@alandefreitas we don't know the use-case, so it is not practical (or advisable) to make performance assumptions without actual measurements. The user might prioritize convenience or re-use of existing code over avoiding dynamic allocation.

---

## Comment 8

> Username: meastp  
> Created at: 2022-10-20 19:56:04 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1286065833  

@alandefreitas @vinniefalco The reason in this case is re-use of existing code. We have ported a large project to boost.beast (and boost.url) and haven't been able to rewrite everything (yet). Thanks a lot for your input, we will probably rewrite the logic to be more efficient at some point (although I'm quite sure the allocation done here is very far from a bottleneck in our service ;) )

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-10-20 21:14:12 UTC  
> Url: https://github.com/boostorg/url/issues/604#issuecomment-1286160330  

I agree. I should note I was just talking about `std::vector` as a recommendation in the general case. `std::vector` might be fine in other cases.
