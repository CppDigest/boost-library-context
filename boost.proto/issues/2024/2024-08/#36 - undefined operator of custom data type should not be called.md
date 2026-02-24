# #36 - [Feature Issue] undefined operator of custom data type should not be called [Open]

> Username: fasxmut  
> Created at: 2024-08-27 09:03:20 UTC  
> Updated at: 2024-08-27 09:03:20 UTC  
> Url: https://github.com/boostorg/proto/issues/36  

```cpp  
#include <iostream>  
#include <boost/proto/proto.hpp>  
#include <vector>  
  
namespace proto = boost::proto;  
  
namespace space  
{  
  
template <int I>  
class placeholder {};  
  
template <typename data_type>  
class vector_space:  
	public proto::callable_context<space::vector_space<data_type>>  
{  
public:  
	using result_type = data_type;  
private:  
	std::vector<result_type> __data;  
public:  
	template <std::same_as<result_type> ... type_list>  
	vector_space(const type_list & ... value_list)  
	{  
		(__data.push_back(value_list), ...);  
	}  
public:  
	auto & get_ref() const  
	{  
		return __data;  
	}  
public:  
	template <int I>  
	result_type operator()(proto::tag::terminal, space::placeholder<I>) const  
	{  
		return __data[I];  
	}  
};  
  
template <typename data_type>  
class eval_class  
{  
public:  
	template <typename expr_type>  
	data_type operator()(const expr_type & expr, const data_type & d1, const data_type & d2) const  
	{  
		auto ctx = space::vector_space<data_type>{d1, d2};  
		return proto::eval(expr, ctx);  
	}  
};  
  
template <std::floating_point data_type>  
class anti_linear  
{  
private:  
	data_type __data;  
public:  
	anti_linear(): __data{0} {}  
	anti_linear(const data_type & data__):  
		__data{data__}  
	{  
	}  
public:  
	operator data_type & ()  
	{  
		return __data;  
	}  
	operator data_type () const  
	{  
		return __data;  
	}  
public:  
	auto operator+(const anti_linear & al) const  
	{  
		std::cout << "[info] operator+ #1 is called\n";  
		return anti_linear{__data + al.__data};  
	}  
	auto operator+(const data_type & dt) const  
	{  
		std::cout << "[info] operator+ #2 is called\n";  
		return anti_linear{__data + dt};  
	}  
public:  
	void print() const  
	{  
		std::cout << "__data: " << __data << std::endl;  
	}  
};  
  
}	// namespace space  
  
int main()  
{  
	{  
		const auto x0 = proto::terminal<space::placeholder<0>>::type{};  
		const auto x1 = proto::terminal<space::placeholder<1>>::type{};  
		const auto eval = space::eval_class<space::anti_linear<double>>{};  
		const auto a0 = space::anti_linear{2.1};  
		const auto a1 = space::anti_linear{-3.2};  
		std::cout << "------------------------------------------------\n";  
		{  
			eval(x0+x1, a0, a1).print();		// (1)  
		}  
		std::cout << "------------------------------------------------\n";  
		{  
			eval(x0+3.0, a0, a1).print();		// (2)  
		}  
		std::cout << "------------------------------------------------\n";  
		{  
			eval(x0*x1 + x0/2.0 + x1/3.0, a0, a1).print();		// (3)  
		}  
		std::cout << "------------------------------------------------\n";  
	}  
}  
```  
  
Output:  
  
```  
------------------------------------------------  
[info] operator+ #1 is called  
__data: -1.1  
------------------------------------------------  
[info] operator+ #2 is called  
__data: 5.1  
------------------------------------------------  
__data: -6.73667  
------------------------------------------------  
```  
  
`operator/` and `operator*` are not defined for `space::anti_linear`, it should make a compiling error, but not.
