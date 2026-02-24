# #93 Fix writes/reads outside allocated memory in resource constraints shortest paths [Closed]

> Username: pmateusz  
> Created at: 2017-07-05 13:11:25 UTC  
> Updated at: 2018-10-12 18:22:58 UTC  
> Closed at: 2018-10-12 18:22:58 UTC  
> Url: https://github.com/boostorg/graph/pull/93  

Found an example where existing algorithm performs writes/reads outside allocated memory when using the default allocator. Implemented repo as a test included in the pull request. Valgrind reports 38 reads/writes outside allocated memory when running the test against the original version of the algorithm.  
  
I did not get a segmentation fault on my machine, but the algorithm returned a list of edges that do not form a path (example: 1-5 5-2 2-3 4-0).  
  
Investigation  
If an existing label is found dominated it becomes immediately deallocated. However, pointers to that label still may exist in other partial paths. Further new allocations cause appearance of invalid paths. The assumption that a pareto optimal path consists only of non-dominated labels and therefore deallocating them is not correct (safe) if the dominance function performs weak inequality checks (as suggested by original author and presented in examples).  
  
Fix  
* Replaced a raw pointer to `r_c_shortest_paths_label` with `boost::shared_ptr`. The algorihtm finds shortest paths based on feasibility/dominance criteria specified by user, so there is a risk that pointers may form a cycle. Taking this into account the algorihtm resets pointers as the final step. I considered using weak pointers in the `r_c_shortest_paths_label`, but there are examples where such approach do not work. Provided it as a comment inside the algorithm code.  
* Replaced `ks_smart_pointer` by `boost::shared_ptr`. The former class did not offer the smart_pointer functionality and is not necessary.  
* Removed the `b_is_valid` checks. It is possible to come up with examples, where this approach will report false negatives (provided an example in comments). It is the same reason why weak_pointers will not work.  
* Added a test case to prevent regressions.  
  
Valgrind does not detect any leaks or possible leaks when running the test against the fixed algorithm.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:43:17 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-407725817  

Hi Mateusz. This sounds like a pretty serious bug and an important fix. I'm not an expert on this algorithm, but I've looked pretty closely through the code and it all looks good.  
Sorry, what is the "reporo" that you refer to as a test?  
Have you done any benchmarks just to check that the change hasn't inadvertently been deleterious to performance? (I personally wouldn't care about minor variation.)  
I'm not the maintainer, btw, I just decided to start actively caring about a library that I use.  :)

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:44:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/93#pullrequestreview-140273277  

📁 test/r_c_shortest_paths_test.cpp

```diff
 282 |+     }
 283 |+ };
 284 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:44:23 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r205077290  

I think some brief comments on these new classes would help the reviewers understand what they do.


---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:45:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/93#pullrequestreview-140273515  

📁 include/boost/graph/r_c_shortest_paths.hpp

```diff
  25 | template<class Graph, class Resource_Container>
  24 |- struct r_c_shortest_paths_label
  26 |+ struct r_c_shortest_paths_label : public boost::enable_shared_from_this<r_c_shortest_paths_label<Graph, Resource_Container> >
```

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:45:21 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r205077503  

I've never used `enable_shared_from_this`; what does it do?

> Username: pmateusz  
> Created_at: 2018-07-25 16:05:46 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r205169835  

[enable_shared_from_this](https://stackoverflow.com/questions/712279/what-is-the-usefulness-of-enable-shared-from-this) is a C++11 construct. See the link, it is better explained than I could.


---

## Comment 4

> Username: pmateusz  
> Created_at: 2018-07-25 16:06:09 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-407808334  

Hi @jeremy-murphy, I answered to your questions below.  
* By _reporo_ I mean repro case. Sorry for my typo. I fixed the comment. I was referring to the new test case I added.  
* Good questions about performance tests. I didn't do them. Changes proposed are to replace the proprietary smart pointer with the boost one. Moreover, I am not proposing an improvement, rather a fix for critical issue, so possible performance impact should be justifiable.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2018-07-27 23:59:54 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-408566139  

You're right, and I didn't mean to cast any doubt about performance, it's just a standard question. I assume you would have noticed if it was 10x slower, which is the kind of accidental performance bug I mean.  
Well, I hope a maintainer has time to review it soon, as I think it really deserves to be merged asap.  
@Belcourt are you currently the right person to gently remind about these PRs?

---

## Comment 6

> Username: pmateusz  
> Created_at: 2018-07-29 16:58:03 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-408690764  

@jeremy-murphy, it seems there is a ticket https://svn.boost.org/trac10/ticket/11723 with a similar issue. I asked the owner for a suggestion about next steps.

---

## Comment 7

> Username: anadon  
> Created_at: 2018-08-31 17:52:57 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-417742445  

I'm helping out with the PR backlog. Looks like you have a bugfix, test, and no new examples will be needed.  This will be in the first batch of merged PR's.  I will need to take time to understand this issue and fix more.  This is to let you know and help me prioritize PR's.

---

## Comment 8

> Username: Belcourt  
> Created_at: 2018-08-31 17:57:16 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-417743682  

I'm having trouble building graph / develop following all these PR's.  Do you, by chance, test each PR by building and running graph, graph parallel, and MPI tests before pushing it?  It might be wise to slow down so we don't irreversibly break something.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-08-31 18:53:20 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-417758844  

Right now I'm letting people know that something is happening, adding some documentation, and prioritizing PR's to mess with.  Some of these old PR's need to be rebased so I'm stirring things up a little bit to get the submitters to check in and to know things are happening.  I'll be reading, testing, merging, and testing again PR's I say will be in the first batch of merges

---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2018-09-29 23:00:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/93#pullrequestreview-160095882  

📁 include/boost/graph/r_c_shortest_paths.hpp

```diff
  30 |     const Resource_Container& rc = Resource_Container(), 
  29 |-     const r_c_shortest_paths_label* const pl = 0, 
  31 |+     const boost::shared_ptr<r_c_shortest_paths_label<Graph, Resource_Container> > pl = nullptr,
```

> Username: pdimov  
> Created_at: 2018-09-29 23:00:33 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442195  

nullptr is C++11, the C++03 tests will fail.

> Username: anadon  
> Created_at: 2018-09-29 23:03:54 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442240  

This was fixed in #114.

> Username: pdimov  
> Created_at: 2018-09-29 23:06:18 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442273  

It's better to fix it here, if possible. It's easier to review and merge PRs one by one (and it would be easier to revert them later if some prove problematic.)

> Username: anadon  
> Created_at: 2018-09-29 23:12:23 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442352  

You can.  I'm new and don't have write access so having one place to work on keeps things a tad more sane.  It fixing commit was 6808062c07be7e394223819ea703ccb2c98e84a1 .

> Username: pdimov  
> Created_at: 2018-09-29 23:20:49 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442479  

No, I can't fix people's PRs, they have to do that themselves. :-)  
  
I'm trying to save you trouble in the long run. Batching up unrelated changes in one PR is not good workflow. It's better to either request the needed PR changes from the original submitters, who are usually happy to help, or, if you want to fix their PRs yourself, you could create one new PR per each original PR, so that the changes can be reviewed and applied in isolation.

> Username: pmateusz  
> Created_at: 2018-10-06 20:39:45 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r223193161  

Thanks, @pdimov, I reapplied @anadon commit that removes usage of `nullptr` and removes spaces from end of lines.


---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2018-09-29 23:02:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/93#pullrequestreview-160095922  

📁 test/r_c_shortest_paths_test.cpp

```diff
 209 |+             return *this;
 210 |+         this->~spp_spptw_marked_res_cont();
 211 |+         new( this ) spp_spptw_marked_res_cont( other );
```

> Username: pdimov  
> Created_at: 2018-09-29 23:02:53 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r221442226  

Invoking the destructor and then the constructor is not a good way to write an assignment operator. Perhaps it works here, I haven't checked, but it's not a good practice and it would be better to rewrite it in a more conventional way.

> Username: pmateusz  
> Created_at: 2018-10-06 20:47:24 UTC  
> Updated_at: 2018-10-07 12:11:02 UTC  
> Url: https://github.com/boostorg/graph/pull/93#discussion_r223193376  

@pdimov, I agree. I implemented proper assignment operators in tests whenever possible. Only one has left in https://github.com/boostorg/graph/blob/develop/include/boost/graph/r_c_shortest_paths.hpp#L47. See this is part of released Boost. It was done by the original developer of the feature to allow reassignment of fields marked as const in the struct definition.  
  
I was not changing it deliberately, because it has been already released and it is not causing any bugs as far as I am concerned. It is quite unusual construct to use without a reason.


---

## Comment 12

> Username: anadon  
> Created_at: 2018-10-07 06:43:40 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-427630110  

Try rebasing on the current develop branch.  I think some of your changes were already added so it won't be trivial to merge.

---

## Comment 13

> Username: pmateusz  
> Created_at: 2018-10-07 22:31:55 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-427692858  

Recent TravisCI failure in a single build is a timeout in the mas_test.cpp test, which is unrelated to changes in this thread.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-10-07 23:20:41 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-427695792  

I restarted the timed-out job and it passed, so it's all clear now.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2018-10-12 17:38:34 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-429403666  

Are these changes in https://github.com/boostorg/graph/pull/114?  If so it can be closed now.

---

## Comment 16

> Username: anadon  
> Created_at: 2018-10-12 18:14:13 UTC  
> Url: https://github.com/boostorg/graph/pull/93#issuecomment-429413629  

These changes were included in #114 and so this should be closed.

---
