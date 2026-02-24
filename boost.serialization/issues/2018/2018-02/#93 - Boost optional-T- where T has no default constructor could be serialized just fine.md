# #93 - Boost optional<T> where T has no default constructor could be serialized just fine [Closed]

> Username: Ramambahara  
> Created at: 2018-02-07 09:13:11 UTC  
> Updated at: 2023-03-08 13:43:10 UTC  
> Closed at: 2018-04-30 20:04:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/93  

I've planed to use boost::serialization in our RPC and discovered that serialization of the optional<T> where T has no default constructor is forbidden by [the static assert in optional.hpp](https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/optional.hpp#L50). Comment says that it is related to some sort of inherent limitation, but I can not understand what is that limitation.  
  
If I remove the static assert then code provided below works just fine:  
```c++  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/serialization/optional.hpp>  
#include <boost/optional.hpp>  
  
#include <iostream>  
#include <sstream>  
  
struct Bar  
{  
  Bar(int m) : m{m}  {}//no default c-tor  
  int m;  
};  
  
struct Foo  
{  
  boost::optional<Bar> bar;  
};  
  
namespace boost::serialization  
{  
template<class Archive>  
void serialize(Archive& ar, Foo& foo, const unsigned int)  
{  
  ar & foo.bar;  
}  
  
template<class Archive>  
void serialize(Archive&, Bar&, const unsigned int)  
{/*save_construct_data and load_construct_data provided instead*/}  
  
template<class Archive>  
inline void save_construct_data(Archive & ar, const Bar * v, const unsigned int)  
{  
  ar << v->m;  
}  
  
template<class Archive>  
inline void load_construct_data(Archive & ar, Bar * p, const unsigned int)  
{  
  int m = {};  
  ar >> m;  
  
  ::new(p)Bar{m};  
}  
  
}//namespace boost::serialization   
  
int main()  
{  
  std::stringstream ss;  
  
  {  
    Foo foo{ Bar{123} };  
  
    std::cout << "data before serialization is: " << foo.bar->m << std::endl;  
  
    boost::archive::text_oarchive oa(ss);  
    oa << foo;  
  }  
  
  {  
    Foo foo;  
    boost::archive::text_iarchive ia(ss);  
    ia >> foo;  
  
    std::cout << "data after deserialization is: " << foo.bar->m << std::endl;  
  }  
  
  return 0;  
}  
```  
@robertramey Could you please explain this limitation in documentation or explain it to me so I could add pull request with documentation update? Or, if there no reason for this static assert to exist, I would be really happy if we can remove it.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-04-30 20:04:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/93#issuecomment-385512524  

The comment says:  
  
    // It is an inherent limitation to the serialization of optional.hpp  
    // that the underlying type must be either a pointer or must have a  
    // default constructor.  It's possible that this could change sometime  
    // in the future, but for now, one will have to work around it.  This can  
  
Since you are serializing a pointer rather than an optional object, the limitation doesn't apply.  
  
The reason for the limitation is the way deserialization has to work:  
  
a) construct the object - we don't know what type it is yet so the default constructor is required  
b) set the object from data in the file.  
  
But if you serialize a pointer:  
  
a) create an empty pointer  
b) when the data is read from a file, it it construct with that data - no default ctor required  
c) create a new object  
d) set pointer to that object.

---

## Comment 2

> Username: Danecchio-ai  
> Created at: 2023-03-08 13:42:31 UTC  
> Updated at: 2023-03-08 13:43:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/93#issuecomment-1460173843  

Sorry, I still don't understand. The only thing that matters is that the object contained inside the boost::optional instance is that it's *serializable*, no? I think the static assert is using the existence of a default constructor as a bad proxy for serializability. In the asker's example, what is being serialized is a boost::optional\<Bar\> instance, no pointers.
