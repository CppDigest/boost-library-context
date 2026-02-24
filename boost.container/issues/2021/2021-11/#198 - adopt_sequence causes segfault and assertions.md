# #198 - adopt_sequence causes segfault and assertions [Closed]

> Username: pauljohnston2009  
> Created at: 2021-11-03 00:10:58 UTC  
> Updated at: 2021-11-03 14:47:25 UTC  
> Closed at: 2021-11-03 14:47:24 UTC  
> Url: https://github.com/boostorg/container/issues/198  

No specific compiler options are needed, seems to be reproducible on clang and gcc.  
It seems like it might be in the the sort algorithm used, though I'm not entirely sure.  
boost 1.77.0  
  
First case (i % 20 for some duplicate keys) causes a segfault, seems to occur with 85, 86 or 87 elements in the array.  
Second case (i % 19 for some duplicate keys) hits the assertion in   
```  
boost/move/algo/adaptive_sort.hpp:453: bool boost::movelib::detail_adaptive::adaptive_sort_build_params  
BOOST_ASSERT(collected < (n_min_ideal_keys+l_intbuf));  
```  
It occurs with with 85, 86, 87, 88 or 89 elements in the array.  
  
```  
TEST(flat_multimap, segfault)  
{  
    using map_type = boost::container::flat_multimap<uint16_t, uint16_t>;  
    constexpr uint8_t size = 85;  
  
    map_type map;  
    map_type::sequence_type storage;  
  
    for (uint16_t i = 0; i < size; ++i) {  
        storage.emplace_back(i % 20, i);  
    }  
  
    map.adopt_sequence(std::move(storage)); // segfault  
    EXPECT_EQ(size, map.size());  
}  
  
TEST(flat_multimap, assert_hit)  
{  
    using map_type = boost::container::flat_multimap<uint16_t, uint16_t>;  
    constexpr uint8_t size = 85;  
  
    map_type map;  
    map_type::sequence_type storage;  
  
    for (uint16_t i = 0; i < size; ++i) {  
        storage.emplace_back(i % 19, i);  
    }  
  
    map.adopt_sequence(std::move(storage)); // assertion  
    EXPECT_EQ(size, map.size());  
}  
```  
  
  
The bug seems to only occur when using adopt_sequence, if I switch it to map.insert there appears to be no issues  
I can also do map.reserve(storage.size()); before the  map.adopt_sequence to resolve the issues. Though I believe that's just masking the issue, and not actually solving it.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-11-03 14:47:24 UTC  
> Url: https://github.com/boostorg/container/issues/198#issuecomment-959306967  

Thanks for the report. As you mentioned, bugs were in the sort/merge part from Boost.Move. I've fixed them so that your test case runs fine.
