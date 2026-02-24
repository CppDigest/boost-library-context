# #325 - Incorrect unregistration (with exception thrown / crash) with multiple DLL's using Boost.Serialization [Open]

> Username: gast128  
> Created at: 2025-01-31 10:47:58 UTC  
> Updated at: 2025-02-03 15:16:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/325  

We have the situation here that an application throws an unregistered_class exception in 'polymorphic::save' in 'oserializer.hpp' in the following code snippet:  
  
```  
const boost::serialization::extended_type_info * true_type =  
    i.get_derived_extended_type_info(t);  
  
// note:if this exception is thrown, be sure that derived pointer  
// is either registered or exported.  
if(NULL == true_type){  
    boost::serialization::throw_exception(  
        archive_exception(  
            archive_exception::unregistered_class,  
            "derived class not registered or exported"  
        )  
    );  
}  
```  
The reason is that a type registered isn't present anymore.   
  
We use Boost.Serialization as shared DLL with the following components:  
- COM component1 in DLL1 registering type e.g. Tracklet  
- COM component2 in DLL2 registering the same type (i.e. Tracklet)  
  
If both COM components are loaded they both register their Tracklet type in singleton tkmap (which is a multiset). If COM component 1 gets unloaded Windows might unload DLL1 eventually. When a DLL gets unloaded it invokes shutdown code extended_type_info_typeid_0::type_unregister() which removes **all** instances of Tracklet from the multiset. If COM component2 now wants to serialize Tracklet it ends in an exception thrown in above code since the type is not registered anymore. The serialization will fail (and in our case even end program due to not catching exceptions there).  
  
A solution might not be that difficult by only removing the type which is registered by the same object instance of extended_type_info_typeid_0. multiset has stable iterators so it could even hold the returned iterator and remove by that same iterator.   
  
Alternatively only removing this pointer could be like this:  
  
```  
BOOST_SERIALIZATION_DECL void extended_type_info_typeid_0::type_unregister()  
{  
    if (nullptr != m_ti) {  
        // note: previously this conditional was a runtime assertion with  
        // BOOST_ASSERT.  We've changed it because we've discovered that at  
        // least one platform is not guaranteed to destroy singletons in  
        // reverse order of destruction.  
        // BOOST_ASSERT(! singleton<tkmap>::is_destroyed());  
        if(! singleton<tkmap>::is_destroyed()) {  
            tkmap & x = singleton<tkmap>::get_mutable_instance();  
  
            const auto pr = x.equal_range(this);  
  
            for (auto it = pr.first; it != pr.second; ++it)  
            {  
                if (*it == this)  
                {  
                    x.erase(it);  
                    break;  
                }  
            }  
        }  
    }  
    m_ti = nullptr;  
}  
```  
Using Boost 1.86; VS2022  
  
Edit: some things and title.
