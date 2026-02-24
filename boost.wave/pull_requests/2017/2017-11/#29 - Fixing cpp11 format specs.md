# #29 Fixing cpp11 format specs [Merged]

> Username: jefftrull  
> Created at: 2017-11-30 19:31:39 UTC  
> Updated at: 2017-12-01 02:50:28 UTC  
> Merged at: 2017-12-01 02:50:28 UTC  
> Closed at: 2017-12-01 02:50:28 UTC  
> Url: https://github.com/boostorg/wave/pull/29  

Wave uses the "%zd" format spec in a couple of places.  Unfortunately, the "z" modifier was introduced in C+11 and so it does not work with old versions of MSVC.  This commit replaces `sprintf` with `boost::lexical_cast`.

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2017-11-30 20:10:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/29#pullrequestreview-80314216  

📁 include/boost/wave/util/cpp_iterator.hpp

```diff
 811 |-                 using namespace std;    // for some systems sprintf is in namespace std
 812 |-                 sprintf (buffer, "%zd", pos.get_line());
 811 |+ 		std::string buffer = lexical_cast<std::string>(pos.get_line());
```

> Username: hkaiser  
> Created_at: 2017-11-30 20:10:58 UTC  
> Updated_at: 2017-11-30 20:23:11 UTC  
> Url: https://github.com/boostorg/wave/pull/29#discussion_r154187833  

The indentation got messed up somehow.

> Username: jefftrull  
> Created_at: 2017-11-30 20:21:08 UTC  
> Updated_at: 2017-11-30 20:25:11 UTC  
> Url: https://github.com/boostorg/wave/pull/29#discussion_r154190175  

That whole region is strange.... I'll clean it up.


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2017-12-01 02:50:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/29#pullrequestreview-80394256  

LGTM, thanks!

---
