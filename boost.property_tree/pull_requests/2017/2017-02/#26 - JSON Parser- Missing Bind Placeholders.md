# #26 JSON Parser: Missing Bind Placeholders [Closed]

> Username: ax3l  
> Created at: 2017-02-15 23:05:37 UTC  
> Updated at: 2020-06-26 16:50:08 UTC  
> Closed at: 2020-06-26 16:50:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26  

Fixes missing [placeholder includes](http://www.boost.org/doc/libs/1_63_0/libs/bind/doc/html/bind.html#bind.implementation.files) to `boost::bind`.  
```  
boost/property_tree/json_parser/detail/parser.hpp(217): error: identifier "_1" is undefined  
boost/property_tree/json_parser/detail/parser.hpp(520): error: identifier "_1" is undefined  
```  
  
Reported in  
  https://svn.boost.org/trac/boost/ticket/12841

---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2017-02-15 23:06:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/26#pullrequestreview-22135245  

📁 include/boost/property_tree/json_parser/detail/parser.hpp

```diff
   7 | #include <boost/bind.hpp>
   8 | #include <boost/format.hpp>
   9 |+ #include <boost/bind.hpp>
```

> Username: ax3l  
> Created_at: 2017-02-15 23:06:50 UTC  
> Updated_at: 2017-02-15 23:07:25 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#discussion_r101407060  

ah, that's doubled now!


---

## Review 2 [Commented]

> Username: ax3l  
> Created_at: 2017-02-15 23:16:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/26#pullrequestreview-22136776  

📁 include/boost/property_tree/json_parser/detail/parser.hpp

```diff
 217 |-                             boost::ref(callbacks), _1),
 218 |+                             boost::ref(callbacks),
 219 |+                             boost::placeholders::_1),
```

> Username: ax3l  
> Created_at: 2017-02-15 23:16:15 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#discussion_r101408642  

one might want this part as explicit as here, since some user code might have `BOOST_BIND_NO_PLACEHOLDERS` defined.  
  
same in the line below.

> Username: jowenshaw  
> Created_at: 2017-12-14 12:22:35 UTC  
> Updated_at: 2017-12-14 12:26:22 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#discussion_r156929373  

I use libboost1.63 and met the same problem today. After using the above patch, there is still an building error: ‘boost::placeholders’ has not been declared. I solved it by adding the following including file: #include <boost/bind/placeholders.hpp>

> Username: ax3l  
> Created_at: 2017-12-14 12:34:30 UTC  
> Updated_at: 2017-12-14 12:34:59 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#discussion_r156931579  

great to hear it helps you!  
  
note that this patch also adds the include you mention include :) (see the "Files changed" tab)


---

## Comment 3

> Username: Osyotr  
> Created_at: 2019-10-08 11:27:37 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-539469937  

Bump.

---

## Comment 4

> Username: ax3l  
> Created_at: 2019-10-08 13:34:33 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-539515984  

@grafikrobot you merged last to master, so I ping you on my two year old fix, fixing this [include-what-you-use](https://github.com/include-what-you-use/include-what-you-use) bug ;)

---

## Comment 5

> Username: ax3l  
> Created_at: 2019-10-08 13:38:34 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-539517872  

cc @CornedBee as Trac issue owner :)

---

## Comment 6

> Username: Osyotr  
> Created_at: 2020-04-02 14:02:24 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-607865726  

Ah sh*t, here we go again.  
Updating boost to 1.72, still bumping into this PR.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-04-02 14:05:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-607867156  

Unfortunately.. I don't have permissions to do anything about this PR.

---

## Comment 8

> Username: ax3l  
> Created_at: 2020-04-02 23:27:17 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-608144486  

@CornedBee can you merge this please?

---

## Comment 9

> Username: Osyotr  
> Created_at: 2020-06-25 10:23:40 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-649451987  

Looks like it's fixed in #50!

---

## Comment 10

> Username: ax3l  
> Created_at: 2020-06-26 16:50:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/26#issuecomment-650282822  

Yay! :)

---
