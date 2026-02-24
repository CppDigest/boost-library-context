# #22 - provide an argument forwarding construct method in object_pool [Open]

> Username: octopus-prime  
> Created at: 2018-08-18 15:56:01 UTC  
> Updated at: 2019-09-19 02:48:39 UTC  
> Url: https://github.com/boostorg/pool/issues/22  

Given an actual compiler there should be a construct method in object_pool like  
  
```  
template<typename... Args>  
element_type * construct(Args&&... args);  
```  
  
See https://en.cppreference.com/w/cpp/memory/shared_ptr/make_shared

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-20 12:22:48 UTC  
> Url: https://github.com/boostorg/pool/issues/22#issuecomment-414298226  

Pull requests with unit tests are welcome. :)

---

## Comment 2

> Username: octopus-prime  
> Created at: 2018-08-26 12:24:10 UTC  
> Url: https://github.com/boostorg/pool/issues/22#issuecomment-416035259  

1)  
You still want to support C++03?  
With C++11 the construct boiler-plate could be replaced by ONE method!  
  
2)  
What kind of test do you mean? Something like  
  
```cpp  
struct foo  
{  
	int i;  
	double d;  
	std::string s;  
	foo* f;  
};  
  
void test_construct()  
{  
	boost::object_pool<foo> pool;  
	auto element = pool.construct(1, 3.14, "bar", nullptr);  
	BOOST_TEST_EQ(element->i, 1);  
	BOOST_TEST_EQ(element->d, 3.14);  
	BOOST_TEST_EQ(element->s, "bar");  
	BOOST_TEST_EQ(element->f, nullptr);  
}  
```

---

## Comment 3

> Username: jeking3  
> Created at: 2018-08-29 02:06:53 UTC  
> Url: https://github.com/boostorg/pool/issues/22#issuecomment-416799043  

Boost.Pool supports C++03 at this time, yes.

---

## Comment 4

> Username: jbherdman  
> Created at: 2019-09-19 02:48:39 UTC  
> Url: https://github.com/boostorg/pool/issues/22#issuecomment-532942394  

@jeking3 Can you have a look at my pull request to solve this issue?  Thanks.
