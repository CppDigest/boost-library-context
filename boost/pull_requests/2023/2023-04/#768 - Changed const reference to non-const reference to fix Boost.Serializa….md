# #768 Changed const reference to non-const reference to fix Boost.Serializa… [Closed]

> Username: nilay2207  
> Created at: 2023-04-23 08:35:08 UTC  
> Updated at: 2024-08-08 15:40:59 UTC  
> Closed at: 2024-08-08 15:40:59 UTC  
> Url: https://github.com/boostorg/boost/pull/768  

// Added BOOST_CLASS_EXPORT macro to register polymorphic class with Boost.Serialization  
class BOOST_SYMBOL_VISIBLE MyPolymorphicClass : public MyBaseClass  
{  
    // Class definition here  
};  
BOOST_CLASS_EXPORT(MyPolymorphicClass)

---

## Comment 1

> Username: mclow  
> Created_at: 2023-04-23 13:38:45 UTC  
> Url: https://github.com/boostorg/boost/pull/768#issuecomment-1519070270  

There are a couple of problems with this PR:  
* It's targeted at `master`.   We never commit directly to `master`; instead we commit to `develop` and then merge to `master` after the tests have passed.   
* this looks like it should be committed to Boost.Serialization, not the main boost project.  https://github.com/boostorg/serialization

---
