# #18 Bugfix/ticket 10382 default move stored edge [Closed]

> Username: mikael-s-persson  
> Created at: 2014-09-21 05:00:09 UTC  
> Updated at: 2014-11-16 22:34:40 UTC  
> Closed at: 2014-11-16 22:34:40 UTC  
> Url: https://github.com/boostorg/graph/pull/18  

Fix for bug #10382 regarding a compilation error on GCC 4.6.x when trying to copy / move an adj-list graph.   
Fixes default move on stored_edge class.  
  
See bug-tracker: https://svn.boost.org/trac/boost/ticket/10382  
  
This very simple bugfix has been lingering for a whole month now.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-11-02 22:12:43 UTC  
> Url: https://github.com/boostorg/graph/pull/18#issuecomment-61426286  

I'm okay with this PR, but would you please change how you test the BOOST_GCC version?  
  
I'd prefer something like this:  
  
BOOST_GCC < 40600  
  
instead of   
  
(BOOST_GCC / 100) < 406)

---

## Comment 2

> Username: mikael-s-persson  
> Created_at: 2014-11-06 03:12:55 UTC  
> Url: https://github.com/boostorg/graph/pull/18#issuecomment-61921330  

I used `(BOOST_GCC / 100) / 406` because that was the condition used a few lines down. I wanted to keep things consistent. Either both need to change or both remain the same.  
  
Please refer to the bug-tracker (linked to above), because there was a related issue reported about GCC 4.9. I suggested that the entire thing (the copy and move code for stored_edge and stored_edge_property) should be simplified to use explicit (non-defaulted) versions for all the constructors / operators and that the idea of trying to remain "trivial" (with default operators) was not worth the maintenance cost. Also, I don't see the point in trying to remain "trivial" when the edges use dynamic allocation to store the properties, the relative benefit is negligible, if such a benefit exists at all.  
  
I don't understand why such a high-priority and critical bug (making the adjacency_list unusable on many popular versions of GCC) is not gathering any serious attention from the maintainers. I can't help but feel that the BGL is just gathering dust, with no active maintenance.

---

## Comment 3

> Username: mikael-s-persson  
> Created_at: 2014-11-06 06:39:42 UTC  
> Url: https://github.com/boostorg/graph/pull/18#issuecomment-61933559  

Please see an alternative pull-request that implements to "simplified" solution that I suggested earlier (and on the bug tracker). I recommend that solution instead (albeit being a more substantial change that needs more testing).

---

## Comment 4

> Username: Belcourt  
> Created_at: 2014-11-16 22:34:40 UTC  
> Url: https://github.com/boostorg/graph/pull/18#issuecomment-63242991  

I just merged your newer patch so I think this is now superfluous.

---
