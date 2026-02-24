# #42 - Automatic Conversion to JSON with base struct [Closed]

> Username: kilasuelika  
> Created at: 2023-09-12 05:41:22 UTC  
> Updated at: 2023-09-12 06:17:29 UTC  
> Closed at: 2023-09-12 06:17:29 UTC  
> Url: https://github.com/boostorg/describe/issues/42  

The following code is mostly copied from documentation [Automatic Conversion to JSON](https://www.boost.org/doc/libs/master/libs/describe/doc/html/describe.html#example_to_jsonl)  
```  
#include <iostream>  
#include <vector>  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/json.hpp>  
#include <boost/utility/string_view.hpp>  
namespace NS  
{  
    struct A  
    {  
        int x = 0;  
        std::string s;  
    };  
    BOOST_DESCRIBE_STRUCT(A, (), (x, s));  
  
    struct C: A  
    {  
        int y = 0;  
    };  
    BOOST_DESCRIBE_STRUCT(C, (A), (y));  
  
    template<class T,  
        class D1 = boost::describe::describe_members<T,  
        boost::describe::mod_public | boost::describe::mod_protected>,  
        class D2 = boost::describe::describe_members<T, boost::describe::mod_private>,  
        class En = std::enable_if_t<boost::mp11::mp_empty<D2>::value && !std::is_union<T>::value> >  
    void tag_invoke(boost::json::value_from_tag const&, boost::json::value& v, T const& t)  
    {  
        auto& obj = v.emplace_object();  
  
        boost::mp11::mp_for_each<D1>([&](auto D) {  
  
            obj[D.name] = boost::json::value_from(t.*D.pointer);  
  
            });  
    }  
  
    template<class T> void extract(boost::json::object const& obj, char const* name, T& value)  
    {  
        value = boost::json::value_to<T>(obj.at(name));  
    }  
  
    template<class T,  
        class D1 = boost::describe::describe_members<T,  
        boost::describe::mod_public | boost::describe::mod_protected>,  
        class D2 = boost::describe::describe_members<T, boost::describe::mod_private>,  
        class En = std::enable_if_t<boost::mp11::mp_empty<D2>::value && !std::is_union<T>::value> >  
    T tag_invoke(boost::json::value_to_tag<T> const&, boost::json::value const& v)  
    {  
        auto const& obj = v.as_object();  
  
        T t{};  
  
        boost::mp11::mp_for_each<D1>([&](auto D) {  
  
            extract(obj, D.name, t.*D.pointer);  
  
            });  
  
        return t;  
    }  
}  
  
namespace NS1  
{  
    using namespace NS;  
  
    void fun()  
    {  
        C c;  
        auto jv2 = boost::json::value_from(c);  
        jv2.as_object()["x"] = 5;  
        auto c1 = boost::json::value_to<C>(jv2);  
  
        std::cout << jv2 << std::endl;  
        std::cout<<c1.x << std::endl;  
    }  
}  
int main()  
{  
    NS1::fun();  
}  
```  
  
But it seems `value_from` and `value_to` will ignore base class. So manually set "x" to 5 is useless on value `c1`.

---

## Comment 1

> Username: kilasuelika  
> Created at: 2023-09-12 06:17:26 UTC  
> Url: https://github.com/boostorg/describe/issues/42#issuecomment-1715055596  

I work out a solution by mimicking example of serialization:  
```  
#include <iostream>  
#include <vector>  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/json.hpp>  
#include <boost/utility/string_view.hpp>  
namespace NS  
{  
	struct A  
	{  
		int x = 0;  
		std::string s;  
	};  
	BOOST_DESCRIBE_STRUCT(A, (), (x, s));  
  
	struct C : A  
	{  
		int y = 0;  
	};  
	BOOST_DESCRIBE_STRUCT(C, (A), (y));  
  
	void merge(boost::json::object& dest, const boost::json::object& src)  
	{  
		for (auto it = src.begin(); it != src.end(); ++it)  
		{  
			dest.insert_or_assign(it->key(), it->value());  
		}  
	}  
  
  
	template<class T,  
		class D1 = boost::describe::describe_bases<T, boost::describe::mod_public>,  
		class D2 = boost::describe::describe_bases<T,  
		boost::describe::mod_protected | boost::describe::mod_private>,  
		class D3 = boost::describe::describe_members<T,  
		boost::describe::mod_public | boost::describe::mod_protected>,  
		class D4 = boost::describe::describe_members<T, boost::describe::mod_private>,  
		class En = std::enable_if_t< boost::mp11::mp_empty<D2>::value&&  
		boost::mp11::mp_empty<D4>::value && !std::is_union<T>::value> >  
	void tag_invoke(boost::json::value_from_tag const&, boost::json::value& v, T const& t)  
	{  
		auto& obj = v.emplace_object();  
  
		//base  
		boost::mp11::mp_for_each<D1>([&](auto D) {  
			using B = typename decltype(D)::type;  
			const B& base = t;  
			merge(obj, boost::json::value_from(base).as_object());  
			});  
		//member  
		boost::mp11::mp_for_each<D3>([&](auto D) {  
			obj[D.name] = boost::json::value_from(t.*D.pointer);  
			});  
	}  
  
	template<class T> void extract(boost::json::object const& obj, char const* name, T& value)  
	{  
		value = boost::json::value_to<T>(obj.at(name));  
	}  
  
	template<class T,  
		class D1 = boost::describe::describe_bases<T, boost::describe::mod_public>,  
		class D2 = boost::describe::describe_bases<T,  
		boost::describe::mod_protected | boost::describe::mod_private>,  
		class D3 = boost::describe::describe_members<T,  
		boost::describe::mod_public | boost::describe::mod_protected>,  
		class D4 = boost::describe::describe_members<T, boost::describe::mod_private>,  
		class En = std::enable_if_t< boost::mp11::mp_empty<D2>::value&&  
		boost::mp11::mp_empty<D4>::value && !std::is_union<T>::value> >  
	T tag_invoke(boost::json::value_to_tag<T> const&, boost::json::value const& v)  
	{  
		auto const& obj = v.as_object();  
  
		T t{};  
  
		boost::mp11::mp_for_each<D1>([&](auto D) {  
			using B = typename decltype(D)::type;  
			B& base = t;  
			base = boost::json::value_to<B>(v);  
			});  
  
		boost::mp11::mp_for_each<D3>([&](auto D) {  
			extract(obj, D.name, t.*D.pointer);  
			});  
  
		return t;  
	}  
  
}  
  
namespace NS1  
{  
	using namespace NS;  
  
	void fun()  
	{  
		C c;  
		auto jv = boost::json::value_from(c);  
		std::cout << jv << std::endl;  
  
		jv.as_object()["x"] = 99;  
		std::cout << jv << std::endl;  
		auto c1 = boost::json::value_to<C>(jv);  
  
		auto jv1 = boost::json::value_from(c1);  
		std::cout << jv1 << std::endl;  
	}  
}  
int main()  
{  
	NS1::fun();  
}  
```  
Thank developers of `boost.describe` and `boost.json`.
