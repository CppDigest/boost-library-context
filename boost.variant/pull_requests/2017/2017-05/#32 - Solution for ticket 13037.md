# #32 Solution for ticket 13037 [Closed]

> Username: adambadura  
> Created at: 2017-05-19 07:04:19 UTC  
> Updated at: 2017-05-19 18:58:12 UTC  
> Closed at: 2017-05-19 18:58:12 UTC  
> Url: https://github.com/boostorg/variant/pull/32  

https://svn.boost.org/trac/boost/ticket/13037  
  
Missing include of `<boost/type_traits/is_const.hpp>` added in `<boost/variant/polymorphic_get.hpp>`.  
  
_Note however that it seems `polymorphic_get.hpp` header has more such issues: use of elements that are not directly included. Among others there is `boost::remove_cv` - a problem mentioned in [ticket 11283](https://svn.boost.org/trac/boost/ticket/11283). Should they be solved as well? At least as for now they are not causing compilation errors._

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-05-19 18:58:12 UTC  
> Url: https://github.com/boostorg/variant/pull/32#issuecomment-302784342  

Great thanks for the fix, I've merged your pull request in https://github.com/boostorg/variant/commit/575c5796b484927827a912f03700cfa465cc7df5! Any other fixes are welcomed.  
  
P.S.: next time make pull requests to the 'develop' branch. It's a tradition in Boost: all the new stuff must cycle through the regression testings in develop branch before it is merged to master branch.

---
