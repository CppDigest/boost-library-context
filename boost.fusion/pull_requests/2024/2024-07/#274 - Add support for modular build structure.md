# #274 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:28:45 UTC  
> Updated at: 2024-08-22 01:51:29 UTC  
> Merged at: 2024-08-22 01:51:29 UTC  
> Closed at: 2024-08-22 01:51:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/274  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:59:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/274#issuecomment-2295310066  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: djowel  
> Created_at: 2024-08-19 02:47:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/274#issuecomment-2295565848  

"All checks have failed". Unrelated?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-08-19 03:28:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/274#issuecomment-2295597525  

> "All checks have failed". Unrelated?  
  
As far as I know it's unrelated. The same errors appear in develop branch. And when I tested fusion independently it works..  
- https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894201599  
- https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894200550

---

## Comment 4

> Username: djowel  
> Created_at: 2024-08-19 04:08:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/274#issuecomment-2295628918  

> > "All checks have failed". Unrelated?  
>   
> As far as I know it's unrelated. The same errors appear in develop branch. And when I tested fusion independently it works..  
>   
> * https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894201599  
> * https://github.com/grafikrobot/boost-b2-modular/actions/runs/10432959230/job/28894200550  
  
`GLIBC_2.27' not found hmmm...

---
