# #212 Explicitly delete all non implemented assignment operators. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-17 14:17:31 UTC  
> Updated at: 2019-11-19 01:40:13 UTC  
> Merged at: 2019-11-19 01:40:12 UTC  
> Closed at: 2019-11-19 01:40:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/212  

Commit (almost) automatically generated with the following sed command:  
sed -i -ne '1h;1!H;${g;s|\n[[:blank:]]*private:\n\([[:blank:]]*\)// silence MSVC warning C4512: assignment operator could not be generated\n\([[:blank:]]*\)\([^\n]\+\);\n|\n\1// silence MSVC warning C4512: assignment operator could not be generated\n\2BOOST_DELETED_FUNCTION(\3);\n|g;p}' $(git ls-files)  
  
Hi,  
  
This is needed to silence some gcc 9 warnings about deprecated implicit copy ctor/operators.  
  
See the discussion we had in https://github.com/boostorg/spirit/pull/488 and the similar pull request that was merged in Boost Spirit here: https://github.com/boostorg/spirit/pull/489  
  
FYI @Kojoley   
  
Cheers,  
Romain

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-20 15:37:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/212#pullrequestreview-228881888  

📁 include/boost/fusion/adapted/boost_tuple/boost_tuple_iterator.hpp

```diff
 147 |         // silence MSVC warning C4512: assignment operator could not be generated
 149 |-         boost_tuple_iterator& operator= (boost_tuple_iterator const&);
 148 |+         BOOST_DELETED_FUNCTION(boost_tuple_iterator& operator= (boost_tuple_iterator const&));
```

> Username: Kojoley  
> Created_at: 2019-04-20 15:37:01 UTC  
> Updated_at: 2019-05-08 17:12:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r277138172  

Extra semicolon.

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-08 17:01:36 UTC  
> Updated_at: 2019-05-08 17:12:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282158829  

I have removed the extra semicolons.


---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2019-05-08 17:06:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/212#pullrequestreview-235177592  

📁 include/boost/fusion/view/joint_view/joint_view.hpp

```diff
  73 |+         BOOST_DELETED_FUNCTION(joint_view& operator= (joint_view const&))
  74 | 
  75 |         typename mpl::if_<traits::is_view<Sequence1>, Sequence1, Sequence1&>::type seq1;
```

> Username: Kojoley  
> Created_at: 2019-05-08 17:06:06 UTC  
> Updated_at: 2019-05-08 17:12:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282160353  

Watchout. You have unprotected things.  
  
Now I need to re-review you patch to Spirit =\

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-08 17:13:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282162854  

That was the only place I have found. Normally in Spirit this case did not happen.

> Username: Kojoley  
> Created_at: 2019-05-08 17:28:22 UTC  
> Updated_at: 2019-05-08 17:28:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282168596  

> Normally in Spirit this case did not happen.  
  
It did, in two places.

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-08 17:33:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282170699  

Sorry for that, I was sure that in spirit I took care of checking this when I did the pull request, I was wrong...

> Username: Kojoley  
> Created_at: 2019-05-08 17:36:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/212#discussion_r282171714  

Not a big problem. It actually was my fault that I did not check it then :D


---

## Review 3 [Approved]

> Username: Kojoley  
> Created_at: 2019-11-18 20:32:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/fusion/pull/212#pullrequestreview-318610227  

@djowel can you please merge this?

---
