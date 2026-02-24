# #1 dynamic_bitset<> find_next() improvements [Closed]

> Username: alyst  
> Created at: 2014-01-12 23:27:59 UTC  
> Updated at: 2019-06-06 11:16:45 UTC  
> Closed at: 2014-05-16 05:25:31 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/1  

This pull request should provide some performance improvements to dynamic_bitset<>::find_next() by simplifying bit operations and using built-in instructions for finding the lowest bit. It also provides dynamic_bitset<> hash implementation (so it could be used by unordered_map/set<>).  
  
There were corresponding tickets in boost.org trac for years already:  
- https://svn.boost.org/trac/boost/ticket/2841  
- https://svn.boost.org/trac/boost/ticket/5158  
- https://svn.boost.org/trac/boost/ticket/5159  
  
But there were no response from the maintainers. Also my post to boost.org mailing list had seen no feedback.  
So this is my attempt to get some feedback via the new Github-based boost infrastructure.

---

## Comment 1

> Username: ahmedcharles  
> Created_at: 2014-05-16 05:25:31 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/1#issuecomment-43298507  

I need to do more work for testing to get these patches submitted.

---

## Comment 2

> Username: IanS4t1qbit  
> Created_at: 2018-06-13 23:29:41 UTC  
> Updated_at: 2018-06-13 23:30:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/1#issuecomment-397119849  

Has dynamic_bitset<> hash actually been implemented?  I am using a std::unordered_map<dynamic_bitset<>, double> but the compiler does not find a hash function.

---

## Comment 3

> Username: BrannonKing  
> Created_at: 2019-02-04 15:07:33 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/1#issuecomment-460282579  

Why is this closed? I needed the hash functionality here at least.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-06-06 11:16:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/1#issuecomment-499453321  

Looks like the ball got dropped on this one.  Instead of re-opening it, will (re)submit one PR per commit.  The lowest_bit optimization probably needs more preprocessor conditionals to support other platforms, similar to recent changes made for popcnt acceleration.

---
