# #12 Fix Boost.Sync tests breakage. [Merged]

> Username: Lastique  
> Created at: 2014-05-31 13:15:43 UTC  
> Updated at: 2014-06-25 19:12:54 UTC  
> Merged at: 2014-05-31 14:12:47 UTC  
> Closed at: 2014-05-31 14:12:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/12  

Re-add date/time type tags removed by commit 6636f49f13154555b3b9c9618fca5f03cf47a5b3. These tags are used by Boost.Sync to categorize different date/time types.

---

## Comment 1

> Username: danieljames  
> Created_at: 2014-05-31 13:35:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/12#issuecomment-44748482  

Could really do with a brief comment explaining what the typedefs are.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-05-31 13:44:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/12#issuecomment-44748661  

The typedefs are used in boost/sync/support/boost_date_time.hpp to implement time_traits specializations for all Boost.DateTime-compatible time units. The typedefs are not for public use, hence they are not documented and start with an underscore. This approach for type categorization is described here: http://www.boost.org/doc/libs/1_55_0/doc/html/proto/appendices.html#boost_proto.appendices.implementation.sfinae  
  
The implementation without the typedefs is possible, but more difficult and has limitation. Boost.DateTime derives time units from the base classes, so Boost.Sync would have to specialize time_traits for all final types of time units. Moreover, this would not work for possible user-defined time units defined in such fashion. This is why the approach with type tags was taken.

---

## Comment 3

> Username: danieljames  
> Created_at: 2014-05-31 13:46:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/12#issuecomment-44748698  

Sorry, I meant a comment in the code.

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-05-31 13:51:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/12#issuecomment-44748812  

Done.

---
