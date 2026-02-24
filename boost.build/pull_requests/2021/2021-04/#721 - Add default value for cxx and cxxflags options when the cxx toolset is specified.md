# #721 Add default value for cxx and cxxflags options when the cxx toolset is specified [Closed]

> Username: sdebionne  
> Created at: 2021-04-22 13:00:36 UTC  
> Updated at: 2021-10-02 20:59:39 UTC  
> Closed at: 2021-04-28 15:27:05 UTC  
> Url: https://github.com/boostorg/build/pull/721  

As discussed on [slack](https://cpplang.slack.com/archives/C27KZLB0X/p1619028622427200), this PR adds default values for `--cxx` and `--cxxflags` options for the `cxx`  toolset:   
  
```  
--cxx=[$CXX]  
--cxxflag=[$CXXFLAGS]  
```  
  
This allows to run `./bootstrap.sh --with-toolset=cxx` and get what I think is the expected behavior.

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-04-22 13:23:59 UTC  
> Url: https://github.com/boostorg/build/pull/721#issuecomment-824838389  

https://github.com/boostorg/build/pull/717#issuecomment-808516262

---

## Comment 2

> Username: sdebionne  
> Created_at: 2021-04-23 07:20:52 UTC  
> Url: https://github.com/boostorg/build/pull/721#issuecomment-825450103  

Cross referencing a typical problematic build (conda-forge/boost-cpp-feedstock#93) that this MR try to help with.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2021-04-28 15:27:05 UTC  
> Url: https://github.com/boostorg/build/pull/721#issuecomment-828549215  

As requested by the maintainers, move the PR to bfgroup/b2#24.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:39 UTC  
> Url: https://github.com/boostorg/build/pull/721#issuecomment-932819679  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
