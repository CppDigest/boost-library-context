# #489 Explicitly delete all non implemented assignment operators. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-16 22:24:33 UTC  
> Updated at: 2019-04-17 13:16:04 UTC  
> Merged at: 2019-04-17 13:16:00 UTC  
> Closed at: 2019-04-17 13:16:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/489  

Commit (almost) automatically generated with the following sed command:  
sed -i -ne '1h;1!H;${g;s|// silence MSVC warning C4512: assignment operator could not be generated\n\([[:blank:]]*\)\([^\n]\+\);\n|// silence MSVC warning C4512: assignment operator could not be generated\n\1BOOST_DELETED_FUNCTION(\2);\n|g;p}' $(git ls-files)  
  
Then all files in the x3 subfolder were reverted to HEAD, and manually  
updated to use " = delete" instead of BOOST_DELETED_FUNCTION.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-16 22:27:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/489#issuecomment-483867177  

> Then all files in the x3 subfolder were reverted to HEAD, and manually  
> updated to use " = default" instead of BOOST_DELETED_FUNCTION.  
  
It should be ` = delete;`, is not it?

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 22:27:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/489#issuecomment-483867341  

> > Then all files in the x3 subfolder were reverted to HEAD, and manually  
> > updated to use " = default" instead of BOOST_DELETED_FUNCTION.  
>   
> It should be ` = delete;`, is not it?  
  
Yes I just saw the mistake as well, and fixed both the diff + the commit message + the PR description.

---

## Review 3 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-16 22:30:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/489#pullrequestreview-227476468  

📁 include/boost/spirit/home/karma/auto/auto.hpp

```diff
 148 | 
 149 |     private:
```

> Username: Kojoley  
> Created_at: 2019-04-16 22:30:32 UTC  
> Updated_at: 2019-04-16 22:36:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/489#discussion_r276018073  

Those should be removed.

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 22:36:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/489#discussion_r276019520  

Done


---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-04-17 13:16:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/489#issuecomment-484085026  

Thanks!

---
