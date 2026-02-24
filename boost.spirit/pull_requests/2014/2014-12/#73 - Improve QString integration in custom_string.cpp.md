# #73 Improve QString integration in custom_string.cpp [Closed]

> Username: mloskot  
> Created at: 2014-12-15 10:01:33 UTC  
> Updated at: 2014-12-15 12:15:05 UTC  
> Closed at: 2014-12-15 10:51:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/73  

There is an example of how to use Qi with Qt's QString as a custom string.  
This is very helpful but it doesn't work in conjunction with the debug facilities.  
The following patch fixes this by adding a couple of template specialisations.  
  
Patch originally submitted as https://svn.boost.org/trac/boost/ticket/8846

---

## Comment 1

> Username: djowel  
> Created_at: 2014-12-15 10:46:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/73#issuecomment-66977043  

Please do it on develop branch. Thanks!

---

## Comment 2

> Username: mloskot  
> Created_at: 2014-12-15 10:51:27 UTC  
> Updated_at: 2014-12-15 10:54:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/73#issuecomment-66977565  

My bad, sorry. Closing.  
  
New PR #75

---

## Comment 3

> Username: timangus  
> Created_at: 2014-12-15 11:13:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/73#issuecomment-66980055  

This is the same thing as https://github.com/boostorg/spirit/pull/72, which @djowel already committed. I probably should have mentioned on the tracker sorry, but as is mentioned, the bug was submitted on my behalf.

---

## Comment 4

> Username: mloskot  
> Created_at: 2014-12-15 12:15:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/73#issuecomment-66986056  

@timangus I might have carried away with my rant...I wish Boost decided on using one bug/patch tracker

---
