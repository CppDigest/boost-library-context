# #123 - Tree class in BGL [Open]

> Username: mkatliar  
> Created at: 2018-10-17 18:09:29 UTC  
> Updated at: 2023-05-07 23:28:30 UTC  
> Url: https://github.com/boostorg/graph/issues/123  

Is there a class for trees in BGL?

---

## Comment 1

> Username: anadon  
> Created at: 2018-10-18 01:37:58 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-430845418  

Usually, you just have an acyclic graph, pick a vertex as your root, and use that as a tree.  The reason why this usually isn't done with a full fat graph is that there are usually reference based methods or array based methods that can have better performance, lower memory usage, or both.  You might want to look at std::make_heap and related functions.  
  
https://en.cppreference.com/w/cpp/algorithm/make_heap  
  
@jzmaddock I think this one can be closed out.

---

## Comment 2

> Username: mkatliar  
> Created at: 2018-10-18 13:20:21 UTC  
> Updated at: 2018-10-18 13:21:19 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-431005478  

In my case the main point is the performance. I know that my graph is always a tree, and I want   
 a) the traversals (depth-first and breadth-first) to be as quick as possible;  
 b) avoid dynamic memory allocation whenever possible;  
 c) fast retrieval of a parent vertex.  
Currently I am using `boost::compressed_sparse_row_graph` as an implementation. I am wondering how much performance improvement could be gained from implementing a specialized tree class. What is your opinion on that?

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2018-10-20 02:40:43 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-431542188  

I strongly suspect that you are not prevented from getting maximum performance by the current implementation, although I could be wrong.  
My fundamental argument would be that 'treeness' is not a property of type, but of value, in the same sense that 'sorted' is a property of value.  
A graph with one node is also a tree. A graph with a cycle becomes a tree when you remove an edge from the cycle (and nominate a root).  
  
I think the key design issue is to implement algorithms that assume a graph is a tree, in the same way that we have algorithms (binary search) that assume a sequence is sorted.  
  
One catch to this is how to deal with trees stored as an undirected graph with explicitly nominated root versus a directed graph with implicit root versus a tree that also stores 'back edges' (from child to parent).

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-10-25 11:55:30 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-433021996  

I think I agree about closing this one:  
  
* The answer to the original question is "no".  
* While I suspect that some unnecessary cycles may be used by breadth/depth first searches in checking for cycles, I wonder if this is detectable in practice.  
* If there is a performance improvement to be made - it's in refining the algorithms for this particular case - rather than in a "tree class" as such.  I do agree that tree-data structures are an important use case though.  
  
Thinking out loud here.... suppose there was a traits class that allowed you to mark a data structure as a tree (true or false), could `if constexpr` then be used to optimise the algorithms for that use case?  Someone would need to step and implement it, and then prove it actually makes a difference though..

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2018-10-30 08:38:13 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-434215156  

I've actually had second thoughts on this and I'm wondering if there is a case for a tree class: not for trees in general, but for _k_-ary trees.  
  
Correct me if I'm wrong, @jzmaddock , but Graph doesn't have concepts for a fixed number of successors, so the only way I can think of to define a _k_-ary tree would be to nominate a particular vertex as 'empty' (say, 0, as distinct from the `graph_traits<G>::null_vertex()`) and create an edge from `u` to 0 to represent a missing _k_-th successor. The extra space of one vertex is not an issue, but now the number of vertices is off by one, which makes things a bit awkward.  
  
So maybe it's not a tree class, per se, but a graph with a fixed number of successors.  
  
@mkatliar were you thinking of arbitrarily structured trees or _k_-ary trees? If the latter, how do you implement them?

---

## Comment 6

> Username: anadon  
> Created at: 2018-10-30 11:43:01 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-434268808  

I don't think k-array trees are something which sound be added to the core of BGL.  A constraint like that could be added, but it could just as well be user code.  What you're trying to get at is a vertex with a set number of out edges and 0 or 1 in edges.  Having these predicates checked isn't that bad, but adding complexity to the BGL interface might be.

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2018-11-05 12:53:04 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-435863102  

The point is that the _k_ is a compile-time value, not a run-time predicate, thus the reference to _concepts_.  
If you don't think [_k_-ary trees](https://en.wikipedia.org/wiki/K-ary_tree) belong in Boost.Graph, where do you think they belong?

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2018-11-05 13:42:53 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-435877558  

You may also find it edifying to read the previous discussions about adding a tree class to Boost, which occurred back in 2002 and 2009, and centered around adding [one particular tree data structure](http://tree.phi-sci.com/).  
That never eventuated and it's not clear why. I suspect that trying to add an outside tree implementation into a new Boost.Tree library required more energy and analysis than was available or predicted.  
(Btw, the rub of all those discussions is that plenty of people want a tree data structure.)  
What I think would be simpler is to integrate a new data structure into BGL. It already has `adjacency_matrix` for whatever special use cases it covers; `k_ary_tree` would just be another, with accompanying tree-specific overloads of relevant algorithms.

---

## Comment 9

> Username: anadon  
> Created at: 2018-11-05 16:50:11 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-435947643  

Reading through the 2009 thread, it doesn't look like an imperative for inclusion was ever reached.  
  
I'm trying to think here, at what point does it make sense to change from having support through graph and runtime checks and support to going through the effort to create a more specialized version of what already exists.  I see thee main options: no explicit support, runtime BGL support, or a separate library in Boost.  Lets break down pros and cons for each.  
  
No Support pro:   
- Our time, many of us are over taxed as is  
  
No Support con:   
- Betraying users expectations  
- Less usable  
- Higher burden on users  
  
Runtime BGL pro:  
- Accretion of features is usually the right way to go  
- Simpler to implement  
Runtime BGL con:  
- Violates C++'s cardinal rule of "you don't pay for what you don't get"  
- BGL isn't space or computationally optimal (at least without a lot of tweaking and knowledge)  
  
Separate Library pro:  
- Minimal space over head  
- Deeper enforcement of properties  
- Better compile time optimization  
Separate Library con:   
- Likely to require a decent chunk of time  
  
Already with that, I was wrong and the balance tilts towards having something separate.  But that something separate should be its own project.  But so long as we're here, let's make sure the tree library as a concept would be fully fleshed out.  Off hand, I can see that it would need to have a few things available.  A forest container, tree data structure, standard functions, and dynamic node expansion.  I'm not sold on the forest container being necessary, but having dynamic node expansion is critical for a number of domains and that behavior isn't immediately clear as to what it should even be.    
  
I'm working on 3 hours of sleep.  Am I missing anything?

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2018-11-06 11:25:59 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-436220598  

What do you mean by "dynamic node expansion"? It sounds like something from a traversal algorithm, but maybe you mean something else.

---

## Comment 11

> Username: anadon  
> Created at: 2018-11-06 11:34:43 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-436222688  

I do mean a traversal algorithm as a special case.  One of my bigger areas of study has been AI, and this is one feature often worked around in my experience.  
  
Also, for completeness, there probably should be functions for converting to and from a graph.

---

## Comment 12

> Username: jeremy-murphy  
> Created at: 2018-11-07 12:34:15 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-436608649  

I think we have very different perspectives on this question, so for the moment instead of responding to your summary I'll just give you mine.  
  
The premises that I take as given are these:  
- A _k_-ary tree is a concept in graph theory and so passes some presumed preliminary test of, "Does it even belong in this library?"  
- _k_-ary trees are an extremely important topic in computer science, so we can presume that people want to model them.  
  
Now the issues start to get a bit more nuanced but I'm going to state them simply.  
  
I'll claim that adding a _k_-ary tree data structure to Boost.Graph does not preclude the creation of a separate _k_-ary tree library, either in or out of Boost.  
  
And I'll assert that the audience here is Boost.Graph users, not all Boost users. And so the question is not the general, "Where could/should a _k_-ary tree data structure go?" -- the question is, "Would Boost.Graph users benefit from a _k_-ary tree data structure?"  
  
My initial thought was no, there would be no performance benefit, but I was thinking about general trees, not _k_-ary trees.  
Now, I presume there could be a performance benefit. Knowing _k_ at compile-time, theoretically, should allow for optimization of the memory layout. When a binary tree stores a _predecessor_ edge (to the parent), we can do cool things like depth-first search and isomorphism in constant space.  
And I guess I am saying that these algorithms cannot be implemented on the existing graph data structures, because they don't have the concept of a fixed _k_ number of successors.  
  
Apart from performance, there could be a much better interface for constructing, querying and mutating a _k_-ary tree, with functions that relate directly to the particulars of the structure.  
  
How would it work in practice? Boost.Graph has a great generic design, so as I said earlier, a `k_ary_tree` would exist alongside `adjacency_list`, `adjacency_matrix` and `compressed_sparse_row_graph`. It would satisfy some graph concepts (probably not all) and satisfy some new _k_-ary tree concepts.  
  
Take an incomplete typical scenario:  
  
```  
adjacency_list<> g;  
// Construct g.  
MyDFSVisitor vis; // models DFS Visitor concept.  
depth_first_search(g, vis, ...);  
```  
  
A user whose graph is a binary tree could instead do this:  
  
```  
k_ary_tree<2> g;  
// Construct g using k-ary tree-specific interface.  
MyDFSTreeVisitor vis; // models new DFS k-ary tree visitor concept (probably).  
depth_first_search(g, vis, ...);  
```  
They would not have to use `compressed_sparse_row_graph` anymore and all the limitations it brings.  
Users who do not use _k_-ary trees would not know anything has changed.  
`depth_first_search` would be a new overload implemented in terms of the _k_-ary tree concept.  
  
To summarize, I think there has to be either an improvement in performance or interface to justify adding a _k_-ary tree data structure, and I think there is strong theoretical support that both of these outcomes would be achieved. The real answer will come from experimentation and a proof of concept.  
  
Now, I don't just say all this for the fun of it. _Elements of Programming_ by Stepanov and McJones has a small, complete set of binary tree concepts and algorithms. Anyone can go ahead and make a proof of concept from it. I'll have a go when I have time.  
  
Cheers.

---

## Comment 13

> Username: jeremy-murphy  
> Created at: 2018-11-26 12:53:56 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-441628704  

I didn't really have time but I started anyway and I may as well share my progress now.

---

## Comment 14

> Username: anadon  
> Created at: 2018-12-04 02:11:37 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-443943518  

Update cross reference from the mailing list: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3700.html

---

## Comment 15

> Username: jeremy-murphy  
> Created at: 2019-01-04 11:31:59 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-451420437  

Happy new year. @jzmaddock have I not convinced you that this issue is actually worthwhile?

---

## Comment 16

> Username: jzmaddock  
> Created at: 2019-01-04 13:00:16 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-451437427  

Maybe, but what do you want me to do?

---

## Comment 17

> Username: jeremy-murphy  
> Created at: 2019-01-05 04:39:19 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-451627335  

Just reopen this issue.

---

## Comment 18

> Username: jzmaddock  
> Created at: 2019-01-05 14:19:48 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-451659535  

>Just reopen this issue.  
  
Ah OK, that I can do ;)  
  
BTW with regard to adding this to the BGL - it would be quite a large addition - more than the community maintenance group normally takes on, so something like a mini-review would probably be best.  Some open questions off the top of my head:  
  
* Can it be shown that tree data structures lead to improved performance, how about neater (self explanatory) code?  
* Would the proposal linked by @anadon above work with the BGL "straight out of the box"?  
  
Other than that, something that followed the standardization proposal is more likely to gain traction I would think - and might shake out any issues in the proposal?

---

## Comment 19

> Username: jeremy-murphy  
> Created at: 2019-01-08 00:21:05 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-452130021  

On Sun., 6 Jan. 2019, 1:19 am jzmaddock <notifications@github.com wrote:  
  
> Just reopen this issue.  
>  
> Ah OK, that I can do ;)  
>  
Thanks. :)  
  
BTW with regard to adding this to the BGL - it would be quite a large  
> addition - more than the community maintenance group normally takes on, so  
> something like a mini-review would probably be best.  
>  
Ok. Let's cross that bridge when we come to it.  
  
Some open questions off the top of my head:  
>  
>    - Can it be shown that tree data structures lead to improved  
>    performance, how about neater (self explanatory) code?  
>  
> Have you looked at the PR? I commented with some benchmarks that  
demonstrate significant performance improvement.  
  
  
>    - Would the proposal linked by @anadon <https://github.com/anadon>  
>    above work with the BGL "straight out of the box"?  
>  
> I don't think so, as WG21/N3700:  
i)  is about search trees, that are self-balancing, which is more specific  
than the purpose of BGL  
ii) is designed like a standard container, which Boost.Graph classes are  
not.  
  
Other than that, something that followed the standardization proposal is  
> more likely to gain traction I would think - and might shake out any issues  
> in the proposal?  
>  
I get what you mean but I think it's also important that Boost maintains  
its independence; after all, BGL defined a framework of concepts for graphs  
long ago -- the standard doesn't have one, yet.  
WG21/N3700 is making tree classes in the standard container design, which  
is not the design that BGL has. I think a lot of concepts will be similar  
or the same and the coexistence of two projects will benefit each other.

---

## Comment 20

> Username: Becheler  
> Created at: 2022-11-25 19:00:49 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1327793582  

Hello! Any news on this front? I was hoping to use BGL to upgrade from my own Forest and k-ary Tree 🙃

---

## Comment 21

> Username: jeremy-murphy  
> Created at: 2022-11-27 23:34:51 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1328369442  

Hey @Becheler thanks for your interest. Best we can offer you right now is the PoC branch I made three years ago.

---

## Comment 22

> Username: jeremy-murphy  
> Created at: 2022-11-27 23:40:47 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1328370606  

Also, this presentation might be useful, but I warn you that the speaker is very slow, you need to watch it at 1.5x: https://youtu.be/e-cRWFiGb8E

---

## Comment 23

> Username: Becheler  
> Created at: 2022-11-27 23:53:07 UTC  
> Updated at: 2022-12-06 02:45:33 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1328372969  

Oooh nice! Thank you! Well I don't think I want to complicate my dependencies even more, so I will keep my rocky implementation , but I will watch this (not-so-slow-for-non-native-speakers) cool presentation!  
[EDIT] I took the time to watch the presentation, and it convinced me to throw my Tree class by the window and begin using the BGL :-)

---

## Comment 24

> Username: jeremy-murphy  
> Created at: 2022-12-06 04:02:39 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1338710169  

That's very flattering, thank you, @Becheler ! It motivates me to one day pick up the baton again and get it finished.

---

## Comment 25

> Username: Becheler  
> Created at: 2023-01-17 04:50:31 UTC  
> Updated at: 2023-01-17 05:21:01 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1384828296  

Hi @jeremy-murphy !   
Trees are back to haunt you! 🌱 👻   
I began to play with your bidirectional binary tree code, but I am not sure to understand how I'm supposed to attach vertex/edgeproperties to it? Until now my BGL rocky way has mostly been playing with `adjacency_list<....., VertexProperty, EdgeProperty>`.   
If I understand correctly your implementation I would have to define myself a property map as a data member when I define my class `class my_binary_tree : boost::bidirectional_binary_tree<>` ? Sorry to bother from ground 0 of BGL 😅

---

## Comment 26

> Username: jeremy-murphy  
> Created at: 2023-01-17 06:00:37 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1384873049  

> Hi @jeremy-murphy !   
> Trees are back to haunt you! 🌱 👻   
  
No problem!  
  
I didn't consider properties when I implemented the class, so yeah, it's completely missing.   
  
I wonder if you could create a new class that simply publicly inherits from both the tree and a property map to get what you need? That would give you the most direct union of functionality and not require redefining the interface.  
  
But use composition if you need more control.  
  
Best of luck!

---

## Comment 27

> Username: Becheler  
> Created at: 2023-01-21 02:29:49 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1399154593  

Hi @jeremy-murphy !  
I'm carving my way in, but the code fails to compile with a `no template named 'BidirectionalBinaryTreeConcept' in namespace 'boost::concepts';`  
I can't find anything in the project that defines this concept, am I missing an external dependency?   
Also I am not sure this present thread issue is the best suited to this discussion, so feel free to re-orient me somewhere else 😄

---

## Comment 28

> Username: Becheler  
> Created at: 2023-01-22 21:19:34 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1399611472  

Ok, found it in the file https://github.com/jeremy-murphy/graph/blob/k-ary-tree/include/boost/graph/graph_concepts.hpp  
. I isolated the required definitions in a `concepts.hpp` file :  
  
```cpp  
#ifndef BOOST_GRAPH_DETAIL_CONCEPTS_TREE  
#define BOOST_GRAPH_DETAIL_CONCEPTS_TREE  
  
#include <boost/concept_check.hpp>  
#include <boost/concept/assert.hpp>  
#include <boost/concept/detail/concept_def.hpp>  
  
namespace boost::concepts  
{  
  
BOOST_concept(ForwardBinaryTree,(G))  
: Graph<G>  
{  
    BOOST_CONCEPT_USAGE(ForwardBinaryTree) {  
        t = has_left_successor(u, g);  
        t = has_right_successor(u, g);  
        v = left_successor(u, g);  
        v = right_successor(u, g);  
        t = empty(u, g);  
        const_constraints(g);  
    }  
    void const_constraints(G const &g) {  
        t = has_left_successor(u, g);  
        t = has_right_successor(u, g);  
        v = left_successor(u, g);  
        v = right_successor(u, g);  
        t = empty(u, g);  
    }  
    bool t;  
    G g;  
    typename graph_traits<G>::vertex_descriptor u, v;  
};  
  
  
BOOST_concept(BidirectionalBinaryTree,(G))  
: ForwardBinaryTree<G>  
{  
    BOOST_CONCEPT_USAGE(BidirectionalBinaryTree) {  
        t = has_predecessor(u, g);  
        t = predecessor(u, g);  
        u = root(u, g);  
        const_constraints(g);  
    }  
  
    void const_constraints(G const &g) {  
        t = has_predecessor(u, g);  
        t = predecessor(u, g);  
        u = root(u, g);  
    }  
  
    bool t;  
    G g;  
    typename graph_traits<G>::vertex_descriptor u;  
};  
  
BOOST_concept(MutableForwardBinaryTree,(G))  
    : ForwardBinaryTree<G>  
{  
    BOOST_CONCEPT_USAGE(MutableForwardBinaryTree) {  
    e = add_left_edge(u, v, g);  
    e = add_right_edge(u, v, g);  
    // TODO: remove_left_edge, remove_right_edge  
    }  
    G g;  
    typename graph_traits<G>::vertex_descriptor u, v;  
    typename graph_traits<G>::edge_descriptor e;  
};  
  
BOOST_concept(MutableBidirectionalBinaryTree,(G))  
    : MutableForwardBinaryTree<G>  
{  
    BOOST_CONCEPT_USAGE(MutableBidirectionalBinaryTree) {  
    e = add_predecessor(u, v, g);  
    remove_predecessor(u, g);  
    }  
    G g;  
    typename graph_traits<G>::vertex_descriptor u, v;  
    typename graph_traits<G>::edge_descriptor e;  
};  
      
}  
  
#endif  
```

---

## Comment 29

> Username: jeremy-murphy  
> Created at: 2023-01-23 00:05:10 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1399648405  

Hi @Becheler , sorry yeah, I forgot that there are actually a few files involved. If you look at the corresponding PR, you can see exactly what changed, but essentially there is the 'public' `k-ary_tree.hpp` file, then a 'private' `detail/k-ary_tree.hpp` file and the tree concepts that you found added to the existing `graph_concepts.hpp` file.

---

## Comment 30

> Username: Becheler  
> Created at: 2023-05-06 03:37:32 UTC  
> Updated at: 2023-05-06 03:38:29 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1537034393  

@jeremy-murphy  in the present state, the syntax `k_ary_tree<3> g` mentioned [here](https://github.com/boostorg/graph/issues/123#issuecomment-436608649) is not supported as the focus seemed to be binary trees only. Am I missing some code updates and do you think it's reasonably feasible to the newcomer to expand on your `binary_tree` implementation?  
Beside having a `array<vertex_descriptor, N> successors;` that is. I'm no graph theoretician 😛

---

## Comment 31

> Username: jeremy-murphy  
> Created at: 2023-05-07 23:28:30 UTC  
> Url: https://github.com/boostorg/graph/issues/123#issuecomment-1537566849  

Hey @Becheler , yeah that doesn't surprise me. I started writing it with _k_-ary in mind but at some point I probably mentally switched over to binary and forgot to look back.  
  
I'm planning on putting some effort into my PR again and any contributions would be welcome! However, this is the general issue about adding a tree class to BGL rather than my specific implementation of it, so I think this topic is best moved over to the PR.
