# #46 - feat: Add emplace_back() and emplace_front() to boost::circular_buffer [Open]

> Username: lano1106  
> Created at: 2023-05-31 04:28:17 UTC  
> Updated at: 2023-06-01 14:59:09 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/46  

this should be trivial to do. In fact, I might give implementing it a shot...

---

## Comment 1

> Username: Lishen1  
> Created at: 2023-05-31 23:45:23 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/46#issuecomment-1571101843  

It's already implemented in some PR

---

## Comment 2

> Username: Lishen1  
> Created at: 2023-06-01 07:06:44 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/46#issuecomment-1571478974  

https://github.com/boostorg/circular_buffer/pull/15

---

## Comment 3

> Username: lano1106  
> Created at: 2023-06-01 14:52:54 UTC  
> Updated at: 2023-06-01 14:56:18 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/46#issuecomment-1572208095  

thx! This is exactly what I was looking for... Yesterday, I have written the feature myself and after reviewing the PR commits, I find it recomforting that I have came up more or less with exactly the same implementation... (minus the unit tests)...  
  
I suppose this is can be seen as a confirmation that the pull request is good...  
  
Any idea when it is going to be merged? Any chance that it makes it to the next release?  
  
Note, that it does not change anything for me as taking the PR code and using it simply consists of copying a header file. No recompilation is required... but I am thinking about the other users...  
  
That was the lack of emplace functions that made me stick with std::deque and use it as a circular buffer...  
  
but boost::circular_buffer is incredibly superior for that purpose due to its amazing memory allocation behavior. deque keeps allocating/deallocating all the way even if its number of elements remains constant...  
  
boost::circular_buffer is an amazing little known gem...

---

## Comment 4

> Username: Lishen1  
> Created at: 2023-06-01 14:59:09 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/46#issuecomment-1572219191  

looks like nobody care about this PR. I just use code from PR directly since boost containers is header-only.
