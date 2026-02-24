# #24 Adding Aho-Corasick to Boost.Algorithm [Open]

> Username: zamazan4ik  
> Created at: 2016-08-24 18:41:04 UTC  
> Updated at: 2020-07-23 20:30:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24  

I wrote implementation Aho-Corasick's algorithm. C++11 required for it (I used std::unique_ptr, variadic templates, default template parameters).

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-08-29 19:07:17 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-243223131  

Good start. Please fix the following:  
- Please optimize the implementation (take care of memory allocations first, remove as many as possible)  
- `AhoCorasick` violates naming policy of Boost. Rename to `acho_corasick`  
- Make the acho_corasick searcher usable for multiple searches (multiple calls to `find` will do multiple calls to `init();`; other issues may exist)  
- Make sure that the default constructor does not allocate and does not throw.  
- Try to use intrusive containers for `Container` (this will significantly reduce dynamic memory allocations count)  
- Make sure that `acho_corasick` outperforms `std::find()` on small strings and on a very long strings. Add a benchmark.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2016-08-31 19:48:12 UTC  
> Updated_at: 2016-08-31 19:53:37 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-243879169  

Thanks for the comment. Let's discuss.  
- Ok, i will try to optimize in this way, but i think i can't do anymore here. But i will try.  
- Fixed (AhoCorasick(and similar) -> aho_corasick(and similar). aho, not acho according to Wikipedia)  
- Fixed  
- Why default constructor shouldn't allocate memory for root node? On other way i should write in 'insert' check for 'initialized or not root node'. And it's a bad idea, i think.  
- Unfortunatey i can't do it. map/unordered_map from Boost.Intrusive required very specific value_type. See here: http://www.boost.org/doc/libs/1_61_0/doc/html/intrusive/map_multimap.html  
  I think that it's too strong limitation.  
- I will do it.  
  
Docs, examples and test i will update after finishing work on aho_corasick.hpp .

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-09-03 09:51:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244537606  

> Why default constructor shouldn't allocate memory for root node? On other way i should write in 'insert' check for 'initialized or not root node'. And it's a bad idea, i think.  
  
Users usually expect the default constructor to b lightweight. Take a look at the `std::set` or `std::map`. Instead of checks they store the first node by value directly in the container.

---

## Comment 4

> Username: zamazan4ik  
> Created_at: 2016-09-03 18:39:38 UTC  
> Updated_at: 2016-09-03 23:09:09 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244563034  

About comparing with std::find. This comparing is a little bit bad, because:  
- std::find searches only first entry in a range. aho_corasick(A-C) finds all entries in a range.  
- on random strings std::find have better performance, because std::find on all steps terminate comparing is symbols are different and go to the next symbol. A-C builds automata on patterns: it's difficult work. A-C outperforms std::find on large cases: we have a lot of patterns, and we have a big text.  
  
It means that there isn't any reason to search one-two short entries in small corpus sequence. I will update documentation about it. But on large cases A-C is very-very fast(my benchmark is: corpus string is "War and peace" Tolstoy, patterns for matching British dictionary. A-C is very fast(less than 1 sec); std::find is very-very-very slow....). My system is  i7-3630QM, 12 Gib RAM, Samsung 850 Evo 500 Gib, Kubuntu 16.10.  
  
About memory allocating. Have you any ideas to optimize memory allocation? I can preallocate pool of memory and use this memory range for creating new nodes.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2016-09-04 06:31:08 UTC  
> Updated_at: 2016-09-04 06:31:21 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244586791  

> std::find searches only first entry in a range. aho_corasick(A-C) finds all entries in a range  
  
Search next pattern from current position: `it = std::find(it, end, string);` This is not just a benchmark, but also a good test: make function that uses `std::find` to find all the matches, compare matches of that function with matches of A-C.  
  
> A-C builds automata on patterns: it's difficult work.  
  
Make 2 kinds of benchmarks:  
- find-like vs A-C with automata build  
- find-like vs A-C without automata build (automata is build outside the time measure).  
  
> Have you any ideas to optimize memory allocation?  
  
First of all, make the Container hold nodes by value, not using `std::unique_ptr`...

---

## Comment 6

> Username: zamazan4ik  
> Created_at: 2016-09-04 20:34:25 UTC  
> Updated_at: 2016-09-04 22:57:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244627065  

Container can't contain node by value, because Container declarated in node. I can store in Container only pointers to node.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2016-09-05 18:43:41 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244797860  

> Container can't contain node by value, because Container declarated in node. I can store in Container only pointers to node.  
  
Boost containers can. Try to use `<boost/container/map.hpp>` and `<boost/unordered_map.hpp>`.

---

## Comment 8

> Username: zamazan4ik  
> Created_at: 2016-09-06 05:28:58 UTC  
> Updated_at: 2016-09-06 05:29:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-244853234  

Thank you. It works.

---

## Comment 9

> Username: mclow  
> Created_at: 2016-09-06 17:52:07 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-245033018  

Actually, it depends on the standard library implementation. For example, this code works fine with libc++:  
  
```  
#include <vector>  
  
struct A {  
    int i;  
    std::vector<A> v;  
    };  
  
int main() {  
    A a;  
    a.v.push_back(A());  
}  
```

---

## Comment 10

> Username: zamazan4ik  
> Created_at: 2016-09-06 19:24:19 UTC  
> Updated_at: 2016-09-06 20:51:39 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-245061125  

Hmmm, thank you for example with libc++. I don't know about it :) .  
  
Now A-C uses boost::container::map and boost::unordered_map, because our library should work also with libstdc++. All works fine. Performance increases in 1.8x (test: British dictionary and "Peace and war").

---

## Comment 11

> Username: apolukhin  
> Created_at: 2016-09-06 22:31:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-245116804  

> Actually, it depends on the standard library implementation. For example, this code works fine with libc++:  
  
With libstdc++ works well on `std::vector` and `std::set`, but fails on `std::unordered_set`. I hope Zhihao Yuan [will continue it's work](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4510.html) and in C++19/20 we'll see incomplete type support for all the standard containers in all the Standard Library implementations :)  
  
> Now A-C uses boost::container::map and boost::unordered_map, because our library should work also with libstdc++. All works fine. Performance increases in 1.8x  
  
There's a small limited amount of possible values for T if T is `char`. Try to use `boost::container::flat_map` as a Container. If that helps or the results will be same as for map/unordered_map - great! Then any minimization of Node's size should give us a performance boost.  
  
`std::vector<size_t> pat;` may be changed to something. Try out `boost::container::basic_string<char>` with `variable length encoded integer`. `boost::container::basic_string<char>` has a small-string optimization, it can store 11 or 22 `char` without memory allocations. This may be a wierd idea, but it may give a significant performance boost (or not, benchmarking required).

---

## Comment 12

> Username: zamazan4ik  
> Created_at: 2016-09-08 21:43:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-245751107  

The idea with using `boost::container::flat_map` instead of STL containers is good: new variant works 150ms instead of 250ms with STL containers. But remember, that A-C is customizable and user can simply use flat_map instead of default containers. I suggest one change: we can delete map\unordered_map variants of A-C, and by default use flat_map or something more efficient. It can be more useful for users. Other users can simply customize with other containers.  
  
Ok, i will test it. I read already about variable length encoded integer. It may be useful for users. But i have some more questions:   
1) Should i use simply boost::container::basic_string<size_t> (size_t is required - length of the matched pattern can be more than 255) or i should it customize? I can't found any examples in Boost with variable length encoded integer.   
2) Maybe do you know about any more efficient tecniques for storing length of patterns? For me it will very useful.

---

## Comment 13

> Username: apolukhin  
> Created_at: 2016-09-09 07:08:16 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-245836787  

> Should i use simply boost::container::basic_string (size_t is required - length of the matched pattern can be more than 255) or i should it customize? I can't found any examples in Boost with variable length encoded integer.  
  
The idea is following: patterns are usually not very long, so length 127 is more than enough most of the time. Now, with variable length encoded integer (vint) you can do the following:  
- if most significant bit in `char` is set to 1, then the `char` contains the whole length of the pattern, you just need to set the most significant bit to 0  
- if the most significant bit in `char` is set to 0 and the next bit is 1, then the whole length of the pattern represented by 2 `char`, you just need to set the second most significant bit to 0 and combine two chars into an `unsigned short`  
- ... `unsigned int`  
- ... `std::size_t` if `sizeof(std::size_t) > sizeof(unsigned int)`  
  
You can start by always storing `std::size_t` in `boost::container::string` without any vint encoding, just `std::copy` them into string and back. In that way you'll be able to keep ~2 `std::size_t` in node without memory allocations.

---

## Comment 14

> Username: zamazan4ik  
> Created_at: 2016-09-11 16:23:14 UTC  
> Updated_at: 2016-09-11 19:26:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-246188921  

I tested new versions, and results are:  
1) There is not any reason to use boost::container::basic_string - implementation based on std::vector is faster.  
2) flat_map is faster than std::map and std::unordered_map. I propose using flat_map as default container for Container and delete default implementations based on std::unordered_map and std::map.  
3) variable length encoded integer is an interesting idea. It makes A-C a little bit slower, so i think we shouldn't use it here.

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2016-09-13 11:56:40 UTC  
> Updated_at: 2016-10-07 05:42:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#discussion_r78543711  

I believe that lines 102-103 can be simplified to one line, `current_node->links[*it] = node();`, and that move semantics will still take place in C++11.

---

## Comment 16

> Username: zamazan4ik  
> Created_at: 2016-09-13 12:00:10 UTC  
> Updated_at: 2016-10-07 05:42:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#discussion_r78544096  

yes, you are right. Will be fixed later.

---

## Comment 17

> Username: jeremy-murphy  
> Created_at: 2016-09-13 12:29:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-246665490  

@apolukhin, although I am a fan of regular types that have trivial default constructors, the existing searchers (BM, BMH & KMP) don't have them, so I wonder if it makes sense here?

---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2016-09-15 13:49:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-247333097  

Alexander, could you update the description with a specific citation of which papers or books you based your implementation on? Thanks.

---

## Comment 19

> Username: toshchev95  
> Created_at: 2020-07-23 12:49:32 UTC  
> Updated_at: 2020-07-23 12:53:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-662987388  

ZaMaZaN4iK, @apolukhin, you made a good job. Don't you want to finish it? Check, please, that your code is multi thread

---

## Comment 20

> Username: zamazan4ik  
> Created_at: 2020-07-23 20:30:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/24#issuecomment-663216970  

@toshchev95 Hi! Thank you! Unfortunately now I have no time for finishing the PR. So if you want to continue work on it - it would be awesome!

---
