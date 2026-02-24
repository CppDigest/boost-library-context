# #320 - Documentation gathering? [Open]

> Username: Becheler  
> Created at: 2022-12-23 21:24:56 UTC  
> Updated at: 2023-03-10 19:14:26 UTC  
> Url: https://github.com/boostorg/graph/issues/320  

Hi!   
  
## Questions  
  
- Is there a non-official project that documents the BGL in a more user-friendly way than the official documentation?  
- Is there a will to update/complement/modernize the examples and documentation?  
- I know time is precious and PRs are welcome. Could a newbie like me help in any way? I can definitely dumb-proof a candidate version lol   
  
## Documentation resources  
  
So anyway, as I am making my first steps in the BGL, I find myself lacking a learning material of quality. I did not find a place where resource where listed, so here is what I could gather, by order of relevance (yes, the official documentation is not first place):  
  
1. The most helpful written resource so far has been [the excellent cookbook](https://github.com/richelbilderbeek/boost_graph_cookbook_1) by Richel. This is an incredible resource for me -actually one of the two reasons why I haven't dropped the BGL yet lol  
    - Pros: very user-friendly, well tested, very very well explained. Actually very close to what I would have been expecting from the book.   
    - Cons: Does not cover algorithms. Low visibility, it took me a while to land on this resource. Is there any awareness of this resource and any intention to base the next official documentation on it?  
2. Of course Stack Overflow, that I've been flooding with questions I could not find answers to on official doc. The second reason for me trying to stick to BGL is the remarkable support that @sehe gives there. Is there a will to scan SO for the most frequent beginners problems and use them for documentation purpose?  
3. [The Basics of Using the Boost Graph Library](https://www.youtube.com/watch?v=GSp2531Wti4)  
      - Pros: Beginner-oriented, short (22mn), very nice surface-level introduction, so far has best short code example explaining properties.  
      - Cons: none yet 😛   
5. The [CPPnow presentation by Boris Schaling](https://www.youtube.com/watch?v=uYvBH7TZlFk)  
     - Pros: Beginners oriented, very clear introduction to algorithms. I love that the speaker validates my feelings when it comes to surprising API traps (like nested pairs of visitors).  
     - Cons: 10 years old, does not use C++11 niceties in the example code, and presentation difficult to read from youtube.  
6. The [official documentation](https://www.boost.org/doc/libs/1_81_0/libs/graph/doc/table_of_contents.html)  
    - Pros: official, tested etc  
    - Cons:   
        - The ToC structure is not noob-friendly: the first 6 chapters basically did not help me getting started  
        - The [Chapter 7](https://www.boost.org/doc/libs/1_81_0/libs/graph/doc/quick_tour.html) should be the entry point for newcomers, and its subsections (eg Constructing a Graph, Accessing a Vertex etc) should probably be made more visible and extended to more furnished entire sections visible from the main page ToC (so beginners can localize what they need right away).  
        - **The standard used in the example code is one of the main friction in my learning curve**. I understood there was a small debate about what standard to use in the example code, but frankly speaking there is no way I can convert my users/colleagues to the BGL without having official examples in C++14 to showcase them.  
        - There is no hierarchical build-up of features (from very dummy to graph-god-mode)  
7. I bump regularly on [the main author website](https://cs.brown.edu/~jwicks/boost/libs/graph/doc/quick_tour.html) and to some extend it gave me some supplementary information, but it is very redundant with the official documentation and globally confused me more than helped.  
8. The [official book](https://www.amazon.com/Boost-Graph-Library-Reference-Manual/dp/0201729148)  
    - Pros: official.  
    - Cons: same as the official documentation. I was hoping the book would help me understanding the general ideas, but it confused me more than anything. Waste of time and money 😶   
  
## Suggestions for better API & documentation (from a newbie perspective)  
  
- Replacing *internal properties* by *standard built-in properties* - would be much clearer, I never understood what "internal properties" were before I just understood they were probably a misnommer.  
- Providing a default graph type that maps to `adjacency_list<vecS, vecS>` would help the newbie focusing on discovering (`Un`)`Directed`, `EdgeProperties` and `VertexProperties` without being distracted by selectors.  
- Using C++11/14 in code examples would surely be beneficial and shorter: newbies to BGL are likely to be millennials entitled to modern versions of compilers anyway ;) @murraycu made an old pull request in this direction: https://github.com/boostorg/graph/pull/81  
- I still don't understand named parameters, so if they are here to stay they would surely benefit from better explanations. If they are superior to other overloads, then presenting only named parameters to the beginner may be better, as they would not get confused with the un-named interfaces (that sometimes works, sometimes not, and I haven't been able to figure out why 😨  ).  
- Same with property maps - I lost hours trying to understand them from the official doc but [this video tutorial](https://www.youtube.com/watch?v=GSp2531Wti4) was able to give me a better intuition - so maybe the same kind of pedagogy could help

---

## Comment 1

> Username: murraycu  
> Created at: 2023-03-10 19:05:58 UTC  
> Url: https://github.com/boostorg/graph/issues/320#issuecomment-1464265206  

> Using C++11/14 in code examples would surely be beneficial and shorter  
  
I prepared that a few years ago. Here's the old pull request: https://github.com/boostorg/graph/pull/81
