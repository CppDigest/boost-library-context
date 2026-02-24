# #143 more robust setting of optional version [Closed]

> Username: dkoes  
> Created at: 2019-01-04 20:29:49 UTC  
> Updated at: 2020-07-18 17:08:58 UTC  
> Closed at: 2020-07-18 17:08:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/143  

I think it would be better to use the macro provided in version.hpp since otherwise the version<boost::optional<T>> struct is missing fields (e.g. tag).

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-01-04 20:53:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/143#issuecomment-451565207  

what is the motivation for this?

---

## Comment 2

> Username: dkoes  
> Created_at: 2019-01-04 21:01:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/143#issuecomment-451567140  

Really obtuse compiler errors when trying to serialize without versioning information.  
  
```  
#include <boost/optional.hpp>  
#include <boost/serialization/utility.hpp>  
#include <boost/serialization/optional.hpp>  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/archive/text_oarchive.hpp>  
  
#include <iostream>  
using namespace boost;  
namespace boost {  
namespace serialization {  
  
//default all our classes to not have version info  
template<class T>  
struct implementation_level_impl<const T> {  
    template<class U>  
    struct traits_class_level {  
        typedef BOOST_DEDUCED_TYPENAME U::level type;  
      };  
  
      typedef mpl::integral_c_tag tag;  
  
      typedef  
      BOOST_DEDUCED_TYPENAME mpl::eval_if<  
      is_base_and_derived<boost::serialization::basic_traits, T>,  
      traits_class_level< T >,  
      //else  
      BOOST_DEDUCED_TYPENAME mpl::eval_if<  
      is_fundamental< T >,  
      mpl::int_<primitive_type>,  
      //else  
      BOOST_DEDUCED_TYPENAME mpl::eval_if<  
      mpl::or_<is_class< T >, is_array< T> >,  
      mpl::int_<object_serializable>,  
      //else  
      BOOST_DEDUCED_TYPENAME mpl::eval_if<  
      is_enum< T >,  
      mpl::int_<primitive_type>,  
      //else  
      mpl::int_<not_serializable>  
      >  
      >  
      >  
      >::type type;  
      BOOST_STATIC_CONSTANT(int, value = type::value);  
    };  
  
  
}  
}  
  
//BOOST_CLASS_VERSION(boost::optional< short unsigned int  >, 0)  
  
int main() {  
   boost::archive::detail::check_object_versioning< boost::optional<short unsigned int> >();  
  
}  
```

---

## Comment 3

> Username: robertramey  
> Created_at: 2020-07-18 17:08:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/143#issuecomment-660512251  

first of all, this change generates failed tests on all configurations and all platforms.  
  
second, something like BOOST_CLASS_VERSION(boost::optional< T >, 1) doesn't work as the < and > characters really mess up the intent.  
  
The original code using struct ... works around the problem above.    
  
So this change is ill conceived on multiple levels.  I'm going to close it without merging it in.  
  
I hope I'm discourage any future efforts at improving/fixing the library.  I depend on users such as yourself to help me out.  But not everyone gets it right all the time.  
  
Thanks again.  
  
Robert Ramey

---
