# #688 - an issue in the http headers equal_range query [Closed]

> Username: WeissGotsman  
> Created at: 2017-07-28 02:41:50 UTC  
> Updated at: 2017-07-28 23:29:35 UTC  
> Closed at: 2017-07-28 23:29:35 UTC  
> Url: https://github.com/boostorg/beast/issues/688  

**Issue:**   
for beast version 89, equal_range query on http headers does yield the result expected.  
  
in boost\beast\http\impl\fields.ipp:  
  
```  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
equal_range(string_view name) const ->  
    std::pair<const_iterator, const_iterator>  
{  
    auto const result =  
        set_.equal_range(name, key_compare{});  
    return {  
        list_.iterator_to(result->first),  
        list_.iterator_to(result->second)};  
}  
  
```  
  
I suppose you are intended to write the following  
  
```  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
equal_range(string_view name) const ->  
    std::pair<const_iterator, const_iterator>  
{  
    auto const result =  
        set_.equal_range(name, key_compare{});  
    return {  
        list_.iterator_to(*(result.first)),  
        list_.iterator_to(*(result.second))};  
}  
  
```  
  
but I did not see any meaning of mapping the iterator pair (bounds) of an equal_range from a tree container to a list container, because such bounds are normally used to iterate through all values that meet the condition of equal_range, while it relies on the "sorted" nature of a tree container, mapping to a list container simply breaks it.  
  
**How to reproduce:**   
  
```  
auto rang = res.equal_range(boost::beast::string_view("Set-Cookie"));  
  
for (auto s = rang.first; s != rang.second; s++)  
{  
	std::cout << "s->name:" << s->name() << ", s->value:" << s->value() << std::endl << std::endl;  
}  
```  
  
does not give all fields with name "Set-Cookie", instead it iterates into other fields.  
  
**How to Fix**   
  
in boost\beast\http\fields.hpp:  
  
```  
  
/** Returns a range of iterators to the fields with the specified name.  
  
	@param name The field name.  
  
	@return A range of iterators to fields with the same name,  
	otherwise an empty range.  
*/  
std::pair<typename set_t::const_iterator, typename set_t::const_iterator>  
equal_range(field name) const;  
  
/** Returns a range of iterators to the fields with the specified name.  
  
	@param name The field name.  
  
	@return A range of iterators to fields with the same name,  
	otherwise an empty range.  
*/  
std::pair<typename set_t::const_iterator, typename set_t::const_iterator>  
equal_range(string_view name) const;  
  
```  
  
in boost\beast\http\impl\fields.ipp:  
  
```  
  
template<class Allocator>  
inline  
auto  
basic_fields<Allocator>::  
equal_range(field name) const ->  
    std::pair<typename set_t::const_iterator, typename set_t::const_iterator>  
{  
    BOOST_ASSERT(name != field::unknown);  
    return equal_range(to_string(name));  
}  
  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
equal_range(string_view name) const ->  
    std::pair<typename set_t::const_iterator, typename set_t::const_iterator>  
{  
    auto const result =  
        set_.equal_range(name, key_compare{});  
    return result;  
}  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-28 02:44:33 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318541325  

hmm... I believe you if there's a bug (I am going to try it right now). But I don't know that I agree about this fix. Two points:  
  
1. All fields with the same name should be adjacent in the list in insertion order  
2. `set_t::const_iterator` is not a public type, I don't want to expose it

---

## Comment 2

> Username: WeissGotsman  
> Created at: 2017-07-28 02:49:32 UTC  
> Updated at: 2017-07-28 02:50:11 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318541962  

then you probably need to:  
1. decrease the upper bound in the tree container  
2. map to the list container  
3. increase the upper bound in the list container

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-28 02:57:45 UTC  
> Updated at: 2017-07-28 02:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318543063  

I don't think the increasing/decreasing upper bound is needed, it looks like it was just a simple syntax error. I have added a test which passes (see test/http/fields.cpp). Please try **version 90** which contains the fix: https://github.com/boostorg/beast/pull/687 If the bug still happens please provide a commit with a test case (just modify https://github.com/vinniefalco/beast/blob/v90/test/http/fields.cpp#L407)  
  
Thanks!

---

## Comment 4

> Username: WeissGotsman  
> Created at: 2017-07-28 03:53:19 UTC  
> Updated at: 2017-07-28 05:28:06 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318551358  

decrease and then increase is required because the upper bound is non-inclusive, and you don't have any continuity guarantee on the domains between equal elements and non-equal element . Here is the fix you need under the assumption "All fields with the same name should be adjacent in the list in insertion order"  
  
```  
  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
equal_range(string_view name) const ->  
    std::pair<const_iterator, const_iterator>  
{  
    auto result =  
        set_.equal_range(name, key_compare{});  
  
    return {  
        list_.iterator_to(*(result.first)),  
        ++(list_.iterator_to(*(--result.second)))};  
}  
  
```  
  
**How to reproduce:**  
  
1. In the http_client example, change to response to boost::beast::http::string_body and make a request to url: http://answers.unity3d.com/index.html  
  
2. then try to grab all cookies from the response:  
  
```  
  
auto rang = res.equal_range(boost::beast::string_view("Set-Cookie"));  
  
for (auto s = rang.first; s != rang.second; s++)  
{  
	std::cout << "s->name:" << s->name() << ", s->value:" << s->value() << std::endl << std::endl;  
}  
  
```  
  
3. With the fixed code, second->name() is X-Frame-Options and the above loop outputs only two Set-Cookie fields. While with the buggy code, rang.second->name() is Content-Type and the above loop outputs all headers.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-28 04:14:45 UTC  
> Updated at: 2017-07-28 04:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318554614  

I see what you mean now! This code reproduces the defect:  
```  
            fields f;  
            f.insert("E", 1);  
            f.insert("B", 2);  
            f.insert("D", 3);  
            f.insert("B", 4);  
            f.insert("C", 5);  
            f.insert("B", 6);  
            f.insert("A", 7);  
            auto const rng = f.equal_range("B");  
            BEAST_EXPECT(std::distance(rng.first, rng.second) == 3);  
            BEAST_EXPECT(std::next(rng.first, 0)->value() == "2");  
            BEAST_EXPECT(std::next(rng.first, 1)->value() == "4");  
            BEAST_EXPECT(std::next(rng.first, 2)->value() == "6");  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-28 04:17:27 UTC  
> Updated at: 2017-07-28 04:17:46 UTC  
> Url: https://github.com/boostorg/beast/issues/688#issuecomment-318554972  

You are right about the domains, this fixes the problem:  
```  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
equal_range(string_view name) const ->  
    std::pair<const_iterator, const_iterator>  
{  
    auto result =  
        set_.equal_range(name, key_compare{});  
    if(result.first == result.second)  
        return {list_.end(), list_.end()};  
    return {  
        list_.iterator_to(*result.first),  
        ++list_.iterator_to(*(--result.second))};  
}  
```  
  
Thanks for finding this!!
