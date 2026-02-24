# #770 - Problems performing boolean operations on polygons with shared edges [Closed]

> Username: lukegluke  
> Created at: 2020-11-03 07:42:34 UTC  
> Updated at: 2021-07-28 12:27:16 UTC  
> Closed at: 2021-07-28 12:27:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/770  

Hi,  
  
It's been quite a while, are the any news about fixing problems with boolean operations on polygons with shared edges?  
https://svn.boost.org/trac10/ticket/12118  
https://svn.boost.org/trac10/ticket/12125  
  
Maybe it has been fixed already?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-12-30 13:41:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/770#issuecomment-752617389  

Thanks for your message!  
  
Ticket 12118 is fixed in the meantime, and I just closed it.  
Ticket 12125 is fixed by removing rescaling, which is (still) not the default but it should be in 2021.  
  
Therefore I assigned this issue to myself and will close it as soon as we change the default, it took much longer than expected but we're nearly there now.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-07-28 12:27:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/770#issuecomment-888267720  

As above, fixed by removing rescaling, testcase is added, it will be released in (hopefully) 1.78  
In the meantime you could define `BOOST_GEOMETRY_NO_ROBUSTNESS`   
Therefore closing this issue.
