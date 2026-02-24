# #256 - Random Crash [Closed]

> Username: Dan-J-D  
> Created at: 2020-07-04 23:22:48 UTC  
> Updated at: 2020-12-26 16:30:35 UTC  
> Closed at: 2020-12-26 16:30:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/256  

```cpp  
uint2048_t& GenerateRandomNumber2048()  
{  
	srand(time(NULL));  
  
	uint2048_t num = 1;  
	std::vector<uint8_t> data = std::vector<uint8_t>(2048);  
	boost::multiprecision::export_bits(num, std::back_inserter(data), 2048);  
	for (uint16_t i = 0; i < data.size(); i++)  
	{  
		data[i] = rand() % 255;  
	}  
	boost::multiprecision::import_bits(num, data.begin(), data.end());  
	return num;  
}  
  
int main()  
{  
	uint2048_t num = GenerateRandomNumber2048();  
	std::cout << num;  
}  
```  
  
that doesnt work, it is crashing on std::cout << num;  
Access violation writing location 0x00C80000  
  
```cpp  
int main()  
{  
	srand(time(NULL));  
  
	uint2048_t num = 1;  
	std::vector<uint8_t> data = std::vector<uint8_t>(2048);  
	boost::multiprecision::export_bits(num, std::back_inserter(data), 2048);  
	for (uint16_t i = 0; i < data.size(); i++)  
	{  
		data[i] = rand() % 255;  
	}  
	boost::multiprecision::import_bits(num, data.begin(), data.end());  
	std::cout << num;  
}  
```  
  
this does work, btw i made a custom uint2048_t type

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-07-05 00:43:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/256#issuecomment-653827611  

Can you reproduce using only `boost::multiprecision`?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-05 08:31:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/256#issuecomment-653858083  

I tried this:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
typedef boost::multiprecision::number<boost::multiprecision::cpp_int_backend<2048, 2048, boost::multiprecision::unsigned_magnitude>> uint2048_t;  
  
uint2048_t& GenerateRandomNumber2048()  
{  
	srand(time(NULL));  
  
	uint2048_t num = 1;  
	std::vector<uint8_t> data = std::vector<uint8_t>(2048);  
	boost::multiprecision::export_bits(num, std::back_inserter(data), 2048);  
	for (uint16_t i = 0; i < data.size(); i++)  
	{  
		data[i] = rand() % 255;  
	}  
	boost::multiprecision::import_bits(num, data.begin(), data.end());  
	return num;  
}  
  
int main()  
{  
	for (;;)  
	{  
		uint2048_t num = GenerateRandomNumber2048();  
		std::cout << num;  
	}  
}  
```  
  
and left it running for a while but no crashes or assertions triggered.  I suspect we need the actual values in the array before `import_bits` is called.

---

## Comment 3

> Username: Dan-J-D  
> Created at: 2020-07-05 16:20:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/256#issuecomment-653908261  

I got it working with a macro because i am going to need the same function but for different sizes but here it is  
```cpp  
#define RNG(numberSize) uint##numberSize##_t GenerateRandomNumber##numberSize() { srand(time(NULL)); uint##numberSize##_t num = 0; std::vector<uint8_t> data = std::vector<uint8_t>(##numberSize); boost::multiprecision::export_bits(num, std::back_inserter(data), ##numberSize); for(uint16_t i = 0; i < data.size(); i++) data[i] = rand() % 255; boost::multiprecision::import_bits(num, data.begin(), data.end()); return num; }  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-12-26 16:30:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/256#issuecomment-751371994  

Doh!  Looking at this again I see the issue:  
  
```  
uint2048_t& GenerateRandomNumber2048();  
```  
  
returns a dangling reference.  Either return by value or make `num` static to fix.
