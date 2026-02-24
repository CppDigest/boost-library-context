# #15 Fix to Tickets #7975 and #8749 [Closed]

> Username: brunocodutra  
> Created at: 2015-02-22 20:44:54 UTC  
> Updated at: 2015-02-22 23:52:04 UTC  
> Closed at: 2015-02-22 23:46:42 UTC  
> Url: https://github.com/boostorg/mpl/pull/15  

Quoting from the original bug report 8749:  
  
> I have traced the problem to the file boost/mpl/map/aux_/item.hpp in the class definition of m_item. m_item derives from Base (which is a map<...> type) but does not define a "typedef m_item type;" to act as a unary metafunction returning itself. The result is that Base::type is found instead which means that m_item acts as a unary metafunction returning a map with all the inserted elements removed. The class m_item_ at the bottom of the file properly defines a "typedef m_item_ type;" which strengthens my belief that it missing from m_item is a bug.  
  
The very same bug also manifested for sets and a very similar bug also occurred in some scenarios where a key was removed from both maps and sets. Tests cases have been added for all observed issues.

---

## Comment 1

> Username: brunocodutra  
> Created_at: 2015-02-22 20:47:31 UTC  
> Url: https://github.com/boostorg/mpl/pull/15#issuecomment-75456867  

I realized upon creating the pull request that I had branched off master instead of develop, so I had to merge in the develop branch before issuing the request, hence the long list of commits.   
I hope that's not an issue for the review process.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-02-22 23:38:46 UTC  
> Url: https://github.com/boostorg/mpl/pull/15#issuecomment-75472066  

Your own initial pull request should be off of the 'develop' branch and then you make your changes from your pull request. What you have done, which appears to be mixing in master changes into 'develop', is not acceptable AFAICS. Pull requests to Boost libraries should always be made off the latest 'develop' branch.

---

## Comment 3

> Username: brunocodutra  
> Created_at: 2015-02-22 23:46:42 UTC  
> Url: https://github.com/boostorg/mpl/pull/15#issuecomment-75472546  

Yes I understand your point, I should have branched off develop from the beginning, I just realized I haven't once I had already pushed changes into github and I admit I was just too lazy to get it right. I just thought merging develop at the end would be equivalent to branching off develop from the beginning, but I guess it's not.  
  
Thank you for your feedback, I'll just close this request and open a new one done the right way.

---
