# #16 Fix to Tickets #7975 and #8749 - Branched off from develop [Merged]

> Username: brunocodutra  
> Created at: 2015-02-23 00:27:25 UTC  
> Updated at: 2015-04-28 22:49:36 UTC  
> Merged at: 2015-04-28 20:28:32 UTC  
> Closed at: 2015-04-28 20:28:32 UTC  
> Url: https://github.com/boostorg/mpl/pull/16  

The fix is based on the report of Ticket 8749 itself:  
  
> I have traced the problem to the file boost/mpl/map/aux_/item.hpp in the class definition of m_item. m_item derives from Base (which is a map<...> type) but does not define a "typedef m_item type;" to act as a unary metafunction returning itself. The result is that Base::type is found instead which means that m_item acts as a unary metafunction returning a map with all the inserted elements removed. The class m_item_ at the bottom of the file properly defines a "typedef m_item_ type;" which strengthens my belief that it missing from m_item is a bug.  
  
Originally Tickets 7975 and 8749 referred only to insertion of keys into maps, but the very same bug can be reproduced for sets as well. Moreover, the removal of keys from both also suffers of the same bug. This changes fix all these issues.

---

## Comment 1

> Username: brunocodutra  
> Created_at: 2015-03-22 17:59:30 UTC  
> Url: https://github.com/boostorg/mpl/pull/16#issuecomment-84667285  

All mpl tests were verified to pass for clang 3.6 and gcc 4.9.2 on linux and for msvc 2010 on windows xp

---
