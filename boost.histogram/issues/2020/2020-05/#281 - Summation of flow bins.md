# #281 - Summation of flow bins [Closed]

> Username: henryiii  
> Created at: 2020-05-21 15:39:01 UTC  
> Updated at: 2020-06-19 20:23:55 UTC  
> Closed at: 2020-06-19 18:17:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/281  

The latest change in develop has broken summation of flow bins. Here's a test that now fails (can be added to `histogram_operators_test.cpp`):  
  
```cpp  
 // Overflow addition  
 {  
     auto a = make(Tag(), axis::integer<int, use_default, decltype(axis::option::underflow | axis::option::overflow)>(0, 3));  
     auto b = a;  
  
     a(-5);  
     b(12);  
  
     // Passes  
     BOOST_TEST_EQ(a.at(-1), 1);  
     BOOST_TEST_EQ(b.at(3), 1);  
  
     auto c = a + b;  
     BOOST_TEST_EQ(c.at(-1), 1); /* Fail: 1 != 0 */  
     BOOST_TEST_EQ(c.at(3), 1); /* Fail: 1 != 0 */  
}  
```  
  
(I assume you will add a better, more comprehensive test, but I'm happy to make a PR if you want this exact one).

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-05-23 16:33:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/281#issuecomment-633089506  

Thanks, I am really surprised a) that this happens b) that this was not already covered by my unit tests. No need for a PR, when I fix this I am going to write a test and I write to integrate this check of flow bins into the existing tests.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-06-19 18:16:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/281#issuecomment-646802128  

This is fixed, right?

---

## Comment 3

> Username: henryiii  
> Created at: 2020-06-19 20:23:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/281#issuecomment-646847525  

Yes, perfectly, I'm already using it - sorry for forgetting to close!
