# #1621 - flat_buffer copy constructor error after a call to flat_buffer::consume [Closed]

> Username: grenadium  
> Created at: 2019-05-27 15:11:28 UTC  
> Updated at: 2019-05-30 18:29:22 UTC  
> Closed at: 2019-05-30 18:29:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1621  

When compiling with beast from the boost 1.70 distribution, I noted that the copy constructor of `flat_buffer` does not correctly copies a buffer that had some bytes consumed.  
  
After looking at the source code it seems that the error comes from the `flat_buffer::copy_from` method: `std::memcpy(begin_, other.begin_, n);` should probably be `std::memcpy(begin_, other.in_, n);`  
  
### Version of Beast  
Version 248 (included in boost 1.70)  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <iostream>  
#include <string>  
#include <string_view>  
  
#include <boost/beast/core/flat_buffer.hpp>  
  
int main()  
{  
	using namespace std;  
	using namespace boost::beast;  
  
	const string test_content = "test";  
  
	flat_buffer buf1;  
	buf1.commit(  
		buffer_copy(  
			buf1.prepare(test_content.size()),  
			boost::asio::buffer(test_content)));  
  
	buf1.consume(1);  
  
	flat_buffer buf2 = buf1;  
  
	const auto buf1_bytes = buf1.cdata();  
	const auto buf2_bytes = buf2.cdata();  
  
	const string_view buf1_sv{  
		static_cast<const char*>(buf1_bytes.data()),  
		buf1_bytes.size()};  
  
	const string_view buf2_sv{  
		static_cast<const char*>(buf2_bytes.data()),  
		buf2_bytes.size()};  
  
	cout << "buf1 size     : " << buf1.size() << "\n"  
	     << "buf1 contents : " << buf1_sv << "\n"  
	     << "buf2 size     : " << buf2.size() << "\n"  
	     << "buf2 contents : " << buf2_sv << "\n";  
  
	return 0;  
}  
```  
prints:  
```  
buf1 size     : 3  
buf1 contents : est  
buf2 size     : 3  
buf2 contents : tes  
```  
  
### All relevant compiler information  
  
Tested with MSVC 2019.1 and GCC 8.3.0 (linux x64)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-27 15:18:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1621#issuecomment-496244977  

Yes it looks like you are correct here, investigating...
