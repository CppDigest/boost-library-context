# #637 The correct option to activate C++11 with Intel compiler is -std=c++11 [Closed]

> Username: aminiussi  
> Created at: 2020-07-13 15:37:50 UTC  
> Updated at: 2021-10-02 21:01:44 UTC  
> Closed at: 2020-07-15 13:31:13 UTC  
> Url: https://github.com/boostorg/build/pull/637  

This is a followup of #635

---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2020-07-13 17:41:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/637#pullrequestreview-447464008  

📁 src/engine/build.sh

```diff
  83 |             intel-darwin) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
  84 |-             intel-linux) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
  84 |+             intel-linux) ( ${CXX:=icc} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
```

> Username: ax3l  
> Created_at: 2020-07-13 17:41:26 UTC  
> Updated_at: 2020-07-15 12:33:11 UTC  
> Url: https://github.com/boostorg/build/pull/637#discussion_r453819558  

For consistency and technical correctness, the C++ compiler is `icpc`:  
```diff  
-            intel-linux) ( ${CXX:=icc} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
+            intel-linux) ( ${CXX:=icpc} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
```

> Username: aminiussi  
> Created_at: 2020-07-14 08:30:07 UTC  
> Updated_at: 2020-07-15 12:33:11 UTC  
> Url: https://github.com/boostorg/build/pull/637#discussion_r454192148  

Ok, but should we do a query-replace ? some icc seems to be looked up in absolute path that probably made sense on someone's installation.  
  
Or should we replace all of them but the absolute path ?


---

## Comment 2

> Username: ax3l  
> Created_at: 2020-07-13 17:42:17 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-657697293  

As documented [here](https://github.com/boostorg/build/pull/635#issuecomment-657264482), please also update the ancient auto-detect logic to source `/opt/intel/inteloneapi/setvars.sh`

---

## Comment 3

> Username: aminiussi  
> Created_at: 2020-07-14 08:32:27 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658048768  

> As documented [here](https://github.com/boostorg/build/pull/635#issuecomment-657264482), please also update the ancient auto-detect logic to source `/opt/intel/inteloneapi/setvars.sh`  
  
But that path is specific to an installation. Do we know on what machine host that installation and do we have access to it ?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2020-07-14 09:09:54 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658067747  

I think it makes sense to add that path to the logic here: https://github.com/boostorg/build/blob/b207584f8c99575330121b60d5b844079aaf9663/src/engine/build.sh#L128-L142  
  
They use similar style checks. It is a heuristic and as such IMO fine, although I'd place such stuff at the bottom of the if-elseif chain but it already is like that :shrug:

---

## Comment 5

> Username: Flamefire  
> Created_at: 2020-07-15 12:12:21 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658731502  

Please check how to use `git rebase -i origin/master` after `git fetch origin` (if `origin` is the name of the boostorg/build remote)

---

## Comment 6

> Username: aminiussi  
> Created_at: 2020-07-15 12:35:33 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658741282  

> Please check how to use `git rebase -i origin/master` after `git fetch origin` (if `origin` is the name of the boostorg/build remote)  
  
Should I rebase on master or develop ?

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-07-15 12:37:35 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658742134  

> > Please check how to use `git rebase -i origin/master` after `git fetch origin` (if `origin` is the name of the boostorg/build remote)  
>   
> Should I rebase on master or develop ?  
  
develop

---

## Comment 8

> Username: aminiussi  
> Created_at: 2020-07-15 13:03:52 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658754517  

> > > Please check how to use `git rebase -i origin/master` after `git fetch origin` (if `origin` is the name of the boostorg/build remote)  
> >   
> >   
> > Should I rebase on master or develop ?  
>   
> develop  
  
Ok, too messy to roll back then, I'll do a new pull request.

---

## Comment 9

> Username: aminiussi  
> Created_at: 2020-07-15 13:31:13 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-658769255  

Ok pull request #639  should do it.

---

## Comment 10

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:43 UTC  
> Url: https://github.com/boostorg/build/pull/637#issuecomment-932819952  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
