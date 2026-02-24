# #116 - Make name-value-pair-serialization concept based to avoid coupling [Closed]

> Username: correaa  
> Created at: 2018-09-03 05:28:49 UTC  
> Updated at: 2020-07-19 15:56:09 UTC  
> Closed at: 2019-10-26 00:26:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/116  

One of the best features of Boost Serialization is that it defines a serialization protocol that is independent of Boost.Serialization itself.  
This makes possible to implement serialization of classes non-invasively and without `#includ`ing any of the Boost Serialization headers.  
  
This has an exception however, serialization over named-tag archives (XML) requires the _explicit_ use of the type `boost::serialization::nvp` (or the macro), forcing the inclusion of `boost/serialization/nvp.hpp` and increasing the coupling with Boost.Serialization.  
  
One workaround would be that Boost.Serialization accepts (for XML serialization at least) any name-value pair _generic_ class, (as long as it has the members `::name() -> const char*` and  `::value()` (and `::const_value()`?) and not just the explicit `boost::serialization::nvp` type.  
  
(Another workaround would be to allow XML archives to invent a unique name of the tags if that it is not specified in some way by means of an name-value wrapper).

---

## Comment 1

> Username: correaa  
> Created at: 2019-10-25 22:48:14 UTC  
> Updated at: 2019-10-25 23:04:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-546537014  

This new "nvp" feature https://www.boost.org/doc/libs/master/libs/core/doc/html/core/nvp.html  
 in Boost.Core solves the dependency on Boost.Serialization.   
In exchange for a dependency in Boost.Core of course, but being Boost.Core a header-only library can help a bit.   
  
However it is not yet a concept-based solution.   
In the sense that the serialize code still depends on a concrete type (template).

---

## Comment 2

> Username: robertramey  
> Created at: 2019-10-26 00:26:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-546551029  

Interesting observation.  
  
It did cross my mind.  But the concept would be something like:  
  
requires a get function and get value function, etc.  
  
And enforcing such a concept requires some TMP jujitsu to ensure that these member functions actually are declared.  And of course this would be doable in MPL to be compatible with C++03.  Still it might be possible.  
  
But would it be worth it?  NVP (surprisingly) is not a commonly used type except for serialization.  And serialization doesn't really need to work with any nvp type but a particular nvp type is good enough for government work.  
  
Now if someone wanted to make an nvp type useful more generally outside of serialization that might be different.  But honestly, I don't see the payback on this kind of effort.  Not everything is worth making generic.  To summarize, perhaps doable, but is it worth doing?

---

## Comment 3

> Username: correaa  
> Created at: 2019-11-27 02:06:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-558895636  

I think there is an intermediate solution that can work out and it is backward compatible.   
  
Let me restate the problem in case the original problem was not clear.  
  
**Context**  
I have a library with an type that needs to be serializable.   
If I am going to serialize to non-tagged archives, this works  
  
```c++  
struct Awesome{  
   int val_;  
   template<class Archive>  
   serialize(Ar& ar, unsigned){  
       ar & val_;  
   }  
};  
```  
  
As you can see the library doesn't depend on Boost.Serialization (or Boost for that matter). It only *will* depend on Boost.Serialization (or a Boost.archive type) when the use of the library decides to use Boost.Serialization specifically. Note that there are might be other ways to use the Serialization *protocol* for other purposed.  
  
```c++  
#include<boost/archive/binary_oarchive.hpp>// also link to Boost -lboost_serialization  
int main(){  
    Awesome aws;  
   std::ofstream ofs{"serialization.bin"}; assert(ofs);  
   boost::archive::binary_oarchive{ofs} << d2D;  
}  
```  
  
***Problem***  
  
Here it is the first problem:  
If the user of the library needs to archive to XML (Boost XML serialization archive) he can use `/xml_oarchive.hpp` but the code above will not work.   
Not even in principle, because the `serialize` code would need a named-value pair for that.  
  
So, as a library writer, I need to write serialize in a different way (added `make_nvp`)  
  
```c++  
#include<boost/serialization/nvp.hpp>   
struct Awesome{  
   int val_;  
   template<class Archive>  
   serialize(Archive& ar, unsigned){  
       ar & boost::serialization::make_nvp("val", val_);  
   }  
};  
```  
  
Of course this adds suddenly a the *explicit* dependency on Boost.Serialization on the library, whether the user of the library is going to use serialization or not. Or whether the user uses XML or not.  
This is the problem in my opinion.   
  
If understand correctly, named-value pair types have been moved to Boost.Core, but the problem persists, because now there is an explicit depency in another Boost library.  
  
***Proposed Solution (new attempt)***  
  
As I explained earlier this could be avoided by using SFINAE or some other technique to use alternative named-value-pair-*like* to be use in its place that is not forced to be a Boost.Serialization type.  
As Robert pointer out doing this in pre-C++11 is very difficult, because it needs TMP martial arts.  
I agree.  
  
Here it is my second attempt to solve this, more in the lines of Classic C++.  
  
What if one makes `make_nvp` a static function of the Archive type?  
For non-tag based archives this will return the object itself (i.e. ignore the name right away for the specific `Archive`) or by default return the good-old named value pair.  
  
```c++  
// #include<boost/serialization/nvp.hpp>  // not needed anymore!!!!!  
struct Awesome{  
   int val_;  
   template<class Archive>  
   serialize(Archive& ar, unsigned){  
       ar & Archive::make_nvp("val", val_);  
   }  
};  
```  
  
Since `make_nvp` is here is a dependent-name (is that the correct term?), there is no explicit dependency on Boost *when writing the serialization code*.   
Moreover different archive could have custom `make_nvp` (including different named-validation).  
  
What do you think? Is this a good solution?  
  
Some points:  
  
a) the addition is backward compatible, boost::serialization::make_nvp will continue working, old code will not break.  
b) For the current archives it needs a minimal change, adding a `static make_nvp` that calls `boost::serialization::make_nvp` in the `basic_archive` which is the base class of current archives.  
c) the change is opt-in. If you don't want a hard dependency on Boost.Serialization use this idiom, otherwise continue using `boost::serialization::make_nvp` or `BOOST_NVP`.  
d) The change is implementable in pre-C++11.  
  
Thanks,  
Alfredo

---

## Comment 4

> Username: robertramey  
> Created at: 2020-07-16 21:07:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-659673529  

I think the following will give you what you want.  
  
#include<boost/core/nvp.hpp>   
struct Awesome{  
   int val_;  
   template<class Archive>  
   serialize(Archive& ar, unsigned){  
       ar & boost::serialization::make_nvp("val", val_);  
   }  
};

---

## Comment 5

> Username: correaa  
> Created at: 2020-07-19 02:23:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-660574347  

Yes, having `nvp.hpp` in `code.hpp` is an slightly better situation but it would be ideal for generic serialization code not have any dependency on Boost at all.  
  
The asymmetry is that serialization for non-NVP archives doesn't have a dependency on any header while NVP archives do.  
So, the really ideal thing would not depending in any specific implementation of named-value-pair class but in any generic type that provides .name()->char*, and .value()->T&.  
  
But I understand the challenges to do this in C++03.   
It is really hard. In C++11 it is not so bad, having `std::enable_if` and all that.  
  
What I am experimenting now is to make `make_nvp` dependent on the Archive template.  
  
` ar & myproj::archive_traits<Archive>::make_nvp("val", val_);`  
  
Which wouldn't depend on you changing the library. There is a slight risk of ODR when implementing the general case though.  
  
It would be even better if each the archive concept would be required to have their own static function (`make_nvp`) and typedef `nvp`.  
  
` ar & Archive::make_nvp("val", val_);`  
  
Thanks,  
Alfredo

---

## Comment 6

> Username: robertramey  
> Created at: 2020-07-19 15:56:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/116#issuecomment-660667374  

Note that if one isn't using nvp, there is no need to include any header whatsoever.  And if one doesn't need to support xml archives then one isn't using nvp.  Probably we should use some type_traits to enforce the requirements of "serialize".  This isn't very hard - even in C++03.  And would result in only a few changes.  BTW this may already be addressed with the "check" type traits which are part of the archive classes.  So it may not even be a problem there.  
  
What is really needed is to upgrade the serialization documentation (which is already pretty good)  
  
a) to be more formal so it's more correct and complete  
b) include more extensive case studies and examples to help people use the serialize templates to craft things like "deep copy",  "generic out put", editable archives and the like.  
  
Robert Ramey
