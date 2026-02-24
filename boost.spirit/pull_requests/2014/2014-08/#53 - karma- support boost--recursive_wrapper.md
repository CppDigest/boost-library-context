# #53 karma: support boost::recursive_wrapper [Closed]

> Username: purpleKarrot  
> Created at: 2014-08-28 10:45:44 UTC  
> Updated at: 2018-10-26 11:46:19 UTC  
> Closed at: 2018-10-26 10:55:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/53  



---

## Comment 1

> Username: djowel  
> Created_at: 2014-08-28 11:07:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/53#issuecomment-53705825  

Could you provide a test for this? You can probably just add it to one of the available tests, where relevant. Thanks!

---

## Comment 2

> Username: hkaiser  
> Created_at: 2014-08-28 12:21:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/53#issuecomment-53712017  

Looks good to me, a test case would be helpful, though.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-26 00:39:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/53#issuecomment-433249778  

I tried my best to find a case where this applies but I did not. Karma should never get a `boost::recursive_wrapper` value because it lives only inside `boost::variant`. Moreover Karma extracts values from a variant variable with `boost::get` like this:  
  
```cpp  
#include <boost/variant.hpp>  
#include <vector>  
  
struct foo; // Forward declaration  
  
typedef boost::variant<int, boost::recursive_wrapper<foo> > bar;  
  
struct foo  
{  
    int i;  
    std::vector<bar> v;  
};   
  
int main()  
{  
    bar v = { foo{ 0, {} } };  
    boost::get<foo>(v);  
}  
```  
  
The only case when `boost::recursive_wrapper` can be exposed to Karma if you use it outside of `boost::variant` for some unknown reason, but in that case you should deal with this by you own.

---

## Comment 4

> Username: purpleKarrot  
> Created_at: 2018-10-26 10:30:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/53#issuecomment-433364346  

I don't remember exactly, but I think I wanted to print the following type:  
  
```  
using int_tree_t = boost::make_recursive_variant<  
      int  
    , std::vector< boost::recursive_variant_ >  
    >::type;  
```  
Writing a parser works, because Qi supports recursive_wrapper.  
Writing a generator with Karma does nor work out of the box.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-10-26 10:55:08 UTC  
> Updated_at: 2018-10-26 11:46:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/53#issuecomment-433369923  

I am sorry, but it looks like you are wrong:  
  
  1. The `boost::recursive_variant_` is a different thing.  
  2. Karma handles both of them out of the box. https://wandbox.org/permlink/dF8gsRRiyjg7n410 https://wandbox.org/permlink/7K52BuIipKFKFNMy  
  3. Reopen if you have an MWE.  
  
I am closing again this because it is a 4 year PR, there is no proof that it is needed and also this change tangles Spirit more with `boost::variant` dependencies while it should move out of it (because of `std::variant`).

---
