# #14 - Add operators [Closed]

> Username: pdimov  
> Created at: 2021-05-07 01:29:51 UTC  
> Updated at: 2021-10-27 13:49:19 UTC  
> Closed at: 2021-10-26 15:37:53 UTC  
> Url: https://github.com/boostorg/describe/issues/14  

Add a namespace `boost::describe::operators` containing `==`, `!=`, `<`, `<=`, `>`, `>=`. Typical use would be  
```  
namespace user  
{  
  
struct X  
{  
};  
  
BOOST_DESCRIBE_STRUCT(X, (), ())  
  
using boost::describe::operators::operator==;  
using boost::describe::operators::operator!=;  
  
} // namespace user  
```  
Maybe add `boost::describe::eq`, `boost::describe::lt`, `boost::describe::le` as helper functions.  
  
Could the operators be directly in `boost::describe`? (Probably not.)

---

## Comment 1

> Username: pdimov  
> Created at: 2021-10-26 15:37:53 UTC  
> Url: https://github.com/boostorg/describe/issues/14#issuecomment-952063826  

Added.

---

## Comment 2

> Username: ddevienne  
> Created at: 2021-10-27 06:44:59 UTC  
> Url: https://github.com/boostorg/describe/issues/14#issuecomment-952590862  

Just curious Peter, but why aren't the commits associated to GH Issues?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-10-27 13:49:19 UTC  
> Url: https://github.com/boostorg/describe/issues/14#issuecomment-952950502  

No particular reason; there's no guarantee that things will be implemented in a single commit, although I could have used "refs".
