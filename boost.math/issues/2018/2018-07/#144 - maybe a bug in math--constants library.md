# #144 - maybe a bug in math::constants library [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 18:02:39 UTC  
> Updated at: 2018-08-18 18:29:44 UTC  
> Closed at: 2018-08-18 18:29:44 UTC  
> Url: https://github.com/boostorg/math/issues/144  

The following templated code will generate incorrect result in visual studio community 2017:  
```  
#include<iostream>  
#include<boost/math/constants/constants.hpp>  
  
// convienent defination of math constants  
template<typename T> const T pi = boost::math::constants::pi<T>(); // PI  
template<typename T> const T two_pi = boost::math::constants::two_pi<T>(); // 2*PI  
  
// physical constants  
template<typename T> const T mu_0 = 4.0*pi<T>*1.0e-4; // permeability of free space in H / km  
int main()  
{  
	using T = double;  
	auto w = two_pi<T> * 0.01;  
	//auto t1 = pi<T>;  
	auto t2 = mu_0<T>;  
	std::cout<<"omega="<<w<<std::endl;  
	std::cout<<"mu_0="<<t2<<std::endl;  
	system("pause");  
  
	return 0;  
}  
```  
In VS2017, mu_0 will be 0.000. However, if I uncomment  
```  
auto t1 = pi<T>  
```  
in main, the result is correct. This problem does not happen when compiled with gcc 8.1 in Manjaro Linux. The boost library version is 1.67.0  
  
Moved from https://svn.boost.org/trac10/ticket/13591

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-18 18:29:44 UTC  
> Url: https://github.com/boostorg/math/issues/144#issuecomment-414077666  

Reduced to the non-boost code:  
  
```  
template <class T>  
T get_pi()  
{  
   return 3.14;  
}  
template <class T>  
T get_2_pi()  
{  
   return 6.28;  
}  
  
template<typename T> const T pi = get_pi<T>(); // PI  
template<typename T> const T two_pi = get_2_pi<T>(); // 2*PI  
  
                                                                           // physical constants  
template<typename T> const T mu_0 = 4.0*pi<T>*1.0e-4;  
  
int main()  
{  
   using T = double;  
   auto w = two_pi<T> * 0.01;  
   //auto t1 = pi<T>;   // uncomment this line and everything works OK.  
   auto t2 = mu_0<T>;  
   std::cout << "omega=" << w << std::endl;  
   std::cout << "mu_0=" << t2 << std::endl;  
   system("pause");  
  
   return 0;  
}  
```  
So not our issue.  Looks like an order-of-initialization issue to me.
