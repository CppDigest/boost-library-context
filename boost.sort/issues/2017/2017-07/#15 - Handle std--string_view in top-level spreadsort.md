# #15 - Handle std::string_view in top-level spreadsort [Open]

> Username: Morwenn  
> Created at: 2017-07-04 14:31:12 UTC  
> Updated at: 2017-07-11 10:23:14 UTC  
> Url: https://github.com/boostorg/sort/issues/15  

The top-level `spreadsort` function has specific overloads targeting `std::string` and `std::wstring` specifically. With C++17 around the corner, these overloads could also accept `std::string_view` and `std::wstring_view` when they are available.  
  
It notably helps `std::string_view` to be more of a "drop-in replacement for `std::string`" as much as possible.  
  
By the way, is `string_spread_sort` also intended to sort collections of `std::vector<char>` or `std::deque<char>`, or is it not suited for these cases?

---

## Comment 1

> Username: spreadsort  
> Created at: 2017-07-08 23:34:51 UTC  
> Url: https://github.com/boostorg/sort/issues/15#issuecomment-313887324  

string_sort is suitable for sorting std::vector<char> and std::deque<char>; you may need to write functors to support it though.  
  
If I added support for string_views, is it feasible to do so cleanly without breaking older compilers?  
  
I'm curious: though it's significantly faster at sorting strings, is anybody using this?

---

## Comment 2

> Username: Morwenn  
> Created at: 2017-07-09 09:42:29 UTC  
> Url: https://github.com/boostorg/sort/issues/15#issuecomment-313909735  

Couldn't you simply add the following to the top-level spreasort.h?  
  
    #ifdef __cpp_string_view  
    template <class RandomAccessIter>  
    inline typename boost::enable_if_c<  
      is_same<typename std::iterator_traits<RandomAccessIter>::value_type,  
              typename std::string_view>::value, void >::type  
    spreadsort(RandomAccessIter first, RandomAccessIter last)  
    {  
      string_sort(first, last);  
    }  
  
    template <class RandomAccessIter>  
    inline typename boost::enable_if_c<  
      is_same<typename std::iterator_traits<RandomAccessIter>::value_type,  
              typename std::wstring_view>::value &&  
      sizeof(wchar_t) == 2, void >::type  
    spreadsort(RandomAccessIter first, RandomAccessIter last)  
    {  
      boost::uint16_t unused = 0;  
      string_sort(first, last, unused);  
    }  
    #endif  
  
I couln't find the exact feature test macro `__cpp_string_view`, but there ought to be an equivalent somewhere in Boost. It's just additional overloads with an SFINAE condition in the return type, so I don't think it would break anything. Alternatively, you could also alter the SFINAE conditions of the already existing functions, but I don't know whether it's ABI-breaking, and I don't know either whether Boost cares about breaking the ABI.  
  
Regarding whether people do sort collections of `std::string_view`: I don't think many people already do that since it's fairly new, but I found [a few occurrences](https://github.com/search?langOverride=&q=string_view+sort&repo=&start_value=1&type=Code) of people doing that on GitHub.

---

## Comment 3

> Username: spreadsort  
> Created at: 2017-07-11 10:23:14 UTC  
> Url: https://github.com/boostorg/sort/issues/15#issuecomment-314401591  

That link doesn't work for me.  
  
If you figure out an appropriate ifdef and a way to test it on a current version of gcc I can download, I'm happy to test and add it.  Until then, unless there is significant demand, It'll wait.
