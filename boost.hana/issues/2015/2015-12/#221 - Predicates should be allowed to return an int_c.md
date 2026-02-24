# #221 - [find_if] Predicates should be allowed to return an int_c. [Closed]

> Username: ricejasonf  
> Created at: 2015-12-11 20:50:18 UTC  
> Updated at: 2015-12-17 15:16:38 UTC  
> Closed at: 2015-12-17 15:16:38 UTC  
> Url: https://github.com/boostorg/hana/issues/221  

I wanted to use hana::length as a predicate for `find_if` and `filter`, but it does not compile. Consider the following example.  
  
```  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
int main()  
{  
  static_assert(hana::find_if(hana::make_tuple(  
      hana::make_tuple(),  
      hana::make_tuple(),  
      hana::make_tuple(hana::int_c<0>, hana::int_c<1>),  
      hana::make_tuple()), hana::length)  
        ==  
      hana::just(hana::make_tuple(hana::int_c<0>, hana::int_c<1>)), "");  
}  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-12-11 21:10:43 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164048400  

Thanks for the report!

---

## Comment 2

> Username: ricejasonf  
> Created at: 2015-12-11 21:35:12 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164053047  

Still broken for `filter`. Should that be a new issue?  
  
```  
/usr/local/include/boost/hana/basic_tuple.hpp:184:20: error: no matching function for call to object of  
      type 'boost::hana::detail::make_filter_indices<boost::hana::length_t>'  
            return static_cast<F&&>(f)(  
```

---

## Comment 3

> Username: ricejasonf  
> Created at: 2015-12-11 21:35:51 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164053142  

```  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
int main()  
{  
  static_assert(hana::filter(hana::make_tuple(  
      hana::make_tuple(),  
      hana::make_tuple(),  
      hana::make_tuple(hana::int_c<0>, hana::int_c<1>),  
      hana::make_tuple()), hana::length)  
        ==  
      hana::make_tuple(hana::make_tuple(hana::int_c<0>, hana::int_c<1>)), "");  
}  
```

---

## Comment 4

> Username: ricejasonf  
> Created at: 2015-12-11 21:51:30 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164057860  

Should we include these?  
  
```  
hana$ grep -r '@param predicate'  
include/boost/hana/fwd/group.hpp:    //! @param predicate  
include/boost/hana/fwd/any_of.hpp:    //! @param predicate  
include/boost/hana/fwd/span.hpp:    //! @param predicate  
include/boost/hana/fwd/take_while.hpp:    //! @param predicate  
include/boost/hana/fwd/remove_if.hpp:    //! @param predicate  
include/boost/hana/fwd/maximum.hpp:    //! @param predicate  
include/boost/hana/fwd/none_of.hpp:    //! @param predicate  
include/boost/hana/fwd/find_if.hpp:    //! @param predicate  
include/boost/hana/fwd/count_if.hpp:    //! @param predicate  
include/boost/hana/fwd/unique.hpp:    //! @param predicate  
include/boost/hana/fwd/drop_while.hpp:    //! @param predicate  
include/boost/hana/fwd/minimum.hpp:    //! @param predicate  
include/boost/hana/fwd/replace_if.hpp:    //! @param predicate  
include/boost/hana/fwd/partition.hpp:    //! @param predicate  
include/boost/hana/fwd/all_of.hpp:    //! @param predicate  
include/boost/hana/fwd/sort.hpp:    //! @param predicate  
```

---

## Comment 5

> Username: ldionne  
> Created at: 2015-12-11 21:54:56 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164059702  

Crap. Yes, I guess it should be supported everywhere, so we need to test it for those algorithms too (and fix it when it doesn't work).

---

## Comment 6

> Username: ldionne  
> Created at: 2015-12-12 23:50:06 UTC  
> Url: https://github.com/boostorg/hana/issues/221#issuecomment-164202797  

Was closed automatically when merging to master; reopening because this is not fixed.
