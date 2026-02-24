# #203 - boost::variant not correctly serialized when reusing the memory location [Open]

> Username: poggenhans  
> Created at: 2020-06-03 22:05:02 UTC  
> Updated at: 2020-07-27 09:06:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/203  

When migrating from boost 1.65 to 1.71, I noticed an issue when serializing variants that share the same memory location. It is also present in the current `master` version.  
  
I boiled it down to the following example:  
  
```c++  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/serialization/serialization.hpp>  
#include <boost/serialization/variant.hpp>  
  
int main() {  
  boost::variant<int> variant = 1;  
  
  // write it two times  
  std::stringstream ss;  
  boost::archive::xml_oarchive oa(ss);  
  oa << make_nvp("var1", variant);  
  oa << make_nvp("var2", variant);  
  
  // read it two times  
  boost::variant<int> v1;  
  boost::variant<int> v2;  
  boost::archive::xml_iarchive ia(ss);  
  ia >> boost::serialization::make_nvp("var1", v1);  
  ia >> boost::serialization::make_nvp("var2", v2);  
  assert(boost::get<int>(v1) == 1);  
  assert(boost::get<int>(v2) == 1);  // Fails: v2 is actually zero  
}  
```  
  
I would have expected that `v2` has the same state as `v1` and `variant`, but it is actually zero or maybe a garbage value. The generated xml shows that the entry for `var2` is only a reference to `var1`, and not a normal value as in older versions.  
  
The reason seems to be that the `tracking_level` for variant is now set to `track_always` (see [here](https://github.com/boostorg/serialization/blob/master/include/boost/serialization/variant.hpp#L171)), so that it essentially behaves like a pointer type. Is this new behavior intended?  
  
I ran into this problem when serializing two different `variant`s where the second object just happened to get the same address on the stack that the first object had. Or sometimes a different address, depending on how I compiled the program.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-07-16 00:00:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/203#issuecomment-659076020  

I invested some time with your example above.  A couple of issues:  
  
Tracking level for a primitive type is always set to no-tracking.  For an explanation, see the comment from tracking.hpp:  
  
// The STATIC_ASSERT is prevents one from setting tracking for a primitive type.  
// This almost HAS to be an error.  Doing this will effect serialization of all  
// char's in your program which is almost certainly what you don't want to do.  
// If you want to track all instances of a given primitive type, You'll have to  
// wrap it in your own type so its not a primitive anymore.  Then it will compile  
// without problem.  
  
So the default tracking level for a primitive (like int) is always track-never.   
  
But I looked at the serialized output for your program and damn if doesn't specify object reference.  So there's clearly a bug somewhere.   
  
a) object reference shouldn't used if it's track never  
b) if it IS tracked - the correct value should be recovered.  
  
I'm still looking at this.

---

## Comment 2

> Username: poggenhans  
> Created at: 2020-07-27 09:06:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/203#issuecomment-664218458  

I think the problem is not the tracking level for the `int` inside the variant, it is rather the `boost::variant` itself where the tracking level is set to `track_always`.  
  
That the value is not correctly recovered could be a separate bug. I couldn't find the place where `track_always` types are recovered, but there must be some logic that handles extracting the reference for tracked types, shouldn't it?
