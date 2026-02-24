# #1156 Update circleCI scripts and image [Merged]

> Username: vissarion  
> Created at: 2023-06-01 07:54:09 UTC  
> Updated at: 2023-07-25 12:13:52 UTC  
> Merged at: 2023-07-25 12:13:48 UTC  
> Closed at: 2023-07-25 12:13:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156  

Following the discussion [here](https://github.com/boostorg/geometry/discussions/1155) this PR proposes:  
  
- to update the circleCI image to a next-gen convenience image (read [this](https://circleci.com/docs/next-gen-migration-guide/) for the advantages of this change). Moreover, following the discussion [here](https://github.com/boostorg/geometry/pull/1147) the compiler is set to gcc-6 to possible catch library issues,  
- add ~~a closing job to circleCI to cover~~ missing tests.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-07-02 09:05:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1156#pullrequestreview-1509514436  

I'm not an expert here.  
I welcome improvements so I approve this.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2023-07-20 23:28:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1156#pullrequestreview-1540274907  

📁 .circleci/config.yml

```diff
 433 |+     steps:
 434 |+       - *attach_workspace
 435 |+       - run: ./$BOOST_DIR/libs/geometry/.circleci/run_test.sh
```

> Username: awulkiew  
> Created_at: 2023-07-20 23:28:05 UTC  
> Updated_at: 2023-07-20 23:32:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156#discussion_r1270095848  

What was the intention here? This script requires parameters, without it it will not run anything, maybe even fail.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2023-07-20 23:31:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156#issuecomment-1644785326  

Thanks for this update!  
  
Btw, I'd be ok with using the default gcc version. It's probably more commonly used (I assume it's ubuntu 20.04) and requires to do less manual steps which have tendency to fail.  
  
FYI, currently the coverage step is failing due to some issues with package repositories so this change could fix it, maybe.  
  
Have you tested whether or not it works with your fork by pushing develop or master branch and checking at CircleCI?  
Even if you havent I'd be ok with pushing this change and finding out this way. In the worst case we could always reverse it.

---

## Comment 4

> Username: vissarion  
> Created_at: 2023-07-21 13:09:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156#issuecomment-1645564114  

Hi @awulkiew thanks for the review. I revised my PR by adding a more straightforward solution, i.e. manually adding the missing tests. The previous approach by adding a closing job was end up too complicated (since I had to add also special cases to the `run_test.sh` and I think it doesn't worth the trouble. Now I am running circleci tests in my fork.

---

## Comment 5

> Username: vissarion  
> Created_at: 2023-07-21 13:11:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156#issuecomment-1645565364  

There is also this related PR https://github.com/boostorg/geometry/pull/1154 where I need your feedback.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2023-07-21 21:25:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1156#issuecomment-1646253614  

Thanks, I'm ok with merging it.  
  
Regarding testing in your fork, AFAIR you have to push them to your develop branch because otherwise minimal test is tested. You can also modify the script temporarily to always run it for all branches. Unless I'm mistaken.

---
