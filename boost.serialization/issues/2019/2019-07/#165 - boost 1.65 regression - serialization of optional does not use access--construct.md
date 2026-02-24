# #165 - boost 1.65 regression - serialization of optional does not use access::construct [Open]

> Username: cosurgi  
> Created at: 2019-07-24 16:55:08 UTC  
> Updated at: 2019-08-02 14:52:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/165  

The `friend class boost::serialization::access;` is ignored during serialization of optional. The private default constructor is not used. Following code shows the problem directly:  
  
```  
// g++ -o serialize_optional serialize_optional.cpp -lboost_serialization  
  
#define BOOST_OPTIONAL_CONSTRUCTOR_ACCESS  
  
#include <fstream>  
#include <iostream>  
#include <boost/serialization/nvp.hpp>  
#include <boost/serialization/serialization.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/serialization/access.hpp>  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/serialization/optional.hpp>  
#include <boost/optional.hpp>  
  
class Foo {  
	public:  
		Foo(int k) : i(k) {}  
		friend std::ostream& operator<<(std::ostream& os, Foo const& foo) {  
			return os << "Foo { " << foo.i << " }";  
		}  
	private:  
		Foo() : i(-1) {};  
		friend class boost::serialization::access;  
  
		template<class Archive>  
		void serialize(Archive & ar, const unsigned int){  
			#define N(a) BOOST_SERIALIZATION_NVP(a)  
			ar & N(i);  
			#undef N  
		};  
		int i;  
};  
  
int main() {  
	using Data = boost::optional<Foo>;  
	std::cout << "Boost version: " << BOOST_VERSION << "\n";  
	{  
		auto f = Data(2345);  
		std::ofstream os("test.xml");  
		boost::archive::xml_oarchive oa(os);  
		oa << boost::serialization::make_nvp("f", f);  
	}  
	{  
		Data g;  
		std::ifstream is("test.xml");  
		boost::archive::xml_iarchive ia(is);  
		ia >> boost::serialization::make_nvp("f", g);  
		  
		std::cout << g.get() << "\n";  
	}  
}  
```

---

## Comment 1

> Username: cosurgi  
> Created at: 2019-07-24 16:59:45 UTC  
> Updated at: 2019-07-24 17:01:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/165#issuecomment-514715568  

I have a sizable chunk of code that relied on this capability: plenty of classes with private default constructors, which are used inside boost::optional. After upgrading to newer boost version I've hit this regression and I had to fix it. Changing my code would destroy the design and introduce security problems.  
  
See my pull request: https://github.com/boostorg/serialization/pull/166

---

## Comment 2

> Username: cosurgi  
> Created at: 2019-08-01 10:20:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/165#issuecomment-517223675  

#166 closed because I'm working on a better solution.

---

## Comment 3

> Username: cosurgi  
> Created at: 2019-08-02 14:52:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/165#issuecomment-517730452  

Sorry, the better solution did not work. Back to square one. And little time left. I think I will have to use a workaround bool to know if class was properly constructed either by loading it from file or by calling a constructor with parameters. But then I no more need to keep it inside a boost::optional<> since I do all the work myself.  
  
I wish I could still use a private default constructor.
