# #87 - from_chars for boost::int128_type not work [Closed]

> Username: gpeterhoff  
> Created at: 2023-10-13 10:40:34 UTC  
> Updated at: 2023-10-16 09:19:20 UTC  
> Closed at: 2023-10-16 09:19:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/87  

Hi Matt,  
from_chars for boost::int128_type does not work if the string values are smaller/larger than min/max:  
```c++  
template <typename Type>  
void test_from_chars(const std::string_view& view)  
{  
	Type  
		v1{0}, v2{0};  
  
	std::cout << view << ":\n";  
	std::from_chars(view.data(), view.data()+view.size(), v1);  
	std::cout << "std\t" << v1 << '\n';  
	boost::charconv::from_chars(view.data(), view.data()+view.size(), v2);  
	std::cout << "boost\t" << v2 << '\n';  
	std::cout << "equal\t" << (v1==v2) << "\n\n";  
}  
  
test_from_chars<boost::int128_type>( "170141183460469231731687303715884105728");	//	max+1  
test_from_chars<boost::int128_type>("-170141183460469231731687303715884105729");	//	min-1  
```   
  
gets  
  
170141183460469231731687303715884105728:  
std	0  
boost	-170141183460469231731687303715884105728  
equal	false  
  
-170141183460469231731687303715884105729:  
std	0  
boost	170141183460469231731687303715884105727  
equal	false  
  
thx  
Gero
