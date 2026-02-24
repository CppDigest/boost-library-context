# #177 Use explicit calls to overloaded operator>> and operator<< in archive interfaces [Closed]

> Username: kevle  
> Created at: 2019-10-10 16:42:39 UTC  
> Updated at: 2019-10-15 20:49:41 UTC  
> Closed at: 2019-10-15 20:49:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/177  

Tentative fix for issue #176 .

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-10-10 18:00:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-540702543  

Looks like your tests all pass.  The one failure on one windows platform is a test artifact that so far no one has been able to resolve.  The successful passing of your change suggests that it is a good one.  Also we should add a test "test_overload .. to the test suite.  I always try to do this in order to avoid the "whack-a-mole" syndrome and guarantee that the quality of the library is monotonically increasing.  I'm still not understanding your change and will have spend some more time on it.  Looks to me that nailing this down and conjuring up a fix must have cost some significant effort.  I'll try to see that this effort is not in vane.

---

## Comment 2

> Username: kevle  
> Created_at: 2019-10-11 07:29:55 UTC  
> Updated_at: 2019-10-11 07:30:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-540949783  

>  Also we should add a test "test_overload .. to the test suite  
  
Yes, this seems necessary. It could also be a compilation only test. I have not quite figured out how to integrate a test and the boost build process quirks. Also the current version of my small example only utilizes non-intrusive routines.  
  
Here is the current version that can surely be reduced even further:  
```  
  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/serialization/serialization.hpp>  
#include <boost/serialization/split_free.hpp>  
  
#include <sstream>  
#include <iostream>  
  
struct CustomTypeSplit  
{  
	int i;  
};  
struct CustomType  
{  
	int i;  
};  
  
struct ContainerType  
{  
	CustomType t;  
	CustomTypeSplit s;  
};  
  
template<typename Stream>  
Stream& operator>>(Stream& is, CustomTypeSplit& x) {  
	std::cout << "operator>> Split\n";  
	return is;  
}  
template<typename Stream>  
Stream& operator<<(Stream& os, const CustomTypeSplit& x) {  
	std::cout << "operator<< Split\n";  
	return os;  
}  
  
template<typename Stream>   
Stream& operator>>(Stream& is, CustomType& x) {  
	std::cout << "operator>>\n";  
	return is;  
}  
template<typename Stream>   
Stream& operator<<(Stream& os, const CustomType& x) {  
	std::cout << "operator<<\n";  
	return os;  
}  
  
namespace boost {  
	namespace serialization {  
		template<class Archive>  
		void serialize(Archive& ar, CustomType& c, const unsigned int)  
		{  
			ar& c.i;  
		}  
		template<class Archive>  
		void load(Archive& ar, CustomTypeSplit& c, const unsigned int)  
		{  
			ar >> c.i;  
		}  
		template<class Archive>  
		void save(Archive& ar, const CustomTypeSplit& c, const unsigned int)  
		{  
			ar << c.i;  
		}  
		template<class Archive>  
		void serialize(Archive& ar, ContainerType& c, const unsigned int)  
		{  
			ar& c.t;  
			ar& c.s;  
		}  
	}  
}  
  
int main(int argc, char* argv[])  
{  
	ContainerType store{11, 22};  
    std::stringstream out;  
	boost::archive::binary_oarchive oa(out);  
    oa << store;  
  
	ContainerType load;  
	std::stringstream in(out.str());  
	boost::archive::binary_iarchive ia(in);  
	ia >> load;  
}  
  
BOOST_SERIALIZATION_SPLIT_FREE(CustomTypeSplit)  
```  
  
I have tried the proposed changes using the split save / load routines that directly use the chevron operators using MSVC 19.22. For some reason, inside those the correct member overload gets chosen. It's possibly related to some ADL rules that I am not familiar with.  
  
The design rationale states:  
> We don't want users to insert/extract data directly into/from  the stream .   
  
Therefore I think relying on custom overloads to access the archives directly is not idiomatic usage of the serialization library.  
  
Thanks for the assistance in getting this resolved.

---

## Comment 3

> Username: kevle  
> Created_at: 2019-10-14 10:04:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-541590733  

The compilation fails since the operator overload from global scope is chosen in the test.  
So the current goal seems to be to either make the compilation succeed ( so that global overloads will not be considered at all ) or to cause a compilation error due to ambiguity, which would be my preferred solution.  
  
As far as I can tell there are multiple ways to force ambiguity.  
  
Method 1:   
  
Add something like  
  
`using detail::interface_iarchive<binary_iarchive>::operator>>;`  
  
to the most specialised archive classes. This results on the member overload and global operator having a single parameter that needs to be deducted and therefore fail the compilation attempt.  
  
Method 2:  
  
Add  
  
```  
	template<class Archive, class T>  
	Archive& operator>>(detail::interface_iarchive<Archive>& a, T& t) {  
		return a.operator>>(t);  
	}  
```  
  
inside namespace boost::archive and advise users to use  
  
`using boost::archive::operator>>;`  
  
should they want to be clear of unwanted overloads. I am not too happy with either of these solutions.  
  
Looking into the way cereal handles these situations might help.

---

## Comment 4

> Username: robertramey  
> Created_at: 2019-10-14 16:06:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-541768783  

Honestly I'm not seeing the problem.  
  
boost::archive::binary_oarchive oba;  
oba << t - finds the proper function - what's the problem?

---

## Comment 5

> Username: kevle  
> Created_at: 2019-10-15 07:00:17 UTC  
> Updated_at: 2019-10-15 07:04:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-542068967  

If either of operator<< / operator>> is overloaded by the user to accept an arbitrary type for the left hand side, it might be used in serialization instead of the appropriate serialize / load / save routines.  
  
An example of such a user overloaded operator is shown here:  
https://github.com/kevle/serialization/blob/explicit_chevrons/test/test_overloaded_operators.cpp#L35  
  
If this behavior is intended there is in fact no problem. I just stumbled across this behavior because of silently corrupted archives that have been caused by some overloads that have not been suitably implemented to work correctly in conjunction with boost::archive's interfaces and wanted to prevent this from happening in the future.

---

## Comment 6

> Username: robertramey  
> Created_at: 2019-10-15 16:07:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-542288326  

This test shows that ar << t eventually resolving to serialize(Archive &ar, t).  I'm sorry but I don't see that this is wrong.  
  
Your original example showed ar << t resolving to ostream & operator<<(ostream &os, t) which such an operator was declared.  I presumed that your report was correct, I didn't verify it with my own test. I agreed that this is a problem.  But it seems to me that your "test_overload" doesn't replicate the original circumstance and doesn't show any problem.  
  
The look up rules for overloading are not trivial to understand in detail.  They depend upon with the function signatures are templates, whether the types are primitive or not not, what namespaces the types may be in etc.  Also the fact that no one has reported this 15 years makes me think that this is not a trivial thing to actually understand.  If I can find some time, I may take your original test and reduce it a minimum to see if I can reproduce it.

---

## Comment 7

> Username: kevle  
> Created_at: 2019-10-15 20:49:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/177#issuecomment-542398480  

std::ostream overloads will not interfere, no worries there.   
Since this problem has not shown up elsewhere I think I can safely close this PR.

---
