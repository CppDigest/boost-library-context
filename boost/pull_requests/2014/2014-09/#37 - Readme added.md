# #37 Readme added [Closed]

> Username: ENikS  
> Created at: 2014-09-04 03:52:18 UTC  
> Updated at: 2015-03-18 18:16:24 UTC  
> Closed at: 2015-03-18 18:02:45 UTC  
> Url: https://github.com/boostorg/boost/pull/37  

For someone who worked with boost for a while this may seam trivial but for new kids in town this is essential. It took me couple of days of hair pooling to accidentally stumble upon that wiki page.  
Long story short: if this info was available on the repository it would've made my life so much easier.

---

## Comment 1

> Username: Beman  
> Created_at: 2014-09-05 15:36:23 UTC  
> Url: https://github.com/boostorg/boost/pull/37#issuecomment-54641271  

While I think adding a readme would be a good idea, duplicating content from the wiki creates a maintenance problem. Also, the wiki information is aimed at developers who want to work directly with github, while end-users who just want to install a boost release should be pointed to the "Getting Started..." docs on the web site.  http://www.boost.org/more/getting_started/index.html  
  
So the readme should just point readers to the wiki and the web site getting started, with an indication as to the distinction between the two sets of docs.  
  
Please update your pull request accordingly.  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-09-05 15:54:41 UTC  
> Url: https://github.com/boostorg/boost/pull/37#issuecomment-54643829  

We could delete the README file in the final release, so that it only appears in github.

---

## Comment 3

> Username: ENikS  
> Created_at: 2014-09-05 16:03:32 UTC  
> Updated_at: 2014-09-05 21:16:11 UTC  
> Url: https://github.com/boostorg/boost/pull/37#issuecomment-54644961  

You are right. I've removed non essential information from the readme so only necessary steps are there.  
  
Once you clone repo localy and run build such as this:  
./b2 -a  
  
One would assume that all libraries are built and ALL header files are copied or linked into proper place but it is not the case. Only these files which .libs are depend upon are installed into include directory. If library is not used in the build process it is left out. Just look for "Signals2" for example.  
So, if you want complete set of includes "./b2 headers" need to be executed.  
  
This information is only available on that little page buried somewhere on 5th page of google search. Took me 2 days and learning jam to figure it out. Only after I entered correct searching terms the page was found.   
So, unless build is fixed that information should be available.

---

## Comment 4

> Username: ENikS  
> Created_at: 2015-03-18 18:02:45 UTC  
> Url: https://github.com/boostorg/boost/pull/37#issuecomment-83100974  

Thinking about it, this should not be required at all. Complete rebuild should check if includes are populated and rebuild all headers automatically. I am moving it to issues.

---
