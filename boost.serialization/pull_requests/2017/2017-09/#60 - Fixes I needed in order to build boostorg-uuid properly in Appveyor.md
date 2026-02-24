# #60 Fixes I needed in order to build boostorg/uuid properly in Appveyor [Closed]

> Username: jeking3  
> Created at: 2017-09-08 21:57:01 UTC  
> Updated at: 2017-10-10 14:15:41 UTC  
> Closed at: 2017-10-10 14:15:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/60  

I was not able to build boostorg/uuid in appveyor with the develop branch of serialization nor with the master branch of serialization, I ran into issues 12205, 12450, 12741.  
  
This pull request has fixes for codecvt_null<wchar_t> and singleton_module::get_lock undefined at link time - these fixes allowed me to build boostorg/uuid on appveyor and travis ci properly, and may address trac items 12205, 12450, 12741  
  
Example of a failing job:  
  
https://ci.appveyor.com/project/jeking3/uuid/build/1.0.6-develop/job/otocinjh2pu7xbun  
  
A more complete Appveyor build showing the build of serialization on appveyor with a variety of compilers - I decided to decouple the appveyor and travis upgrades from these code fixes for this submission into serialization however:  
  
https://ci.appveyor.com/project/jeking3/serialization  
  
Finally, here's a build job on Appveyor with these changed patched in, it looks like there are still mingw issues in serialization:  
  
https://ci.appveyor.com/project/jeking3/uuid/build/1.0.10-develop

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2017-09-08 22:02:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/60#pullrequestreview-61633344  

📁 include/boost/archive/codecvt_null.hpp

```diff
  65 | template<>
  66 | class BOOST_SYMBOL_VISIBLE codecvt_null<wchar_t> : public std::codecvt<wchar_t, char, std::mbstate_t>
  67 | {
```

> Username: jeking3  
> Created_at: 2017-09-08 22:02:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#discussion_r137898892  

Below: In both cases BOOST_WARCHIVE_DECL is already setting export, so this resolves a warning.


---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2017-09-08 22:03:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/60#pullrequestreview-61633548  

📁 include/boost/archive/codecvt_null.hpp

```diff
  92 |         return do_encoding();
  93 |     }
  94 | public:
```

> Username: jeking3  
> Created_at: 2017-09-08 22:03:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#discussion_r137899062  

Below: the body of the constructor is defined inline here and therefore does not need to be exported.


---

## Review 3 [Commented]

> Username: jeking3  
> Created_at: 2017-09-08 22:04:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/60#pullrequestreview-61633721  

📁 test/Jamfile.v2

```diff
 133 | 
 134 | if ! $(BOOST_ARCHIVE_LIST) {
 135 |     test-suite "serialization2" :
```

> Username: jeking3  
> Created_at: 2017-09-08 22:04:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#discussion_r137899225  

Below: I found that test_inclusion needs singleton_module::get_lock which is in the library, so I removed the no-lib link designation from the test and it resolved that issue.


---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-21 22:17:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#issuecomment-331297251  

@robertramey any chance we could get this merged in?  I cannot build uuid on windows without these changes.

---

## Comment 5

> Username: viboes  
> Created_at: 2017-09-22 01:00:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#issuecomment-331321722  

BTW Robert, the CI files should be broken as nothing is going on.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-09-27 20:27:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#issuecomment-332645132  

@robertramey any chance we could get this merged in? I cannot build uuid on windows without these changes.  I had to disable running the serialization unit test in uuid/develop because of this, and I would like to re-enable it.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-10-10 14:15:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/60#issuecomment-335486850  

It looks like these issues were resolved; I re-enabled the uuid serialization unit test in Boost.Uuid without these changes merged.

---
