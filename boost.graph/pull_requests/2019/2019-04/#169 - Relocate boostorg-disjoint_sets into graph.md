# #169 Relocate boostorg/disjoint_sets into graph [Merged]

> Username: jeking3  
> Created at: 2019-04-26 18:50:15 UTC  
> Updated at: 2019-05-02 17:49:42 UTC  
> Merged at: 2019-05-02 17:22:51 UTC  
> Closed at: 2019-05-02 17:22:51 UTC  
> Url: https://github.com/boostorg/graph/pull/169  

Reasons for this:  
  
1. The headers in boostorg/disjoint_sets have a dependency on graph, causing a cycle; this resolves the cycle.  
2. Maintaining a repository for one class like disjoint_sets is unreasonable overhead.  
3. The only consumers of disjoint_sets are graph and graph_parallel, and graph_parallel depends on graph.  
  
The disjoint_sets headers are still in boost/pending, as graph already has a number of these.  
The history of the files in disjoint_sets has not been preserved (I believe this would require a rebase and force push to achieve, and does not seem worth the trouble it would cause).  
  
Completing this work will require some coordination - pull requests after this will be:  
  
* Removing disjoint_sets from the superproject.  
* Removing disjoint_sets from the boostorg exceptions. cc: @pdimov   
  
This may require an announcement for all developers so they can update their local sandboxes.  
  
This fixes #113

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-04-26 19:42:47 UTC  
> Updated_at: 2019-05-02 11:30:57 UTC  
> Url: https://github.com/boostorg/graph/pull/169#issuecomment-487177795  

It was suggested on the boost mailing list that it is acceptable for the builds to be broken during the migration, since our multirepo layout does not handle this type of consolidation well.  If you would consider merging this, I will submit a pull request into boostorg to remove disjoint_sets as a project.  Things build locally with this PR in place and the original repository removed, and a clean build is performed.

---

## Comment 2

> Username: anadon  
> Created_at: 2019-05-02 14:19:23 UTC  
> Url: https://github.com/boostorg/graph/pull/169#issuecomment-488691883  

@jzmaddock Can we try this out?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-05-02 17:32:17 UTC  
> Url: https://github.com/boostorg/graph/pull/169#issuecomment-488761742  

Hopefully I've managed to coordinate these changes with disjoint_sets without breaking anything.... but we'll see!

---
