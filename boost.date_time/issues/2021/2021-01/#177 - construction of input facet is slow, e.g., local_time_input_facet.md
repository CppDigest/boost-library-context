# #177 - construction of input facet is slow, e.g., local_time_input_facet [Open]

> Username: abby-sergz  
> Created at: 2021-01-04 10:26:07 UTC  
> Updated at: 2021-01-04 14:20:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/177  

I noticed a significant performance difference in the following two approaches, when a facet is created for every parsing and when it's re-used. It turned out, that the latter is 100 times faster. It means, that e.g. to parse 100 000 entries, it can take around half of a minute vs a third part of a second.  
A profiler shows that construction of `string_parse_tree` is the reason of the slowness.  
  
Slow:  
```C++  
  void naive_parsing(uint32_t iterations = k_count)  
  {  
    for (uint32_t i = 0; i < iterations; ++i)  
    {  
      local_date_time ldt(not_a_date_time);  
      local_time_input_facet* input_facet = new local_time_input_facet();  
      input_facet->format("%Y-%m-%dT%H:%M:%s%Q");  
      std::stringstream ss{k_input};  
      ss.imbue(std::locale(ss.getloc(), input_facet));  
      ss >> ldt;  
      auto unix_time = (ldt.utc_time() - time_t_epoch).total_milliseconds();  
      if (k_expected_unix_time != unix_time)  
      {  
        std::abort();  
      }  
    }  
  }  
```  
Faster:  
```C++  
  void sstream_cached_parsing()  
  {  
    local_time_input_facet* input_facet = new local_time_input_facet();  
    std::stringstream ss;  
    ss.imbue(std::locale(ss.getloc(), input_facet));  
    input_facet->format("%Y-%m-%dT%H:%M:%s%Q");  
    for (uint32_t i = 0; i < k_count; ++i)  
    {  
      local_date_time ldt(not_a_date_time);  
      ss.clear();  
      ss.str(k_input);  
      ss >> ldt;  
      auto unix_time = (ldt.utc_time() - time_t_epoch).total_milliseconds();  
      if (k_expected_unix_time != unix_time)  
      {  
        std::abort();  
      }  
    }  
  }  
```  
FWIW, in the latter case, the performance is comparable with the same naive approach but based on https://github.com/HowardHinnant/date.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-01-04 11:32:48 UTC  
> Url: https://github.com/boostorg/date_time/issues/177#issuecomment-753923980  

Interesting although I would have guessed locale and string stream construction would be to blame.  Clearly I wouldn't recommend writing this naively -- in fact, I've mentioned to others if you've got a much more specific subset of the universe to parse and you want it fast you should ignore all this stuff and write your own.  
  
So, in the profile then it must be this constructor:      
```  
string_parse_tree(collection_type names, unsigned int starting_point=0) :  
    m_value(parse_match_result_type::PARSE_ERROR)  
```  
that's showing up as slow?  I note its taking a vector of strings by value here -- which looks like it should be by const ref instead.  From the problem as described it seems that only the construction of the tree could play a major role since in both scenarios the parsing would be equivalent.

---

## Comment 2

> Username: abby-sergz  
> Created at: 2021-01-04 13:10:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/177#issuecomment-753966042  

I guess that the contribution from passing std::vector not by a const reference is negligible, there are probably way too many other allocations, but it might be that several things simply pile up.  
Here is a screenshot of the profiler result  
![image](https://user-images.githubusercontent.com/2505903/103535425-f786dd00-4e90-11eb-8b66-701e7df6fa5a.png)  
  
On the other hand the number of months and weekdays is not so big, what about trying to replace string_parse_tree by something simpler, like `std::vector<std::string>`?  
  
Additionally, what about lazy caching of the locale specific strings instead of always gathering them unconditionally?  
  
---  
I am pretty sure that a custom parser can be at least one order of magnitude faster than both, the cached approach and the date library. The issue with it is its cost. Catching and fixing bugs, and even merely creation of tests takes a bigger effort than to create a parser itself. Afterwards one will have to maintain it. However it's actually a huge burden and I really appreciate that here is somebody who handles it.  
In addition to that it will likely be subtly different in terms of formatting patterns, so it's a yet one format and additional room for mistakes.  
If it's possible, it would be better to improve an already existing popular solution.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2021-01-04 14:20:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/177#issuecomment-754001433  

> On the other hand the number of months and weekdays is not so big, what about trying to replace string_parse_tree  
  
yea, I'm sure there's a number of different approaches possible, but it won't be a trivial effort  
  
> Additionally, what about lazy caching of the locale specific strings instead of always gathering them unconditionally?  
  
Which would necessarily introduce some sort of global data.  In this case I think it's on the user to not be naive on reconstructing expensive machinery in a loop.   
  
In any case, this is a completely valid issue which will require some significant effort to solve -- which means it won't happen quickly on my end. I prefer to spend my limited time making formatting work with std::format in c++20, string_view in c++17, etc.
