# #127 - Algorithm functions cannot be used with CTypedPtrArray of MFC. [Closed]

> Username: K-Magara  
> Created at: 2021-05-11 00:30:34 UTC  
> Updated at: 2021-05-17 06:38:20 UTC  
> Closed at: 2021-05-17 06:38:20 UTC  
> Url: https://github.com/boostorg/range/issues/127  

If you use an algorithm function with CTypedPtrArray, you will get a compile error where the template arguments do not match with std::swap().  
I used reverse() as an example, but the same was true for sort() and so on.  
The following sample code is a console application + MFC, and unnecessary initialization is omitted.  
Boost 1.76, Tried with Visual Studio 2015 and 2019  
```  
#include "boost/foreach.hpp"  
#include "boost/range/mfc.hpp"  
#include "boost/range/algorithm.hpp"  
  
class CMyClass  
{  
	int	_m;  
public:  
	CMyClass(int m):_m(m) {}  
	int	GetType(void) {  
		return _m;  
	}  
};  
  
int main()  
{  
	CTypedPtrArray<CPtrArray, CMyClass*>	ar;  
	CMyClass*	p;  
	for ( int i=0; i<3; i++ ) {  
		p = new CMyClass(i);  
		ar.Add(p);  
	}  
	BOOST_FOREACH(p, ar) {  
		printf("%d\n", p->GetType());  
	}  
	boost::reverse(ar);     // Error with std::swap()  
	BOOST_FOREACH(p, ar) {  
		printf("%d\n", p->GetType());  
		delete	p;  
	}  
}  
```  
It works fine with CPtrArray instead of CTypedPtrArray.  
```  
	CPtrArray	ar;  
	CMyClass*	p;  
	for ( int i=0; i<3; i++ ) {  
		p = new CMyClass(i);  
		ar.Add(p);  
	}  
	BOOST_FOREACH(auto pp, ar) {  
		printf("%d\n", ((CMyClass*)pp)->GetType());  
	}  
	boost::reverse(ar);     // ok !!  
	BOOST_FOREACH(auto pp, ar) {  
		printf("%d\n", ((CMyClass*)pp)->GetType());  
		delete	pp;  
	}  
```  
I suspect something is missing in the CTypedPtrArray definition in mfc.hpp, but I didn't know what it was.

---

## Comment 1

> Username: K-Magara  
> Created at: 2021-05-17 06:38:20 UTC  
> Url: https://github.com/boostorg/range/issues/127#issuecomment-842044002  

I got in touch with the author.  
It turned out to be impossible, so I will close it.
