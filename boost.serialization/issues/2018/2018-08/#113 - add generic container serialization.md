# #113 - add generic container serialization [Closed]

> Username: HDembinski  
> Created at: 2018-08-04 09:42:18 UTC  
> Updated at: 2024-01-09 21:33:01 UTC  
> Closed at: 2024-01-09 21:33:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/113  

I noticed that serialization code is missing for `boost::container::vector` and `boost::container::string` and probably more of the Boost containers. Boost should be able to serialize ideally all or at least most of its own containers.  
  
The straight forward thing would be to implement serialization code for each individual container, but that's a maintenance burden and it does not exploit the fact that many containers adhere to a standard interface.   
  
With a bit of concept checking a templated generic serialization code for containers could be written that adhere to the vector interface; similar for string, list, and map. The current code for std::vector, std::string, std::map, std::list could be turned into such a generic code. The concept checking would implement a form of duck-typing.

---

## Comment 1

> Username: rwols  
> Created at: 2018-08-04 09:51:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410437801  

If you're going down this route, I think there should be two forms of "generic" save and load procedures, one for "Sequence" containers and one for "MapLike" containers, e.g. ones that have a `key_type` and a `mapped_type`. For some archive implementations this distinction may be beneficial. For instance, YAML has the notion of a sequence and a map.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-04 15:06:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410455820  

On 8/4/18 2:42 AM, Hans Dembinski wrote:  
>  
> I noticed that serialization code is missing for   
> |boost::container::vector| and |boost::container::string| and probably   
> more of the Boost containers. Boost should be able to serialize   
> ideally all or at least most of its own containers.  
>  
> The straight forward thing would be to implement serialization code   
> for each individual container, but that's a maintenance burden and it   
> does not exploit the fact that many containers adhere to a standard   
> interface.  
>  
> With a bit of concept checking a templated generic serialization code   
> for containers could be written that adhere to the vector interface;   
> similar for string, list, and map. The current code for std::vector,   
> std::string, std::map, std::list could be turned into such a generic   
> code. The concept checking would implement a form of duck-typing.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/serialization/issues/113>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3H2TEhM6Kns-WO4PWz0ZqENbee8nks5uNWx6gaJpZM4Vu7c->.  
>  
  
FYI - the pattern which has been followed from the beginning is:  
  
a) if it's a type in the std:: namespace - included serialization (and a   
test for it) in the serialization library  
b) otherwise, include serialization in the particular boost library.  
  
I would be loath to change this.  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-08-05 00:13:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410485877  

So, in other words, you think I should open an issue in boost.containers. Fair enough, but my point was to provide generic serialization for standard containers, so that Boost.Serialization would support any vector implementation, be it std, boost, Qt, ...

---

## Comment 4

> Username: alfC  
> Created at: 2018-08-05 10:43:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410511523  

I agree that serialization of custom classes should be in the respective custom libraries, but "standard stuff" can be part of Boost.Serization, and that includes "concepts".  
So, I think it Boost.Serialization could incorporate serialization for "*standard* concepts"  
when they are specific enough to implement serialization in a generic way.  
  
Ultimately, if we (Robert) decide that a concept-based serialization is desirable, I think this will necessarily put the serialization functions in the `boost::serialization` namespace, otherwise these functions couldn't be found generically (well assuming that all archives types are in  
`boost::serialization`).  
This implicitly means that the Boost.Serialization is the obvious place to  
put it.  
  
Unfortunately, it looks like there are only a few (official and unofficial)  
concepts that are relevant for both serialization and deserialization at this stage of the standard.  
(the items are a subset extracted from https://stackoverflow.com/a/24229501/225186)  
  
   - TriviallyCopyable  
   - TrivialType  
   - StandardLayoutType  
   - PODType  
  
  
   - Container (of value_type with concept "serializable")  
   - ReversibleContainer (of "serializable")  
   - SequenceContainer (of "serializable")  
   - AssociativeContainer (of "serializable")  
   - UnorderedAssociativeContainer (of "serializable")  
  
  
I am not sure how specific they are to *actually* implement both  
serialization and deserialization (and efficiently, for example there is no  
concept specific enough to have `.reserve(n)`).  
Without real c++ concepts it could be quite tricky to implement, but it is possible.  
  
I am sure things like this have been partially implemented unintentionally by many people, it is a matter of collecting a bunch of good implementations, for example, in (proposed)  
  
```  
#include<boost/serialization/std/concepts/Container.hpp> // e.g. to serialize (any container) only, no specific enough to deserialize  
#include<boost/serialization/std/concepts/SequenceContainer.hpp> // e.g. to deserialize (no much gain in serialize) (things like std::vector), based on `push_back` (`reserve` could be detected?)  
#include<boost/serialization/std/concepts/AssociativeContainer.hpp> // e.g. to deserialize (things like std::map or std::set?), based on `insert`.  
#include<boost/serialization/std/concepts/UnorderredAssociativeContainer // e.g. to deserialize (things like std::unordered_map/set)  
```  
  
As (standard) concepts become more specific or general more implementations  
(and more optimal even) can be added.  
  
I think this setups a more formal way to discuss this issue.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-08-05 16:35:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410531882  

This is an interesting idea.  It looks like you've peered into the implementation of std container serialization and seen how i've attempted to factor out the commonality in the implementations.  So the idea making this more general using concepts is an interesting one and very much in line with the design and subsequence maintenance of the serialization library.  I believe that his approach is probably the main reason it has managed to stay relevant after so many years.   So I think the idea is quite interesting but seems to me it would require a lot more investigation:  
  
a.  concepts isn't yet in C++ and people are still arguing about.  I've approached serialization as "solving the problem we have" rather than "this is the next great thing". I'm not sure you have enough to work with yet.  
  
b. it's very unclear to me how the "overloading" feature of concepts would play in to this.  I might or might not be useful. interesting though.  
  
c. If one were to make a module which implemented serialization for any components which fullfill std concepts. I agree that the place to place would be in the serialization library.  
  
d. the one's you mention above, sequence container, etc. are already factored out and implemented within the serialization library implementation.  These are done in terms of specific containers as type parameters.  So much of the implementation is "done"  
  
e. this would entail a major, major, major effort in terms of documentation, tutorial, examples, etc.  If I were to undertake this, I'd recommend that the serialization transition to boost book or doc book first.  
Same can said for testing.    
  
f. The library does have a good foundation of concepts in the documentation and there is (ad hock) compile time testing of concept definitions.  But this is ad hoc and would likely have to be re-visited to make it consistent with this effort.  
  
g. Then there is the question of backward compatibility of already created archives.  Always an issue.  In this case, it would be stickier than other cases.  
  
In short, an interesting idea worthy of exploration.  But I can't really know where it would lead.

---

## Comment 6

> Username: correaa  
> Created at: 2018-08-05 20:52:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410547204  

That's right Robert. Programming with concepts requires us to think differently (and think really hard). The old paradigm, based on overloads, specific types and matching templates arguments, and the new paradigm can coexists.   
  
I think the weak point is that many concepts still have a long way to go to be specific enough to implement serializaliation (and specially deserialization since "mutability" concepts are less intuitive), let alone efficiently. But slowly getting there wouldn't harm and will help identify the concepts we need. Eventually, concept-based serialization will be equivalent and as efficient to already implemented cases (e.g. std::vector, std::set, std::map) but will be "open" to new containers. This will minimize code and code coupling to an optimal minimum.  
  
The hard part is to identify concepts and hope that the standard introduce good basic concepts to build upon. This is a very interesting project.  
  
BTW, in SE, there is a intriguing posting by Sean Parent https://stackoverflow.com/a/14000046/225186 where he defined the theoretical 'area' function (and "underlying type") for Regular types. In some sense the 'area' is the "size of serialization" (or an upper bound?) and is something to think in relation to concept-based serialization. (this is a generalization to the `size()` function of a container).

---

## Comment 7

> Username: robertramey  
> Created at: 2018-08-05 21:05:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410547964  

On 8/5/18 1:52 PM, Alfredo Correa wrote:  
>  
> That's right Robert. Programming with concepts requires us to think   
> differently (and think really hard). The old paradigm, based on   
> overloads, specific types and matching templates arguments, and the   
> new paradigm can coexists.  
>  
I don't think Its not that much different by the time one gets it   
implemented.  
>  
> I think the weak point is that many concepts still have a long way to go  
>  
Right ...  
>  
>  slowly getting there wouldn't harm and will help identify the   
> concepts we need. Eventually, concept-based serialization will be   
> equivalent and as efficient to already implemented cases (e.g.   
> std::vector, std::set, std::map) but will be "open" to new containers.   
> This will minimize code and code coupling to an optimal minimum.  
>  
> The hard part is to identify concepts and hope that the standard   
> introduce good basic concepts to build upon. This is a very   
> interesting project.  
>  
Actually I see a way to work on this without mucking anything else up.  
  
Implement serialization for boost/collections in terms of   
std::concepts.  The actual implemention code can be stolen from the   
current implementation of std::vector, std::map, etc.  
>  
> BTW, in SE, there is a intriguing posting by Sean Parent   
> https://stackoverflow.com/a/14000046/225186 where he defined the   
> theoretical 'area' function (and "underlying type") for Regular types.   
> In some sense the 'area' is the "size of serialization" (or an upper   
> bound?) and is something to think in relation to concept-based   
> serialization. (this is a generalization to the |size()| function of a   
> container).  
>  
I don't find this convincing or anyway helpful.  
  
If you want a nice project implement serialization for boost   
collections.  But do it in way such that applies to any type with   
specificly define type requirements (concepts).  Then even if it doesn't   
work - you'll still have added serialization into an type which might   
benefit from it.  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 8

> Username: correaa  
> Created at: 2018-08-05 22:17:37 UTC  
> Updated at: 2018-08-05 22:23:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410552606  

On Sun, Aug 5, 2018 at 2:05 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> On 8/5/18 1:52 PM, Alfredo Correa wrote:  
> > Programming with concepts requires us to think  
> > differently (and think really hard). The old paradigm, based on  
> > overloads, specific types and matching templates arguments, and the  
> > new paradigm can coexists.  
> >  
> I don't think Its not that much different by the time one gets it  
> implemented.  
>  
  
Sure, actual code will look like everyday code in the end.  
It is just that, given certain requirements one has to think hard exactly  
how to implement the optimal or acceptable (de)serialization.  
Think, for example, of whether to use `reserve` or not given a concept.  
  
> >  
> > The hard part is to identify concepts and hope that the standard  
> > introduce good basic concepts to build upon. This is a very  
> > interesting project.  
> >  
> Actually I see a way to work on this without mucking anything else up.  
>  
  
Great, are you thinking in the lines of std::enable_if, general  
decltype/detect SFINAE or C++ Concepts-lite?  
  
>  
> Implement serialization for boost/collections in terms of  
> std::concepts.  The actual implemention code can be stolen from the  
> current implementation of std::vector, std::map, etc.  
>  
  
I see, yes, that is called "lifting" the code to get more generic code.  
The challenge is to lift the code maintaining an acceptable efficiency  
(again, think of the example of `reserve`).  
  
> >  
> > BTW, in SE, there is a intriguing posting by Sean Parent  
> > https://stackoverflow.com/a/14000046/225186 where he defined the  
> > theoretical 'area' function (and "underlying type") for Regular types.  
> > In some sense the 'area' is the "size of serialization" (or an upper  
> > bound?) and is something to think in relation to concept-based  
> > serialization. (this is a generalization to the |size()| function of a  
> > container).  
> >  
> I don't find this convincing or anyway helpful.  
>  
  
Yes, there is not a urgent need for this yet. But, I think the idea of  
`underlying_type` and `area` are deeply related to bit-based  
representations of regular types (aka serialization).  
That is why I brought the topic, so we start to think in terms of formal  
concepts.  
(My other point is that any collection serialization that uses `size()` is  
a particular case of the more general idea of `area`).  
Also, I can imagine archive types that can benefit from knowing in advance  
upper bounds to the size of its elements.  
  
  
> If you want a nice project implement serialization for boost  
> collections.  But do it in way such that applies to any type with  
> specificly define type requirements (concepts).  Then even if it doesn't  
> work - you'll still have added serialization into an type which might  
> benefit from it.  
>  
  
Yes, I have some code like this, that is why I have strong opinions.  
Having said all that, I would say the hardest part is how informally  
defined and how badly scattered is the documentation of concepts in the C++  
standard.  
  
I think the first step is to identify the concepts we would like to use and  
implement, that is why I put this initial list.  
We have to understand that the correspondence between serialization and  
deserialization will be broken for some concepts.  
For example, a Sequence can be always serialized, but it can be  
deserialized only when the target size is the expected one (because no  
concept that I know of actually has the requirement of a resize).  
That is why I say that deserialization based on concepts probably is still  
challenging without further refinements (for example ResizableSequence or ElasticSequence, it doesn't have a name as far as I know).

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-08-06 09:10:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410641391  

I don't see why it is necessary to push the discussion towards concepts. We have SFINAE and `enable_if`, even in pre C++11. With this you can make a generic serialization function for vector-like containers that have methods required for the serialization to work, namely `reserve(n)` etc. There is no need to make this more complicated.

---

## Comment 10

> Username: HDembinski  
> Created at: 2018-08-06 09:13:41 UTC  
> Updated at: 2018-08-06 09:14:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-410642416  

On top of the generic version, there can be specialized overloads for specific vector-types if needed. The compiler will use the generic version only as a last resort. The same approach can be used for set-like, map-like etc., I just want to keep the discussion simple and concrete for now, so I focus on vector-like.

---

## Comment 11

> Username: sdebionne  
> Created at: 2019-05-27 15:49:03 UTC  
> Updated at: 2019-05-29 08:20:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496252621  

I would also be interested in generic serialization.  
  
To use SFINAE, the default implementation of `serialize()`, would need to be "removed" else one would get ambiguous overload issues.  
  
For instance, with:  
  
``` c++  
template <typename Archive, typename T>  
void serialize(Archive& ar, T& d, const unsigned int version, std::enable_if_t< is_bus_stop<T>::value >* = nullptr) {}  
```  
  
I get:  
  
```   
error: call of overloaded 'serialize(boost::archive::text_oarchive&, bus_stop&, const boost::serialization::version_type&)' is ambiguous  
     serialize(ar, t, v);  
     ~~~~~~~~~^~~~~~~~~~  
boost/serialization/serialization.hpp:65:13: note: candidate: void boost::serialization::serialize(Archive&, T&, unsigned int) [with Archive = boost::archive::text_oarchive; T = person]  
 inline void serialize(  
             ^~~~~~~~~  
other/serialization.hpp:24:8: note: candidate: void boost::serialization::serialize(Archive&, T&, unsigned int, std::enable_if_t<is_bus_stop<T>::value>*) [with Archive = boost::archive::text_oarchive; T = person; std::enable_if_t<boost::hana::Struct<T>::value> = void]  
   void serialize(Archive& ar, T& d, const unsigned int version, std::enable_if_t<hana::Struct<T>::value>* = nullptr)  
        ^~~~~~~~~  
```   
  
Looking at the `boost/serialization/serialization.hpp` header, it looks like a workaround has already been implemented, but for a different purpose (compilers lacking proper PTFO support) :  
  
``` c++  
// note the usage of function overloading to compensate that C++ does not  
// currently support Partial Template Specialization for function templates   
// We have declared the version number as "const unsigned long".    
// Overriding templates for specific data types should declare the version  
// number as "const unsigned int". Template matching will first be applied  
// to functions with the same version types - that is the overloads.    
// If there is no declared function prototype that matches, the second argument  
// will be converted to "const unsigned long" and a match will be made with   
// one of the default template functions below.  
```  
  
The workaround was removed in 5a94e5e4eb01cd8c941db725e5f89965a91740a9 though.

---

## Comment 12

> Username: sdebionne  
> Created at: 2019-05-29 07:56:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496827730  

We could add some level of reflection to check if a given class has a serialize member function, something along the (c++11) lines of:   
  
``` c++  
#include <type_traits>  
  
template <typename Archive, typename C>  
struct has_serialize  
{  
private:  
    template<typename T>  
    static constexpr auto check(T*)  
        -> typename  
        std::is_same<  
            decltype(std::declval<T>().serialize(std::declval<Archive&>(), std::declval<unsigned int>())),  
            void  
        >::type;  
  
    template<typename>  
    static constexpr std::false_type check(...);  
  
    typedef decltype(check<C>(0)) type;  
  
public:  
    static constexpr bool value = type::value;  
};  
```  
  
The default implementation would then be easily disabled with:  
  
``` c++  
// default implementation - call the member function "serialize"  
template <class Archive, class T>  
inline void serialize(  
	Archive & ar, T & t, const unsigned int file_version, std::enable_if_t<has_serialize<Archive, T>::value>* = nullptr  
){  
	access::serialize(ar, t, static_cast<unsigned int>(file_version));  
}  
```  
  
@HDembinski Is that a path worth exploring?

---

## Comment 13

> Username: HDembinski  
> Created at: 2019-05-29 09:32:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496861092  

@sdebionne I think we have different things in mind. My point was the following: to serialize a vector-like containter, you don't need to access its internals. Any container that supports the vector interface can be (de)serialized with the same templated code, something along the lines of (pseudo-code):  
```  
template <class Archive, class Vector>  
void save(Archive& ar, const Vector& v, unsigned version) {  
   ar << v.size();  
   ar << boost::serialization::make_array(v.data(), v.size());  
}  
  
template <class Archive, class Vector>  
void load(Archive& ar, Vector& v, unsigned version) {  
   std::size_t size = 0;  
   ar >> size;  
   v.reset(size);  
   ar >> boost::serialization::make_array(v.data(), v.size());  
}  
  
template<class Archive, class Vector>  
inline void serialize(  
    Archive & ar,  
    Vector & t,  
    const unsigned int file_version  
){  
    boost::serialization::split_free(ar, t, file_version);  
}  
```  
  
To avoid ambiguities, these three functions may only participate in overload resolution when the class `Vector` implements the vector interface, in particular it must have a `.size()` method, a `.data()` method and a `.reset()` method. This can be checked with SFINAE tricks even in pre-C++11.

---

## Comment 14

> Username: HDembinski  
> Created at: 2019-05-29 09:35:11 UTC  
> Updated at: 2019-05-29 09:35:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496862007  

@sdebionne Your `decltype` code is C++11, so it cannot be used in Boost.Serialization. I am sure Robert doesn't want to drop support for pre-C++11 compilers. Boost.Serialization code could be much simpler if support for old language standards was dropped.

---

## Comment 15

> Username: sdebionne  
> Created at: 2019-05-29 10:10:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496874281  

@HDembinski My point was that a generic version for Vector-like containers will be ambiguous with the default implementation that comes with Boost.Serialization  
  
```  
// default implementation - call the member function "serialize"  
template <class Archive, class T>  
inline void serialize(  
	Archive & ar, T & t, const unsigned int file_version) {  
	access::serialize(ar, t, static_cast<unsigned int>(file_version));  
}  
```  
  
that needs to be disabled somehow (or have lower priority). Or am I missing something?  
  
`struct has_serialize` is implemented in C++11 for an easy solution, but it probably does not have to.

---

## Comment 16

> Username: HDembinski  
> Created at: 2019-05-29 10:14:43 UTC  
> Updated at: 2019-05-29 10:31:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496875777  

> @HDembinski My point was that a generic version for Vector-like containers will be ambiguous with the default implementation that comes with Boost.Serialization.  
  
And I said above that SFINAE tricks should be used to avoid ambiguities, but I think you are right, this probably requires special support in the library for a generic free serialize function.  
  
```  
template <class Archive, class T>  
void generic_save(Archive& ar, const T& t, unsigned version, vector_interface_tag) {  
  // ...  
}  
  
template <class Archive, class T>  
void generic_load(Archive& ar, T& t, unsigned version, vector_interface_tag) {  
  // ...  
}  
  
template <class Archive, class T>  
void generic_save(Archive& ar, const T& t, unsigned version, map_interface_tag) {  
  // ...  
}  
  
template <class Archive, class T>  
void generic_load(Archive& ar, T& t, unsigned version, map_interface_tag) {  
  // ...  
}  
  
// more generic_X implementations  
  
template<class Archive, class T>  
inline void serialize(  
    Archive & ar,  
    T & t,  
    const unsigned int file_version  
){  
    boost::serialization::generic_split_free(ar, t, file_version, interface_tag<T>());  
}  
```

---

## Comment 17

> Username: sdebionne  
> Created at: 2019-05-29 10:23:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496878649  

SFINAE needs to be used on both the vector and default implementations:  
 - on the vector side to *enable* (what your code shows)  
- on the default side to *disable* (the issue that my code try to solve)

---

## Comment 18

> Username: HDembinski  
> Created at: 2019-05-29 10:24:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496878802  

I corrected my statement.

---

## Comment 19

> Username: sdebionne  
> Created at: 2019-05-29 10:25:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496879265  

> this probably requires special support in the library for a generic the free serialize function.  
  
This is what I meant to demonstrate :-)

---

## Comment 20

> Username: HDembinski  
> Created at: 2019-05-29 10:28:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496880080  

You wrote:  
  
> We could add some level of reflection to check if a given class has a serialize member function, something along the (c++11) lines of:  
  
which I think has nothing to do with this point. Boost.Serialization already detects the presence of a serialize method.

---

## Comment 21

> Username: HDembinski  
> Created at: 2019-05-29 10:38:56 UTC  
> Updated at: 2019-05-29 10:41:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496883123  

No, it should work without special support from Boost.Serialization.  
```  
#include <type_traits>  
#include <vector>  
  
template <class Archive, class T>  
std::enable_if_t<std::is_floating_point<T>::value> serialize(Archive& ar, T& t, unsigned version) {}  
  
template <class Archive, class T>  
std::enable_if_t<std::is_integral<T>::value> serialize(Archive& ar, T& t, unsigned version) {}  
  
template <class Archive, class T>  
void serialize(Archive& ar, std::vector<T>& v, unsigned version) {}  
  
int main() {  
    struct archive {} ar;  
    double a = 1.0;  
    int b = 1;  
    serialize(ar, a, 1);  
    serialize(ar, b, 1);  
}  
```  
Instead of `std::is_floating_point` etc one would check for the presence of the vector interface.  
  
There is no ambiguity between the new generic and the existing specific implementations, because the latter are considered a better match by the compiler.

---

## Comment 22

> Username: HDembinski  
> Created at: 2019-05-29 11:34:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496898863  

The conceptual problem with this approach is that the interface checks must be mutually exclusive. They must be designed so that not two of them return true for a given type, otherwise there will be ambiguities again.

---

## Comment 23

> Username: sdebionne  
> Created at: 2019-05-29 13:20:13 UTC  
> Updated at: 2019-05-29 13:58:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496933431  

> which I think has nothing to do with this point. Boost.Serialization already detects the presence of a serialize method.  
  
AFAIU the default free implementation of `serialize()` calls the member function serialization (through the `access` class):  
  
``` c++  
template<class Archive, class T>  
inline void serialize(  
    Archive & ar,   
    T & t,   
    const unsigned int file_version  
){  
    // invoke member function for class T  
    t.serialize(ar, file_version);  
}  
```  
  
The free implementation can be overloaded for a specific type and then has higher priority during  overload resolution. So far so good.  
  
For a generic type, if you want to keep the same `serialize()` interface, without adding an other dispatch mechanism like the `interface_tag`, then the default implementation has to be removed from overload resolution based on whether the class has a `serialize()` member function. Hence the reflection.

---

## Comment 24

> Username: robertramey  
> Created at: 2019-05-29 14:11:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496953656  

>I am sure Robert doesn't want to drop support for pre-C++11 compilers.   
True  
  
> @sdebionne Your `decltype` code is C++11, so it cannot be used in Boost.Serialization.   
Not True  
  
I don't see any conflict here. I've maintained that code should be backward compatible as long a possible.  Since the code was written years ago, the imposes no significant burden.  
  
BUT that's not the same as saying that the code cannot evolve in ways only supported by more recent versions of C++.  There is no reasons that new features need to be available to older compilers.  After all, the programs already written don't need to use the new features anyway.  The only thing necessary is to make them conditional in a way that is clear, regular, transparent and doesn't impose any burden on anyone who doesn't use them.  The serialization library is built upon John Maddocks boost config library which permits programs to be be backward and forward compatible in a portable way for an indefinite future.  
  
>Boost.Serialization code could be much simpler if support for old language standards was dropped.  
True - but not relevant here.

---

## Comment 25

> Username: sdebionne  
> Created at: 2019-05-29 14:11:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496953877  

Some overlap between our messages, sorry.  
  
```  
template <class Archive, class T>  
std::enable_if_t<std::is_floating_point<T>::value> serialize(Archive& ar, T& t, unsigned version) {}  
  
template <class Archive, class T>  
std::enable_if_t<std::is_integral<T>::value> serialize(Archive& ar, T& t, unsigned version) {}  
```  
  
These will be ambiguous with the default free implementation of `serialize()` if not removed. This is the only support we need from the library.  
  
> The conceptual problem with this approach is that the interface checks must be mutually exclusive. They must be designed so that not two of them return true for a given type, otherwise there will be ambiguities again.  
  
I agree. But is there a better solution?

---

## Comment 26

> Username: robertramey  
> Created at: 2019-05-29 14:23:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496958834  

Note there is much information available by googling C++ detection idiom.  I had never considered what implications this might idea might have for serialization.  But it looks like it might be possible to implement serialization - "automagically" with this.  And, considering the above statement on C++11+ support, this would not break backward compatibility in either code or archives.  
  
Actually, since this is implemented with enable_if and type_traits, it wouldn't even require C++11 and would work with C++03 compilers.  
  
Oh - and there is the fact that the serialization library currently has a set of type traits with things like serialization implementation level, etc which might be sharpened up to provide service to reflection based serialization.  
  
Summary  
=======  
  
Project:  Reflection Based Serialization  
Requirements:  C++03 or higher  
Goal: Permit one to avoid writing recursive serialization functions by hand.  
Motivation: Because it would be cool and throw everyone for a loop.

---

## Comment 27

> Username: robertramey  
> Created at: 2019-05-29 14:36:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-496964439  

FYI - there are some other interesting - and easier projects - for those who like working with the library.  
  
a) translation of documentation to XML using XmlMind.  This would result in documentation which:  
1) plays nicer with boost tools  
2) support creation of documentation as PDF.  
3) would be much easier (for me) to keep updated.  
  
b) creation for more archive classes  
1) portable binary archive  
2) a JSON archive  
3) an archive which along with the data, generates google protocol buffers files so that archives could be read by non-C++ programs  
  
c) complete refactor/separation of archive/serialization so that including boost serialization does not include files from "archive".  This would eliminate the confusion of dependency checking tools and prevent them from including dependencies for the whole world when one just wants to serialize something simple.  
  
d) support serialization on platforms not supporting RTTI.  The system purportedly supports this, but it doesn't quite work.  Since no one has ever complained I never invested the time to fix this.  
  
e) tweak implementation and API so that we eliminate throwing destructor in XML archives.  This would permit it to pass some sanitizers which it fails now.  
  
f) peal Singleton off in to it's own boost library.  
  
The above are more or less in order of difficulty.  Of course the above should maintain backward compatibility.  
  
Good Luck!  
  
And thanks to all of you over the years who have contributed to maintenance and enhancement of the library.  I see no standards proposal which might replace this library.  In spite of some interesting library submissions on Github, I see no serious competitor to this library in it's bread and depth for serious work.  
  
Robert Ramey

---

## Comment 28

> Username: HDembinski  
> Created at: 2019-05-30 16:28:40 UTC  
> Updated at: 2019-05-30 16:29:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-497392680  

> These will be ambiguous with the default free implementation of serialize() if not removed. This is the only support we need from the library.  
  
@sdebionne I don't think so. Do you have proof for your claim?

---

## Comment 29

> Username: HDembinski  
> Created at: 2019-05-30 16:34:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-497394593  

> FYI - there are some other interesting - and easier projects - for those who like working with the library.  
  
@robertramey This is a nice list of useful new features, but if all these items are put together, it is essentially a rewrite of the library. Rewriting boost.serialization along these lines and raising the c++ standard to c++11 in the process would be a worthwhile project, similar to how mpl was replaced by mp11. The boost.serialization code is pretty complex, but mostly because TMP was very awkward in pre-c++11 times. The rewrite could be simpler and easier to maintain.

---

## Comment 30

> Username: robertramey  
> Created at: 2019-05-30 17:32:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-497414427  

>  if all these items are put together, it is essentially a rewrite of the library.  
  
LOL - I'll leave this up to the person who actually undertakes the work - which is not likely to be me. I should note that the serialization as a project is way underestimated - as it was by me.  Years of effort along with two grueling boost reviews and lot's and lot's of "help" I had to respond to.  
  
But your observation is a very interesting one which comes up in software development on a regular basis.  I would note that it is also very common that such ideas often result in failure in that the new system turns out to be less of a success than the system it's meant to replace - if in fact it ever gets done.  
  
> if all these items are put together, it is essentially a rewrite of the library  
  
I actually disagree with this view. The above list is pretty much orthogonal in that the implementation of one feature wouldn't affect or be affected by the implementation of another feature.  
  
>raising the c++ standard to c++11  
  
I can see the attraction of raising the requirement to C++11 minimum.  It would simplify adding new features and fixing old ones.  Same could be said for replacing mpl with mp11.  But of course that would obsolete support for older C++ compilers - and likely previously existing archives as well. If it were me, I wouldn't go there just to implement any (or even all) of the features/extensions above.  The list above is really quite modest in its scope.  It's just that the serialization library is hard due to things other than the restriction to C++03.  
  
Actually a much more interesting idea would be to make a new serialization library (boost.serialization2?) entirely from scratch using the very latest C++ facilities.  This new library would have the following features:  
  
a) it would not be required to be compatible with the current library.  It would not be required to be able to read archives created by the current library.  It might do so or it might provide a migration path.  Not that this would be hard for users to do.  It would only be a case of:  
`  
user_object x;  
{  
std::ifstream is("name of file holding old archive");  
boost::archive text_iarchive ia(is);  
x << ia;  
}  
{  
std::ofstream os("name of file holding new archive");  
boost::archive2 text_oarchive(os);  
oa << x;  
}  
`  
conversion done - easy as pie.  
  
b) it would draw inspiration from the current library as well as other efforts such as cereal.  
  
c) it would be free to use the experience of the current library to narrow the scope of the new library to make it simpler.  For example, cereal doesn't support raw pointers.  It just supports serialization of pointers through a couple of special types like boost::smart_pointer. This would make implementation much, much easier.  It might also not support of tracking.  This would leave this up to the user.  Again making implementation much safer.  It might not build the concept of versioning into the library, but leave it to uses to serialize their own version numbers.  
  
d) It would likely be header only.  The current library invests a lot of effort to maintain separate parts - one type specific as header only and none specific as precompiled library.  At the time the library was first conceived and implemented, code size and reuse were considered of much greater importance than they are now.  So eliminating this feature of the code would make a library much easier to write and would also be popular with the current generation of programmers who find working with separately compiled libraries a burden.  
  
e) It might serve as a better candidate for migration into the C++ standard.  Traditionally, Boost has served as the incubator for libraries for the C++ standard.  More recently, this role of boost has been cast aside and its now more as fixing components that the C++ standards committee has messed up.  So it's not at all clear what the future of such new serialization library might be in this regard.  It's very clear that the current serialization library would not be model for C++ standard serialization.  Years ago I had assumed that with a few years experience with the boost serialization library, someone might a serious proposal for C++ standard serialization.  Much to my surprise this has never happened.  Not even an unserious proposal has been submitted.    
  
These points, along with the likely millions of archives which need to be read with the old code will likely mean that the boost serialization library will be around for a very, very long time if we're willing to tweak it from time to time.  It will likely be around as long as C++ macros are around.  
  
Sooooo - Now you've got TWO projects worth of investment of effort.  Extending the the current  library and making a whole new one.  Each is worthy and important in it's own way.   
  
Good Luck with this.  
  
PS - helpful tip:  Rather than asking "Do you have proof for your claim?"  try phrasing your question as "Hmmm - I can't see why this would be necessary, could you expand your explanation on why this would be the case?".  This asks the person for help rather than challenging him to defend his position.  People are suckers for other people asking for help.

---

## Comment 31

> Username: sdebionne  
> Created at: 2019-06-03 11:44:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498223324  

> @sdebionne I don't think so. Do you have proof for your claim?  
  
```c++  
#include <type_traits>  
#include <boost/serialization/serialization.hpp>  
  
struct foo { int i; };  
  
//Check if T is a model of the Foo concept  
template <typename T> struct is_foo : std::false_type {};  
template <> struct is_foo<foo> : std::true_type {};  
  
namespace boost {  
    namespace serialization {  
  
template <class Archive, class T>  
std::enable_if_t<is_foo<T>::value> serialize(Archive& ar, T& t, const unsigned int version) {  
    ar & t.i;  
}  
  
}} //namespace boost::serialization  
  
int main() {  
    struct archive {} ar;  
    foo f{ 1 };  
  
    // 'boost::serialization::serialize': ambiguous call to overloaded function  
    boost::serialization::serialize(ar, f, 1);  
}  
```

---

## Comment 32

> Username: HDembinski  
> Created at: 2019-06-03 18:49:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498379754  

@sdebionne Sorry, you were right. Thank you for the nice short proof.

---

## Comment 33

> Username: HDembinski  
> Created at: 2019-06-03 18:52:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498380724  

@robertramey I like the idea of a serialization2 library for the reasons you pointed out, and although I am tempted to work on it, I will probably resist that temptation. I had a look into the Boost.Serialization and I can imagine what huge amount of development effort this was. Like you said, rewriting is easier than designing from scratch, but for a huge library like serialization it would still be a serious undertaking.

---

## Comment 34

> Username: robertramey  
> Created at: 2019-06-03 20:17:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498409980  

LOL - thanks for recognizing this.  FWIW - it seems pretty simple when it started out.  But adding in all the stuff that was "required" made it very hard.  Anyone who undertook this would have the advantage of being able to see boost serialization along with some others to get a better idea of the cost and benefits of every feature.  Maybe you want to submit a proposal to the C++ standards committee.  Even this would be a substantial undertaking.

---

## Comment 35

> Username: alfC  
> Created at: 2019-06-03 21:52:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498440245  

Hi Robert,  
  
I have a question for you, given your experience.  
  
Do you think *reflection* when (if) available in C++ will make writing such  
library easier or is it just hopeful thinking?  
  
On Mon, Jun 3, 2019 at 13:17 Robert Ramey <notifications@github.com> wrote:  
  
> LOL - thanks for recognizing this. FWIW - it seems pretty simple when it  
> started out. But adding in all the stuff that was "required" made it very  
> hard. Anyone who undertook this would have the advantage of being able to  
> see boost serialization along with some others to get a better idea of the  
> cost and benefits of every feature. Maybe you want to submit a proposal to  
> the C++ standards committee. Even this would be a substantial undertaking.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/113?email_source=notifications&email_token=AARR6YGUMULSMXNAPXJDST3PYV36JA5CNFSM4FN3W47KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODW2SD7A#issuecomment-498409980>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AARR6YHIECAWJBGNZQ73B7TPYV36JANCNFSM4FN3W47A>  
> .  
>

---

## Comment 36

> Username: robertramey  
> Created at: 2019-06-03 22:28:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498449790  

wishful thinking.  
  
Since you're asking the question here, I'm presuming you've got some personal experience with the boost and perhaps other serialization libraries.  
  
Suppose you want to serialize some data type.  For primitive data types like int, there's nothing to do.  For compound data types, you need to specify a list of members which hold the permanent state of the type.  Right now we do that by writing the "serialize" function.  This often times consists of writing a "serialize" function which has ar & ... for each member.  If we had reflection, this could be generated automagically via TMP.  So this looks attractive.  In fact, I'm willing to predict that within a week of reflection becoming available, some smart guy will do just that.  
  
So far so good.  
  
But think of your experience in the real world.  What about the following?  
a) How do you automatically know which variables to save and which you can skip?  
b) what about versioning.  
c) what about special cases like std::vector where you need to specify (or not) the number of elements.  
...  
  
It's my view that reflection won't save very much work.  
  
Now if you look into the library, you'll see the whole "archive" concept and code required to support it. It's quite complex.  In part because it exploits the refection we already have (via type traits which reveal whether a datum is a pointer, value, const, etc...) to load/save the data in an appropriate manner. Addition of reflection won't help implementation of this at all.  
  
So ... I believe that though reflection might have it's uses, the benefit it will give to support serialization will in fact be very small - if any.  One will still have to specify the "extra" information required.  Then someone will propose and "attribute" attached to data members to specify the extra information.  OK that can be made to work.  But by the time this is done, we will have come full circle in making the language more elaborate to specify things that we're already doing through library facilities.  This will continue the trend in C++ to move things that have been supported quite well via libraries (e.g. contracts) into the core language itself which makes things more complex with no corresponding benefit.  
  
Robert Ramey

---

## Comment 37

> Username: HDembinski  
> Created at: 2019-06-04 08:16:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498570823  

> It's my view that reflection won't save very much work.  
  
I basically agree with Robert, but in my ideal world, serialization would work like copy constructors and assignment operators. For simple cases, the compiler should automatically generate the serialization operator, but the user can always overload it with a special version.

---

## Comment 38

> Username: alfC  
> Created at: 2019-06-04 15:38:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498725817  

Ok, thanks Robert. I had the same impression. The challenge of  
serialization is to identify how a class is a value type, and if it is.  
  
I also agree with Hans. I agree that serialization should be recursive by  
default. The problem is to agree on what operator or name to use. We can  
reserve save(Ar&)/load(Ar&) or use the binary operators <<,>>,&. The  
problem is that none of them can be members of the class.  
  
On Tue, Jun 4, 2019 at 01:16 Hans Dembinski <notifications@github.com>  
wrote:  
  
> It's my view that reflection won't save very much work.  
>  
> I basically agree with Robert, but in my ideal world, serialization would  
> work like copy constructors and assignment operators. For simple cases, the  
> compiler should automatically generate the serialization operator, but the  
> user can always overload it with a special version.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/113?email_source=notifications&email_token=AARR6YGESIV5WSR6WJGOBU3PYYQHHA5CNFSM4FN3W47KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODW3ZMRY#issuecomment-498570823>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AARR6YAEHXB6GLSERYP4Q2TPYYQHHANCNFSM4FN3W47A>  
> .  
>

---

## Comment 39

> Username: sdebionne  
> Created at: 2019-06-05 07:45:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-498975578  

I like the idea of serialization2 too and I wonder if [Cereal](https://uscilab.github.io/cereal/), somehow boostified, could be that rewrite. Looking at the "Key Differences From Boost", it sounds like there are very little missing features (e.g. versioning) that could be easily added or features that could be dropped (e.g. support for raw pointers). @robertramey Any chance that you were in touch with the uscilab devs and discussed a boost review of Cereal?  
As a side note, Boost.MPI uses Boost.Serialization for custom types, the `packed_archive` would eventually need to be adapted.

---

## Comment 40

> Username: robertramey  
> Created at: 2019-06-05 15:51:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-499142845  

I looked at the information on cereal to get a sense of it.  I've seen bench marks on it and heard good feedback on it.  My sense is that it would be a very worthy basis on which to build the next generation serialization library:  
  
a) it excludes raw pointers.  It supports specific smart point implementations with custom implementations.   It would also exclude things like pointer tracking, serialization of derived types though calls to the a base type etc..   This would result in a much simpler library implementation and many users of serialization wouldn't miss this feature.  In the context of a boost library, I'm not sure how that would work out.  Often a boost library has to fulfill even the most obscure requirements in order to get a consensus in review.  The support of raw pointers is a trade off between complexity and completeness.  One reason for thinking in terms of serialization2 would be to "please everyone" by offering a choice between the two options.  
  
b) is header only.  This results in a number of benefits.  First, due to the ability of C++ optimizers to collapse inline code, it results in execution time of about half of the current library.  The current library is pretty fast, but of course faster is always better.  It also simplifies builds due to the elimination of the linkage step.  Users love this because separate/compilation of C++ is getting more complex all the time.  Also machines are much, much, much faster, and there is much, much, much more memory available to the build, than when the serialization library was built.  So the increase stress of constantly (re) building a header library isn't such a burden.  (But that burden has been growing as faster or faster than the machine resources!).  But still there is something to be said for the usage of the pimple idiom to implement the library and minimize rebuilding.  No one complains about the compile time of serialization library either when it's built or when it's used.  The current system supports separate compilation on an applications serialization code.  But to many users today - separate compilation is a burden rather than a feature.  It's a trade off.  A good design could support both.  
  
c) versioning not supported intrinsically. Versioning is built into the serialization library.  This is blessing and a curse. One one hand it forces users to version stuff - even if only by default, but it adds a little overhead.  If I had to do it again, I'd exclude default versioning.  I'd just let the user specify his how version member along and maintain it with some boiler plate code (that is the version type would be serializable).  
  
d) I don't think serialization through virtual base classes can be supported with a cereal-like system.  
  
e) The (more or less) clean separation between the serialization concept and archive concept would be maintained as both boost and cereal do now.  A large part of the documentation of the boost serialization library would carry over to a cereal inspired system.  
  
Sooooo - a cereal like system would have some very attractive features and I think would complement rather than replace the current boost serialization library.  It would be much less complex to implement than the current library. But it would still be a lot of work.  I think it would be very popular with boost users - simpler to use, faster and "concept compatible" with the old library.  If it implemented or used the current archive classes so it was a "drop-in" replacement it would be a huge hit.  If it interoperated with archives created by boost serialization it would be an even bigger hit - this might not be possible.  
  
Still, even with the boost serialization and cereal to leverage on - it still would be a lot of work.

---

## Comment 41

> Username: HDembinski  
> Created at: 2019-06-06 11:33:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-499457876  

>  But still there is something to be said for the usage of the pimple idiom to implement the library and minimize rebuilding.  
  
I agree, serialization code is always "cold".

---

## Comment 42

> Username: HDembinski  
> Created at: 2019-06-06 11:33:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/113#issuecomment-499457981  

Sorry! I clicked the wrong button.
