# #170 Depreciation cleanups; require c++11 [Closed]

> Username: anadon  
> Created at: 2019-04-30 21:47:52 UTC  
> Updated at: 2019-05-02 19:02:43 UTC  
> Closed at: 2019-05-02 19:02:43 UTC  
> Url: https://github.com/boostorg/graph/pull/170  

@jzmaddock I have a bigger one for you.  
  
Cleaned up a lot of old depreciation warnings, and starting to add logic for coverage CI.  
  
Also, there were a few instances where C++11 was just so easier, and so now BGL requires C++11 support.  As I go forward, this will become more important.

---

## Comment 1

> Username: anadon  
> Created_at: 2019-05-01 01:22:32 UTC  
> Url: https://github.com/boostorg/graph/pull/170#issuecomment-488170104  

@Glenfe in Slack mentioned that some of the C++11 requirements w/r std::bind1st can be replaced with boost::bind which has C++03 support.  It looks like he's going to review a few of these changes.

---

## Review 2 [Commented]

> Username: anadon  
> Created_at: 2019-05-01 01:39:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-232474257  

📁 include/boost/graph/labeled_graph.hpp

```diff
 389 |-     { return boost::remove_vertex(vertex(l), _graph); }
 389 |+     {
 390 |+         // _map.erase(l);
```

> Username: anadon  
> Created_at: 2019-05-01 01:39:29 UTC  
> Updated_at: 2019-05-02 17:05:12 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r279990952  

Related to #167 .


---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2019-05-01 04:20:06 UTC  
> Url: https://github.com/boostorg/graph/pull/170#issuecomment-488207528  

As I have said previously, everyone appreciates effort, but I don't think this is the right way to progress the BGL. The best pull requests are small, not big; they focus on one thing, or one idea across several areas. This is a mixture of what looks like mostly white-space changes and actual code changes, which makes the actual code changes easy to miss. I recommend to just not worry about white-space at all. It's something we can fix automatically with clang-format one day -- no-one wants to review white-space changes, and it's just risky to mix them up with actual material changes.  
Also, I don't think changing the base language standard of a library should happen on the whim of a pull request. I'm all for progress, but we also have a community that uses this library and they should probably be consulted with in case we happen to have a lot or a few significant users that rely on '03 compatibility.  
Let's hope not, but maybe let's ask first?

---

## Comment 4

> Username: anadon  
> Created_at: 2019-05-01 04:34:08 UTC  
> Url: https://github.com/boostorg/graph/pull/170#issuecomment-488208720  

@jeremy-murphy Glen Fernandes caught me on a number of issues with this commit.  Check back in about a week or so.  I should have it in a shape you find considerably less objectionable.

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 17:51:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233150373  

📁 .travis.yml

```diff
  65 |-           sources:
  66 |-             - ubuntu-toolchain-r-test
  67 |- 
```

> Username: jzmaddock  
> Created_at: 2019-05-02 17:51:52 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280529897  

Boost in general is still testing with these older compiler, I'm not sure we want to remove all these?


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 17:54:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233151906  

📁 example/fr_layout.cpp

```diff
  17 | #include <vector>
  18 | #include <boost/random/linear_congruential.hpp>
  19 |- #include <boost/progress.hpp>
```

> Username: jzmaddock  
> Created_at: 2019-05-02 17:54:52 UTC  
> Updated_at: 2019-05-02 17:54:53 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280531053  

I know boost/progress.hpp is old stuff, but I assume program output is a lot less pretty without this?  What's the rationale for removing it?


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 17:56:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233152593  

📁 example/graph-assoc-types.cpp

```diff
  15 |+ generic_foo(
  16 |+   Graph & g
  17 |+ ){
```

> Username: jzmaddock  
> Created_at: 2019-05-02 17:56:21 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280531570  

I know I said I'd accept any sensible reformating, but please not mixed up with substantive changes, and IMO this is a horrible format :(


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 17:57:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233153167  

📁 example/graphviz.cpp

```diff
  76 |+   int argc, 
  77 |+   char* argv[]
  78 |+ ){
```

> Username: jzmaddock  
> Created_at: 2019-05-02 17:57:29 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280532017  

Sorry, but that formatting really does grate with me.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 17:59:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233154181  

📁 include/boost/graph/adjacency_list_io.hpp

```diff
  78 | // get a subset of properties tagged Stag
  79 |- template<class Tag, class Value, class Next, 
  79 |+ template<class Tag, class Value, class Next,
```

> Username: jzmaddock  
> Created_at: 2019-05-02 17:59:27 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280532771  

Does this file contain anything other than whitespace changes?   It's *very* hard to review stuff like this.... sorry!


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2019-05-02 18:02:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/170#pullrequestreview-233155578  

📁 include/boost/graph/bc_clustering.hpp

```diff
 156 |   std::vector<centrality_type> edge_centrality(num_edges(g));
 157 |-   betweenness_centrality_clustering(g, done, 
 157 |+   betweenness_centrality_clustering(g, done,
```

> Username: jzmaddock  
> Created_at: 2019-05-02 18:02:19 UTC  
> Url: https://github.com/boostorg/graph/pull/170#discussion_r280533847  

Another 2 files with just whitespace changes: with over 200 changed files and a mix of substantive and formatting changes this is getting rather hard going.   Please can we split this into smaller orthogonal changes?  
  
Stopping at this point...


---

## Comment 11

> Username: anadon  
> Created_at: 2019-05-02 18:13:56 UTC  
> Url: https://github.com/boostorg/graph/pull/170#issuecomment-488775947  

You can change the review settings to hide files with only whitespace  
changes.  
  
On Thu, May 2, 2019, 14:02 jzmaddock <notifications@github.com> wrote:  
  
> *@jzmaddock* commented on this pull request.  
> ------------------------------  
>  
> In include/boost/graph/bc_clustering.hpp  
> <https://github.com/boostorg/graph/pull/170#discussion_r280533847>:  
>  
> >  template<typename MutableGraph, typename Done>  
>  void  
>  betweenness_centrality_clustering(MutableGraph& g, Done done)  
>  {  
>    typedef typename Done::centrality_type centrality_type;  
>    std::vector<centrality_type> edge_centrality(num_edges(g));  
> -  betweenness_centrality_clustering(g, done,  
> +  betweenness_centrality_clustering(g, done,  
>  
> Another 2 files with just whitespace changes: with over 200 changed files  
> and a mix of substantive and formatting changes this is getting rather hard  
> going. Please can we split this into smaller orthogonal changes?  
>  
> Stopping at this point...  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/170#pullrequestreview-233155578>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAXYEBIWXM3WEGDUTDY744LPTMUCZANCNFSM4HJQNRIQ>  
> .  
>

---
