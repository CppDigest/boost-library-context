# #316 - Interface question [Closed]

> Username: Becheler  
> Created at: 2022-12-09 17:33:15 UTC  
> Updated at: 2023-03-21 23:49:16 UTC  
> Closed at: 2023-03-21 23:49:16 UTC  
> Url: https://github.com/boostorg/graph/issues/316  

Hi!  
  
I began to integrate BGL in my code, what I absolutely love. However, I begin to have `boost::` everywhere, what I expect to confuse the users of my code.  
  
So I began to write a Tree class wrapper that hides a boost graph member and defines suitable traits, but I find myself hesitating between composition or simple inheritance. I like the composition because it gives a more object syntax to the code, but I also have to rewrite lots of interfaces. Inheritance would expose all the beautiful BGL features, but that may be too many features lol  
  
I guess this problem is not new and people surely tried either approaches, any feedback or strong no-goes?  
  
Thank you!

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-12-12 05:02:00 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1345879742  

Pragmatism, pragmatism, pragmatism.  :)  
  
Maybe the appearance of `boost::` in your code is actually the least evil? How do you know that it would confuse users of your code? Maybe it won't?  
  
I don't really know your use case or context, but maybe ADL can be your friend? Maybe you're writing `boost::algorithm(tree)` but if the tree's type is in `boost::` then you only need to write `algorithm(tree)`?  
  
Don't write code unless you have to.  :)

---

## Comment 2

> Username: Becheler  
> Created at: 2022-12-12 18:35:24 UTC  
> Updated at: 2022-12-12 18:36:39 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1347040531  

The users are expected to be researchers in genetics, and the use of generic C++ a-la-BGL or boost libraries is largely uncommon (difficult dependencies management in the pre-conan era is one of the many reasons).  
  
Consequently, all the interfaces that expose let's say function objects will be extremely confusing to the users. I had several talks where I presented a much simpler `Tree<vertex>` class than the BGL graphs that left the audience completely lost on what the class actually does or is intended for. It's very challenging to communicate abstractions past a certain point would I say, like templates past the generic-data-container `vector<T>`.   
  
So for example I need to wrap this code :  
```cpp  
boost::write_graphviz(  
    out, _graph,  
    boost::make_label_writer(boost::get(vertex_bundle, this->_graph)),  
    boost::make_label_writer(boost::get(edge_bundle, this->_graph)));  
```  
so that the level of abstraction goes down to "this class allows you to write your Tree data in a graphviz format". So I would say that ADL will be surely useful but not enough. Does it make sense?

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2022-12-14 04:16:45 UTC  
> Updated at: 2022-12-14 04:17:07 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1350374673  

Yes, if your audience is not fluent in C++ then you have a particular set of challenges.  
  
You certainly might want to create your own `tree.hpp` file that includes all the BGL headers a user could need for working with a tree, write some useful type aliases (e.g. `using binary_tree = boost::k_ary_tree<2>`) and document a small vocabulary of things that a user can get started with. Once they understand the various dimensions along which that small set of things fit together, then you can expand the vocabulary? I don't know, I'm a software engineer, not a teacher.  :)  
  
You could probably do something that is usually not recommended, but you could simply import all of the BGL namespace into your own namespace so that your users don't have to think about using `boost::`? That might cause other problems, do it with caution.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2022-12-14 04:17:56 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1350375565  

Finally, I would try to avoid asking them to learn about templates.

---

## Comment 5

> Username: Becheler  
> Created at: 2022-12-21 03:01:41 UTC  
> Updated at: 2022-12-21 03:08:07 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1360790181  

Thank you for your thorough answer!  
  
Yeah I am not a teacher either (and neither a fully-blown dev btw lol) so yes I love a good challenge 😛   
  
After giving it more thoughts, my problem is a bit more tricky than I first thought.   
  
There are actually several types of graphs involved in my problem, (binary directed, k-ary directed, "_trees_" that have a few cycles, spatio-temporal networks...).  
  
- The vocabulary that naturally emerges from my first category of problem is closer to the Forest data structure developed by Adobe's ASL, see the [code]( https://github.com/stlab/libraries/blob/main/stlab/forest.hpp) and [the article](https://stlab.cc/2020/12/01/forest-introduction.html).  
- The vocabulary emerging from the spatio-temporal networks is closer to the BGL.  
  
So my initial objective (how to enforce some invariants and simplify some interfaces for the binary & k-ary trees classes of problems) kinda falls flat because it would inflate the number of interfaces to learn (as a wise man once said _"Maybe the appearance of boost:: in your code is actually the least evil?"_ 😈  ). I hesitate to go full-BGL for everything...  
  
Shoot! There are so many ways to describe a same problem!   
  
Notes:  
1. To be sure, the `boost::k_ary_tree<2>` you are referring to is from `jeremy-murphy:k-ary-tree` and would require me to copy/paste the `k-ary_tree.hpp` file on [this branch](https://github.com/jeremy-murphy/graph/tree/k-ary-tree/include/boost/graph)  
2. Does it work with `boost::k_ary_tree<3>`? (or 4, 5, 6 ...1000?)  
3. Is your concept of k-ary-tree "has exactly k children" or "at most k children"?  
  
Thanks again for your reflexions!

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2022-12-23 20:22:11 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1364316936  

If it's any help to you, `boost::k_ary_tree<>` does model a forest. Maybe not very well, but it does.  :)  
  
1. Yes.  
2. I believe so, but I'm not sure I tested it!  
3. There is a subtle but important distinction to make here, because obviously the leaves of any rooted tree have zero children, regardless of the cardinality of the tree. So it's perhaps clearer to say that a binary tree has exactly 2 _positions_ for children, each of which could be 'null'.  To answer your question, it's a cardinal tree, so it has exactly _k_ positions for children.

---

## Comment 7

> Username: Becheler  
> Created at: 2022-12-23 20:46:24 UTC  
> Updated at: 2022-12-23 20:46:59 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1364324752  

Thank you! That will be very useful in the case of managing a forest of `boost::k_ary_tree<2>`.   
  
I am not entirely sure I will need a `boost::k_ary_tree<N>` though, because I suspect my genealogical problems to either   
- converge to a cardinal binary tree, in what case I will gladly use your `boost::k_ary_tree<2>`,   
- or don't converge and you only know that the tree has k positions **_or less_** for children .   
   
Anyway that is really precious stuff! Already on my way to steal your header and waiting for it to be included in BGL "at some point" ! 😛

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2023-01-03 00:18:44 UTC  
> Url: https://github.com/boostorg/graph/issues/316#issuecomment-1369286929  

Good luck! Please let me know how it goes.
