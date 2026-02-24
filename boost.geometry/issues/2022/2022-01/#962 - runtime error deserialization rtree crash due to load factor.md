# #962 - runtime error deserialization rtree crash due to load factor [Closed]

> Username: addy90  
> Created at: 2022-01-13 12:09:05 UTC  
> Updated at: 2022-01-19 23:36:04 UTC  
> Closed at: 2022-01-19 23:36:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/962  

I discovered a crash in the experimental rtree deserialization feature.  
  
In some cases, the following exception is raised:  
```  
Dynamic exception type: boost::wrapexcept<std::runtime_error>  
std::exception::what: rtree loading error  
```  
  
The error comes from here, the test exactly above fails in certain conditions when the element_count is too low in my case:  
https://github.com/boostorg/geometry/blob/394461f773f6003a20c8ebaf06c1f92857e31c8f/include/boost/geometry/index/detail/serialization.hpp#L417  
  
I can replicate the error with the attached source code (Boost Unit Test), please rename to `.cpp`:   
[rtree_serialization_test.txt](https://github.com/boostorg/geometry/files/7862513/rtree_serialization_test.txt)  
  
`BOOST_GEOMETRY_INDEX_DETAIL_EXPERIMENTAL` is enabled.  
  
I have two settings in line 17 and 18. With `POINTS = 100` the loading crashes always, with `POINTS 200` it does not crash in both tests! So it clearly has to do with the loading of the tree. There are surely other options that also make it crash or work but these two should be sufficient to replicate the problem.  
  
When I remove or comment line 415 to 417 from (so when I remove the test and the exception) both my cases work:  
https://github.com/boostorg/geometry/blob/394461f773f6003a20c8ebaf06c1f92857e31c8f/include/boost/geometry/index/detail/serialization.hpp  
  
In my case, `max_elements` is 16 and `min_elements` when I compute it from here should be `4`:  
https://github.com/boostorg/geometry/blob/394461f773f6003a20c8ebaf06c1f92857e31c8f/include/boost/geometry/index/parameters.hpp#L33  
  
The test here fails because my `element_count` is only `3`, so the tree is "underloaded":  
https://github.com/boostorg/geometry/blob/394461f773f6003a20c8ebaf06c1f92857e31c8f/include/boost/geometry/index/detail/serialization.hpp#L415  
  
I am not sure if the rtree packing algorithm allows for "underloaded" rtrees but it looks like this is happening. The rtree is not loaded as the deserialization expects it. So when the test is removed, it works because the rtree is in fact valid concerning the rest of the source code.  
  
I conclude that this means that the test from line 415 to 417 is wrong and either needs to be removed or redefined to work in all valid cases. I am not sure if more cases exist apart from the two I provided.  
  
I hope you are able to replicate my error with the provided code and suggestions. Thank you for your great work on Boost::geometry!

---

## Comment 1

> Username: addy90  
> Created at: 2022-01-13 13:42:27 UTC  
> Updated at: 2022-01-13 14:01:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/962#issuecomment-1012147922  

I just noticed this was similarly posted here: #865  
But the fix was only for the root node, it seems that it still happenes as we can see in my example.  
  
When I run my example, `leafs_level` is `3`and `current_level` is `0`.  
So maybe the test should contain `current_level > 0` instead of `leafs_level > 0` to correctly test the root node?  
So in words "throw the exception when `current_level` is above `0` - it doesn't matter how many `leafs_level` exist but the root node always can contain less nodes"?  
https://github.com/boostorg/geometry/blob/394461f773f6003a20c8ebaf06c1f92857e31c8f/include/boost/geometry/index/detail/serialization.hpp#L415  
  
The example works when I make this change but I am not sure about the rtree internals if this is the right solution.  
See attached patch file (rename to `.patch`):  
[rtree_deserialization_patch.txt](https://github.com/boostorg/geometry/files/7863245/rtree_deserialization_patch.txt)

---

## Comment 2

> Username: awulkiew  
> Created at: 2022-01-13 15:00:25 UTC  
> Updated at: 2022-01-13 15:06:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/962#issuecomment-1012216358  

Thanks for the report! I can reproduce it. Yes, you seem to be correct that the current level should be checked instead since root node can contain less elements than min no matter how big is the rtree.  
  
EDIT: The root node in the rtree from your example indeed contains 3 elements with min being 4. I have to double check whether or not this is the correct fix because I only looked at the code briefly. I'm not fully sure if this is a correct fix (replacing the condition with `current_level > 0`) because in the comment it states `// leafs_level == 0 implies current_level == 0`. It implies that these tests are interchangeable. So either this comment is incorrect as well or something else happens here.  
  
Btw, there is a utility for checking the correct numbers of nodes and elements count < min is only checked for current_level > 0:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/utilities/are_counts_ok.hpp#L86

---

## Comment 3

> Username: addy90  
> Created at: 2022-01-13 15:16:12 UTC  
> Updated at: 2022-01-13 15:36:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/962#issuecomment-1012231157  

Thank you very much for your fast reaction!  
Yes I am glad you could reproduce my issue.  
  
I am also not sure about the right fix here, for the moment I can continue to develop with my patch applied but I would of course prefer an official solution! Thank you for sending the link to the utility check, I didn't know about it yet but it looks like this resembles my suggestion quite much. Maybe it indeed is that easy to fix.  
  
I will look forward to your final solution, thanks in advance!  
  
EDIT: Maybe the comment is not completely right, I mean, when there are no leafs_level, then yes, current_level always is zero. But what happenes when leafs_level is not zero is not stated. Also current_level not being zero does not mean that leafs_level is zero, only the other way round. The condition is sufficient but not necessary, am I seeing this right? It does not state `leafs_level == current_level` but `if leafs_level == 0 then current_level == 0`. So I think it is not interchangeable.

---

## Comment 4

> Username: awulkiew  
> Created at: 2022-01-13 22:18:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/962#issuecomment-1012561778  

I think you're correct. While preparing the previous fix I probably thought that min count can be ignored only in rtrees containing one leaf root node. I must have forgotten than root can contain lesser number of elements than min no matter how many nodes there are in the rtree.

---

## Comment 5

> Username: addy90  
> Created at: 2022-01-14 07:41:32 UTC  
> Updated at: 2022-01-14 08:12:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/962#issuecomment-1012857874  

The reason sounds plausible to me!  
I can at least tell that in my real scenario, I serialized huge point and segment rtrees with about 2.5 million respectively 5 million entries in binary files. With the patch applied, the deserialization finally worked in all cases I have tested yet! Deserialization is much faster than recreation of such large trees.  
So thank you for providing an official patch!  
  
EDIT: Also a large test with about 10 million point entries and 26 million segment entries worked flawless with the patch applied from serialization to deserialization. I know the size does not tell whether the loading is correct but the more levels the more the probability one loading mistake might happen. I can at least tell that in such large examples (whole street network graph of country) it works.
