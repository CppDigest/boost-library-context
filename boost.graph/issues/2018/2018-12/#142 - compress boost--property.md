# #142 - compress boost::property [Closed]

> Username: mglisse  
> Created at: 2018-12-16 18:16:01 UTC  
> Updated at: 2023-09-13 01:12:00 UTC  
> Closed at: 2023-09-13 01:12:00 UTC  
> Url: https://github.com/boostorg/graph/issues/142  

(adapted from https://svn.boost.org/trac10/ticket/9826)  
  
Looking at property_map, I noticed that boost::property is wasting space. property<tag,double> will typically have size 16 because of the no_property member. list_edge<Vertex,no_property> will also be too long for the same reason. A proper use of the empty base optimization (possibly through tuple or compressed_pair) should help with this. Partial specializations for no_property may be a simpler alternative. If you are lazy, [[no_unique_address]] would already be nice.  
  
Graphs can grow large, and it seems quite important to avoid wasting memory.  
  
This appears to be a regression caused by:  
  
> commit 616b9e71348b430af89a2405b5b8463557e63dec  
> Author: Jeremiah Willcock <jewillco@osl.iu.edu>  
> Date:   Sat Jun 30 20:00:41 2012 +0000  
  
Before that commit, property was implemented with derivation (which is still what the documentation shows...), and the empty base optimization avoided wasting so much space.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-10-14 05:52:36 UTC  
> Url: https://github.com/boostorg/graph/issues/142#issuecomment-1278517695  

If you open a pull request to fix it, I can help you get it merged in.  
  
I would start with adding some tests to measure the size of a property_map, etc, make the functional change and then adjust the test to use the new lower size, thereby ensuring that any future change that accidentally makes the size larger will at least trigger a test failure.
