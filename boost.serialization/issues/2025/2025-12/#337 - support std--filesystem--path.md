# #337 - [Feature request] support std::filesystem::path [Open]

> Username: gast128  
> Created at: 2025-12-23 11:29:58 UTC  
> Updated at: 2025-12-23 16:47:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/337  

Hello all,  
  
is it possible to have support for std::filesystem::path serialization? There were old discussions about boost::filesystem::path:  
- http://lists.boost.org/Archives/boost/2008/09/142550.php  
- https://svn.boost.org/trac/boost/ticket/5192  
  
Implementation inspired by John Rade's answer would be something like this:  
  
```  
namespace boost  
{  
    namespace serialization  
    {  
        template <class Archive>  
        void serialize(Archive& ar, std::filesystem::path& rpth, const unsigned int)  
        {  
            std::string str;  
  
           BOOST_IF_CONSTEXPR (Archive::is_saving::value)  
            {  
               str = rpth;  
            }  
  
            ar & boost::serialization::make_nvp("path", str);  
  
            BOOST_IF_CONSTEXPR (Archive::is_loading::value)  
            {  
               rpth = std::move(str);  
            }  
        }  
    }  
}  
  
```  
  
Probably someone who knows the library would come up with a better implementation.

---

## Comment 1

> Username: robertramey  
> Created at: 2025-12-23 16:15:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/337#issuecomment-3687192613  

Generally, Boost libraries contain their own serialization implementation and the serialization library only contains serialization code for std types.  This means that the serialization is implemented by the person who knows more about the library whose classes are being serialized.  Also, having the maintainer of the serialization library do this obviously does not scale.

---

## Comment 2

> Username: gast128  
> Created at: 2025-12-23 16:47:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/337#issuecomment-3687298854  

> Generally, Boost libraries contain their own serialization implementation and the serialization library only contains serialization code for std types. This means that the serialization is implemented by the person who knows more about the library whose classes are being serialized. Also, having the maintainer of the serialization library do this obviously does not scale.  
  
Seems like a deadlock then if the library maintainer does not know the serialization library and the serialization library maintainer does not know the Boost library. I personally would use the dependency metric where to place it.  
  
This request is not about a Boost library but about std::filesystem::path. No internals needed since only the public API can be used. There is already support for std (e.g. variant; vector; pair) which helps a lot.
