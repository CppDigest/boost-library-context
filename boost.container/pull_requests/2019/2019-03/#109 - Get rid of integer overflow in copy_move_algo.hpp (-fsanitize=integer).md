# #109 Get rid of integer overflow in copy_move_algo.hpp (-fsanitize=integer). [Merged]

> Username: Breakthrough  
> Created at: 2019-03-20 18:43:57 UTC  
> Updated at: 2019-03-30 11:34:56 UTC  
> Merged at: 2019-03-30 11:34:41 UTC  
> Closed at: 2019-03-30 11:34:41 UTC  
> Url: https://github.com/boostorg/container/pull/109  

When running a program with -fsanitize=integer, I get runtime errors on line 514 of copy_move_algo.hpp due to the way the while loop is written:  
  
[...] boost/container/detail/copy_move_algo.hpp:514:15: runtime error: unsigned integer overflow: 0 - 1 cannot be represented in type 'typename boost::container::allocator_traits<new_allocator<pair<unsigned int, __normal_iterator<const unsigned int *, vector<unsigned int, allocator<unsigned int> > > > > >::size_type' (aka 'unsigned long')  
  
When the loop terminates, n-- will cause unsigned integer overflow in n.  Although the value of n is discarded otherwise, this behavior is likely unwanted and unintentional, thus I'm submitting this PR.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-03-30 11:34:56 UTC  
> Url: https://github.com/boostorg/container/pull/109#issuecomment-478237508  

Thanks for the patch.

---
