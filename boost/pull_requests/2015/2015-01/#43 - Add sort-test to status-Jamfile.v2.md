# #43 Add sort/test to status/Jamfile.v2 [Closed]

> Username: spreadsort  
> Created at: 2015-01-19 20:47:01 UTC  
> Updated at: 2015-01-21 03:00:38 UTC  
> Closed at: 2015-01-20 12:05:40 UTC  
> Url: https://github.com/boostorg/boost/pull/43  

This will enabling testing of the new sort library.  
  
The sort library is here: https://github.com/boostorg/sort.  Do I also need to add it directly to this boost project in some fashion?

---

## Comment 1

> Username: spreadsort  
> Created_at: 2015-01-19 20:48:45 UTC  
> Url: https://github.com/boostorg/boost/pull/43#issuecomment-70557989  

Is there something I should do to test that this all works properly, aside from making sure that boostorg/sort's test directory runs without trouble?

---

## Comment 2

> Username: Beman  
> Created_at: 2015-01-20 12:05:40 UTC  
> Url: https://github.com/boostorg/boost/pull/43#issuecomment-70643996  

OK, done. Also added sort to .gitmodules.  
  
I change libs/sort/test/Jamfile.v2 to conform to the usual test-suite naming convention. So please do a pull on your local working copy.  
  
The regression tests look good so far, although only a few test runners have cycled. See http://www.boost.org/development/tests/develop/developer/summary.html  
  
Thanks,  
  
--Beman

---

## Comment 3

> Username: spreadsort  
> Created_at: 2015-01-21 03:00:37 UTC  
> Url: https://github.com/boostorg/boost/pull/43#issuecomment-70776899  

Thanks!  
  
On Tue Jan 20 2015 at 7:05:48 AM Beman Dawes notifications@github.com  
wrote:  
  
> Closed #43 https://github.com/boostorg/boost/pull/43.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/boost/pull/43#event-221552701.

---
