# #467 Undo PR #465 now that the CXX is cleared in bootstrap.sh. [Merged]

> Username: grafikrobot  
> Created at: 2021-01-20 01:21:08 UTC  
> Updated at: 2021-01-22 19:36:20 UTC  
> Merged at: 2021-01-22 19:04:10 UTC  
> Closed at: 2021-01-22 19:04:10 UTC  
> Url: https://github.com/boostorg/boost/pull/467  



---

## Comment 1

> Username: pdimov  
> Created_at: 2021-01-22 17:50:59 UTC  
> Url: https://github.com/boostorg/boost/pull/467#issuecomment-765582548  

Everything seems to work now except the first job that uses `ci_boost_release.py` fails for some reason. https://travis-ci.org/github/boostorg/boost/jobs/755274550  
  
I see no difference that could explain it; neither CXX nor CXXFLAGS seems to be set by the script.  
  
Maybe when it fails bootstrap (or build.sh) should print why it's chosen the cxx toolset? Or it could print it unconditionally. E.g. "using cxx toolset because CXX=..., CXXFLAGS=..." or something like that.

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-01-22 17:54:53 UTC  
> Updated_at: 2021-01-22 17:55:44 UTC  
> Url: https://github.com/boostorg/boost/pull/467#issuecomment-765584705  

```  
        # Bootstrap Boost Build engine.  
        os.chdir(os.path.join(self.root_dir,"tools","build"))  
        utils.check_call("./bootstrap.sh")  
```  
  
Maybe this is the reason? It calls tools/build/bootstrap.sh instead of the root one.  
  
Edit: "it" refers to ci_boost_release.py: https://github.com/boostorg/release-tools/blob/b08dbc34451e9f5b09ec44cab53b3598d6747e15/ci_boost_release.py#L132-L134

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2021-01-22 18:59:13 UTC  
> Url: https://github.com/boostorg/boost/pull/467#issuecomment-765620896  

That indeed would be the reason. As the b2/bootstrap.sh does not clear CXX/FLAGS intentionally. As it accepts build.sh arguments also.

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-01-22 19:05:30 UTC  
> Updated_at: 2021-01-22 19:05:46 UTC  
> Url: https://github.com/boostorg/boost/pull/467#issuecomment-765624096  

OK, merged this. Since you wrote `ci_boost_release.py`, do you have any suggestions how to clear `CXX` before invoking `bootstrap` there?

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2021-01-22 19:36:20 UTC  
> Url: https://github.com/boostorg/boost/pull/467#issuecomment-765639526  

Perhaps this.. https://github.com/boostorg/release-tools/pull/20

---
