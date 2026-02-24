# #35 - [Feature request] Add "contains" method to associative indexes, as of C++20 [Closed]

> Username: fgallegosalido  
> Created at: 2020-06-15 17:46:54 UTC  
> Updated at: 2021-08-16 10:57:29 UTC  
> Closed at: 2021-08-16 10:57:29 UTC  
> Url: https://github.com/boostorg/multi_index/issues/35  

C++20 added a new method to the associative containers, `contains`, which takes a key and returns a boolean, indicating whether or not the element is in the container.  
  
This releases us from using functions like `count` or `find` just for checking if the element is in the container, and be more explicit about our intentions.  
  
As multi-index tries to be as similar as the STL counterparts, this is a logical addition.

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-07-29 10:25:37 UTC  
> Url: https://github.com/boostorg/multi_index/issues/35#issuecomment-888999151  

Just added, will be released in Boost 1.78.
