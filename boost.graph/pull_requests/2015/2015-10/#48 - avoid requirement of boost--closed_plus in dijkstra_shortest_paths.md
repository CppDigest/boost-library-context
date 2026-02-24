# #48 avoid requirement of boost::closed_plus in dijkstra_shortest_paths [Closed]

> Username: ahhz  
> Created at: 2015-10-23 22:51:11 UTC  
> Updated at: 2019-03-08 18:05:14 UTC  
> Closed at: 2019-03-08 18:05:13 UTC  
> Url: https://github.com/boostorg/graph/pull/48  

dijkstra_shortest_paths requires closed_plus because of the details of  
the relax function. By using a new relax_target function it no longer tries  
to create distances above infinity and normal std::plus can be used.

---

## Comment 1

> Username: murraycu  
> Created_at: 2016-08-12 07:48:55 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-239382905  

Isn't that boost::closed_plus itself part of boost graph? What's the advantage to avoiding it?

---

## Comment 2

> Username: ahhz  
> Created_at: 2016-08-24 12:44:22 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-242049220  

Yes, boost::closed_plus is an undocumented part of boost graph.  
  
Subjective reasons:  
1. Getting to understand why BGL uses this closed_plus rather than simple std::plus is an unnecessary complexity.   
2. The recommended fix provides conceptual clarity: rather than doing something special when an INF and normal value are added, it avoids the situation altogether.  
3. The closed_plus functor is ugly, because it only avoids overflow in one particular scenario and not others  
4. The closed_plus functor is intertwined with the INF value, that are both input to the dijkstra functions. Changing one without adjusting the other accordingly will lead to errors.  
5. Customisability: if you want to provide your own plus function, it needs to do the equivalent of std::plus, rather than the more complex boost::closed_plus  
  
Objective reason:  
1. Efficiency: some if-statements are elided every time two values are added.

---

## Comment 3

> Username: murraycu  
> Created_at: 2016-08-24 17:44:57 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-242150248  

Sounds good to me (I'm not the maintainer). I wonder if boost::closed_plus can be removed.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2016-10-31 23:12:12 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257447175  

Your relax_target seems to have omitted the case for undirected graphs where you sum the weight into the target vertex.  Why is that, or am I missing something?

---

## Comment 5

> Username: murraycu  
> Created_at: 2016-11-01 12:53:17 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257559113  

That sounds like something that should have a test.

---

## Comment 6

> Username: ahhz  
> Created_at: 2016-11-01 13:41:59 UTC  
> Updated_at: 2016-11-01 13:52:57 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257569061  

@Belcourt: I don't think that relax_target has omitted anything.   
  
The original relax function does two things:   
  
d(target) = min(d(target), d(source) + w(source, target);  
if undirected:  d(source) = min(d(source), d(target) + w(target, source);  
  
The relax target function only does the following:  
  
d(target) = min(d(target), d(source) + w(source, target);  
  
This is exactly what it is supposed to do, and avoiding the second part is the whole purpose of the relax_target function.  
  
Note that when you pass an edge_descriptor to a function it contains the information about which vertex is the source and which is the target (even for undirected graphs where edges do not really have a source and a target: the context of how an edge is used does have a source and target).  
  
See also the definition of the out_edges function on :   
http://www.boost.org/doc/libs/1_62_0/libs/graph/doc/IncidenceGraph.html   
  
@murraycu: Yes, there should be such tests already. After all a shortest path analysis on an undirected graph is not exactly a corner case.

---

## Comment 7

> Username: Belcourt  
> Created_at: 2016-11-01 17:57:03 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257641078  

I'll have to write a test case but I'm suspicious that you'll update the wrong vertex distance in the undirected case.

---

## Comment 8

> Username: ahhz  
> Created_at: 2016-11-01 18:28:44 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257651994  

Do you know why you would think that? To me it is clear that the edge_descriptors that you get out of the out_edges function are 'correct' in the sense that the source(e) is the vertex of which we are considering all the adjacent vertices and that target(e) is always one of the adjacent vertices. In the dijkstra algorithm we only update the target of the edge, and hence any part of the relax function that updates the source can be removed from the relax_target function, which is what is being proposed here.  
  
If your test would fail, I would think that your graph class is not a proper model of IncidenceGraph.

---

## Comment 9

> Username: Belcourt  
> Created_at: 2016-11-01 19:13:05 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257664466  

I'm pondering this simple test case which shows that relax and relax_target produce different results.  
  
For example: an undirected graph with d_u = 5, d_v = 4, and w_e = 0 results in:  
  
relax updates d_u with 4, updates predecessor map, and returns true  
relax_target doesn't update anything and returns false.  
  
I don't doubt your assessment, I simply don't understand why this wouldn't break someone's code.  That's why I want to create the above as a test case and run it with both the old and new code.

---

## Comment 10

> Username: ahhz  
> Created_at: 2016-11-01 20:25:24 UTC  
> Updated_at: 2016-11-03 09:51:02 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-257684622  

Ah, I am not proposing a blanket replacement of relax for relax_target. However in the dijkstra algorithm d_v is always greater than or equal to d_u. So, in that algorithm you can make the replacement, with the mentioned advantages.

---

## Comment 11

> Username: anadon  
> Created_at: 2018-08-31 18:32:45 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-417753389  

I'm helping out with the PR backlog. Looks like you have a code change.  Reading from the comments here, the changes may not be fully ready.  I would like some feedback on this proposed change.  This is to let you know and help me prioritize PR's.

---

## Comment 12

> Username: ahhz  
> Created_at: 2018-09-03 11:02:03 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-418080595  

As far as I can see, the proposed change is fully ready, but untested outside of my own project. The only remaining change to make is the [documentation](https://www.boost.org/doc/libs/1_68_0/libs/graph/doc/dijkstra_shortest_paths.html);  it should say that the default `CombineFunction` is `std::plus<D>` rather than `boost::closed_plus<D>`.   
  
You could make the equivalent changes for `dijkstra_shortest_paths_no_color_map`.

---

## Comment 13

> Username: anadon  
> Created_at: 2018-09-03 14:05:35 UTC  
> Updated_at: 2018-09-03 14:05:48 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-418125357  

Then please update the documentation and apply similar changes to `diijkstra_shortest_paths_no_color_map`.

---

## Comment 14

> Username: ahhz  
> Created_at: 2018-09-03 15:06:55 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-418140836  

OK, added those updates. I don't know what the issue with subgraph.html is. That was another fix that was somehow merged into this one.

---

## Comment 15

> Username: anadon  
> Created_at: 2018-09-03 15:44:19 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-418148921  

Thanks!  If you see other related cleanups or changes either let me know to add them to this or another PR as appropriate.  I'd like to get this repo fixed up.

---

## Comment 16

> Username: anadon  
> Created_at: 2018-10-15 19:34:46 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-429984813  

@ahhz Can you reabse on devel?

---

## Comment 17

> Username: ahhz  
> Created_at: 2018-10-16 08:45:05 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430153446  

>   
>   
> @ahhz Can you reabse on devel?  
  
I think I just did, but don't have much experience with git. Note that the "fixed type" and "coding style" fixes are not mine and were merged into my PR for some reason. I don't understand why because they are unrelated.

---

## Comment 18

> Username: anadon  
> Created_at: 2018-10-16 18:24:58 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430345504  

@ahhz Looks like something went wrong.  I don't think people can hijack PRs like that, so to you rebase off of the boostorg fork branch devel?

---

## Comment 19

> Username: ahhz  
> Created_at: 2018-10-17 06:05:40 UTC  
> Updated_at: 2018-10-17 09:10:00 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430500961  

Ok, I will have another go. But could you be more precise in what you need me to do. What I did was  
  
git rebase origin:develop alex_relax_target  
  
And then resolve a trivial merge conflict through the github web-page.  
  
Now I need to undo this and use a better command. Any suggestion on how I do that?

---

## Review 20 [Commented]

> Username: anadon  
> Created_at: 2018-10-18 07:06:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/48#pullrequestreview-165949202  

📁 include/boost/graph/dijkstra_shortest_paths_no_color_map.hpp

```diff
  99 |         // Attempt to relax the edge
 100 |-         bool was_edge_relaxed = relax(current_edge, graph, weight_map,
 100 |+         bool was_edge_relaxed = relax_target(current_edge, graph, weight_map,
```

> Username: anadon  
> Created_at: 2018-10-18 07:06:16 UTC  
> Updated_at: 2019-03-08 16:45:08 UTC  
> Url: https://github.com/boostorg/graph/pull/48#discussion_r226188707  

Why change the function called?


---

## Comment 21

> Username: anadon  
> Created_at: 2018-10-18 07:13:03 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430900282  

You're making this commit to reduce a header dependency, but don't remove any include.  Also, std::plus is C++14 and would bump the version requirement of graph.  There are also a large number of commits which look like they're formatting changes not necessarily related to this PR.  These are partly why it is harder for you to get things to work.  
  
Let's get this logically sorted out.  What are you trying to achieve with that PR?  How is what is already here insufficient?  Why make these changes?  
  
It looks like you're newer to coding and repo management so let's take this slow.

---

## Comment 22

> Username: ahhz  
> Created_at: 2018-10-18 07:14:43 UTC  
> Updated_at: 2018-10-18 08:21:27 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430900663  

The original function was doing more than it should do. It checks whether to reduce the distance of the source  of the relaxed edge and that of the target of the relaxed edge, but in the dijkstra algorithm only the distance of the target edge can be reduced.

---

## Comment 23

> Username: ahhz  
> Created_at: 2018-10-18 07:23:04 UTC  
> Updated_at: 2018-10-18 08:22:59 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430902757  

I am only new to this collaborative repo management. Why do you say that std::plus is C++14. Only std::plus without template argument is C++14. I had two files in my original PR that change only the required functionality. This change is also described and motivated in the PR text. The formatting changes were added by someone else and I should have not accepted those. I then added further changes as you requested (docs and the other dijkstra function) all of the other changes are from a misguided rebase, where I hadn't updated the develop branch before rebasing to it.

---

## Comment 24

> Username: anadon  
> Created_at: 2018-10-18 07:31:29 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430905007  

Sorry, misread the documentation.  It is older.  
  
I see the 'avoid requirement of boost::closed_plus' but that is unrelated to what you are saying is incorrect behavior in dijkstra_shortest_path() from relax().  So please answer the contextual questions listed above.

---

## Comment 25

> Username: anadon  
> Created_at: 2018-10-18 07:44:59 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430908735  

The use of closed_plus was to cope with weights of positive infinity and does not pull in an extra header.  This should not be changed.  
  
relax() looks like it is doing what it should be doing.  How is relax() currently breaking your implementation?

---

## Comment 26

> Username: ahhz  
> Created_at: 2018-10-18 07:52:36 UTC  
> Updated_at: 2018-10-18 08:24:17 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430910883  

It is not incorrect behavior but superfluous behavior. The consequence of that is a sum of target_weight plus edge_weight which necessitated using closed_plus. Getting rid of the superfluous stuff also gets rid of the closed_plus requirement. I think I responded to all concerns and questions. The problem is that those concerns were raised without consideration or understanding of how the dijkstra sp algorithm works.

---

## Comment 27

> Username: anadon  
> Created_at: 2018-10-18 08:02:22 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430913743  

If this is in the name of performance, then this PR will be declined.  That sort of superfluous behavior that some code needs is generally removed by the compiler.  Trust the compiler to optimize obvious things, and even some not so obvious things.

---

## Comment 28

> Username: ahhz  
> Created_at: 2018-10-18 08:12:09 UTC  
> Updated_at: 2018-10-18 08:41:44 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430916576  

I have elaborately explained why I proposed this change. Did you not review the PR thread? Can we only propose improvements for code that is breaking implementations? Closed_plus is not to deal with edges of infinite weight, at least not in the dijkstra algorithm. If you read up on the documentation you will see that a requirement/assumption is that edge_weight < INF. Rather, in the dijkstra algorithm closed_plus dealt with vertices at infinite distance (which is not necessary when cutting superfluous branches from the relax function, which is what relax_target does).

---

## Comment 29

> Username: ahhz  
> Created_at: 2018-10-18 08:14:28 UTC  
> Updated_at: 2018-10-18 08:31:23 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430917269  

Did you not read the PR thread? I have explained why I proposed this change as response to an earlier question.

---

## Comment 30

> Username: anadon  
> Created_at: 2018-10-18 08:31:08 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430922409  

Your PR does not fix incorrect behavior, and the possible performance improvements are already done by the compiler.  
  
There is incorrect behavior introduced in the case that edge weight types roll over like an int, rather than hit a ceiling case like float.  This actually exposes a minor bug in closed_plus{} that I'll need to make a patch for.  On large graphs or graphs with large weights, the standard behavior desired differs from that of std::plus() because edges need to hit and stay at that maximum value.  
  
Be less mean when challenged.  Insulting me does no good to anyone, and can poison a community.

---

## Comment 31

> Username: ahhz  
> Created_at: 2018-10-18 08:36:03 UTC  
> Updated_at: 2018-10-18 08:43:12 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430923926  

How did I insult you?

---

## Comment 32

> Username: anadon  
> Created_at: 2018-10-18 08:44:11 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430926443  

>The problem is that those concerns were raised without consideration or understanding of how the dijkstra sp algorithm works.  
  
>Did you not read the PR thread? I have explained why I proposed this change as response to an earlier question.  
  
Insinuating that I don't know basic algorithms and the accusatory questions.  If you are not aware how this came off, you really need to become aware.  
  
But in any case, there is incorrect behavior which could be introduced so I'm disinclined to have this merged.

---

## Comment 33

> Username: ahhz  
> Created_at: 2018-10-18 09:01:49 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430932055  

> Insinuating that I don't know basic algorithms  
  
I would be highly surprised if you did not know the dijkstra shortest path algorithm. My point was that you and earlier commenters on this PR responded in a very literal way to the code, saying that my proposed change was not doing the same as the original code, i.e. relax != relax_target. My response has been that it is indeed not the same, but it is the same for the dijkstra algorithm, considering the way it works through the vertices in a particular order. If you go back in the PR thread you will see that that then stopped the discussion, which to me indicated an unwillingness to engage with the actual algorithm and a desire to just look at code equivalency.   
  
> the accusatory questions.  
  
Yes, I see this comes across as unfriendly, apologies. At the same time, you must see that I can be a bit irritated when first I am asked to revisit and update a three year old PR (and it is actually older as I had something similar in Trac before, and then after doing so I get the same questions again that I answered in detail long before, and which has been recorded on this very same page.

---

## Comment 34

> Username: ahhz  
> Created_at: 2018-10-18 12:45:37 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-430994476  

> There is incorrect behavior introduced in the case that edge weight types roll over like an int, rather than hit a ceiling case like float  
  
If the weight type is insufficient to hold the maximum distance, you have bigger problems and it would violate requirements / assumptions of the algorithm. For instance, it would fail to reach vertices that are in fact reachable.    
  
I don't know what bug you saw in closed_plus, but it  should be clear that this form of addition does not give general protection against overflows, it only treats the INF value as a special case, e.g. 0.7 * inf + 0.7 * inf would still overflow.   
  
I don't think this is a bug though, trying to snap all overflows in closed_plus to  INF (other than those involving the special INF value)  would be hiding problems instead of solving them.  
  
What has it to do with rolling over? Aren't overflows equally problematic for int and float, except that for float you have additional problems due to limited precision?

---

## Comment 35

> Username: anadon  
> Created_at: 2018-11-01 14:15:38 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-435053769  

@ahhz Please rebase your PR on the upstream devel branch.

---

## Comment 36

> Username: ahhz  
> Created_at: 2019-03-08 18:05:13 UTC  
> Url: https://github.com/boostorg/graph/pull/48#issuecomment-471021094  

I tried too many thing trying to revert my incorrect changes, then incorrect reverts, etc. I have re-cloned the repository and made a fresh PR based on the current develop, it is #165   
  
This PR is therefore closed

---
