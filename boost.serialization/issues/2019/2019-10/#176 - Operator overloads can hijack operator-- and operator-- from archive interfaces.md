# #176 - Operator overloads can hijack operator<< and operator>> from archive interfaces [Closed]

> Username: kevle  
> Created at: 2019-10-10 14:16:19 UTC  
> Updated at: 2019-10-15 20:52:42 UTC  
> Closed at: 2019-10-15 20:52:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/176  

During (de-)serialization of objects, a freestanding operator>> / operator<< may be chosen instead of the appropriate serialize routine. This is due to the way the interface class calls the respective operators.  
  
This is the relevant code from archive/detail/interface_oarchive.hpp  
```  
    template<class T>  
    Archive & operator<<(const T & t){  
        this->This()->save_override(t);  
        return * this->This();  
    }  
      
    // the & operator   
    template<class T>  
    Archive & operator&(const T & t){  
        return * this ->This() << t;  // This might not call Archive & operator<<(const T & t) but a freestanding version.  
    }  
```  
  
Here is a ( somewhat constructed ) illustration of the current behavior:  
```  
  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/serialization/serialization.hpp>  
  
#include <sstream>  
#include <iostream>  
  
struct CustomType  
{  
	int i;  
};  
  
struct ContainerType  
{  
	CustomType t;  
};  
  
template<typename Stream>   
Stream& operator>>(Stream& is, CustomType& x) {  
	is >> x.i;  
	std::cout << "operator>>\n";  
	return is;  
}  
template<typename Stream>   
Stream& operator<<(Stream& os, const CustomType& x) {  
	std::cout << "operator<<" << x.i << "\n";  
	os << 0;  
	return os;  
}  
  
namespace boost {  
	namespace serialization {  
		template<class Archive>  
		void serialize(Archive& ar, CustomType& c, const unsigned int)  
		{  
			ar& c.i; // will not be used  
		}  
		template<class Archive>  
		void serialize(Archive& ar, ContainerType& c, const unsigned int)  
		{  
			ar& c.t;  
		}  
	}  
}  
  
int main(int argc, char* argv[])  
{  
	ContainerType store{5};  
    std::stringstream out;  
	boost::archive::binary_oarchive oa(out);  
    oa << store;  
  
	ContainerType load;  
	std::stringstream in(out.str());  
	boost::archive::binary_iarchive ia(in);  
	ia >> load; // The value in load.t.i is 0 because of Stream& operator<<(Stream& os, const CustomType& x)  
}  
```  
  
A possible remedy to this situation would be explicitly calling the overloaded operator:  
```  
    // the & operator   
    template<class T>  
    Archive & operator&(const T & t){  
        return this->This()->operator<<(t);  
    }  
```  
  
I have prepared a branch using the explicit overloads. Since I am not sure whether the current behavior is intentional or even required under some circumstances I have not opened a pull request.  
  
https://github.com/kevle/serialization/tree/explicit_chevrons

---

## Comment 1

> Username: robertramey  
> Created at: 2019-10-10 15:54:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/176#issuecomment-540652540  

Very interesting.  Actually amazing that I nor it seems that anyone else has noticed this before.  Truthfully, I'm a little naive about the issues related to overloaded lookups across namespaces.  I would have to spend some time looking at this.  In the meantime a few observations:  
  
a) This might be related to a "hack" I made which explicitly permits overloads where C++ would otherwise not.  It's included (I think in iseralize and serialize.   I did it 15 years ago to make the package easier to use without much thought.  Of course I know a lot more.  It's an interesting question as to whether this is cause of our issue here and whether it's a good idea and if it wasn't what would happen if we eliminated it.   Very tricky C++ questions related to philosophy and practicality.  
  
b) Have you run all the tests of the serialization library with your changes?  It's kind a big job so lots of people don't do it.  But a change like this could have fallout in surprising places so they have to be run.  (Sometimes even that isn't enough!).  Fortunately now we have CI so if you post a PR, The whole serialization test suite will get run.  If there are failures they will be very interesting to see.  But you'll have to comb through the data log to see them.  It's a pain.   
  
So post a PR and let's see what happens.  
  
Very good effort in chasing this down ... and proposing a fix (with C++ syntax that almost no one understands).  Congratulations on your awesome C++ skills.

---

## Comment 2

> Username: kevle  
> Created at: 2019-10-15 20:52:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/176#issuecomment-542399693  

Issue can be resolved in user code by changing the function signature.
