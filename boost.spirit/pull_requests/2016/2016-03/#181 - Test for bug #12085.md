# #181 Test for bug #12085 [Closed]

> Username: hia3  
> Created at: 2016-03-27 09:26:18 UTC  
> Updated at: 2018-02-25 01:13:48 UTC  
> Closed at: 2018-02-25 01:13:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/181  

https://svn.boost.org/trac/boost/ticket/12085

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-02-24 16:28:53 UTC  
> Updated_at: 2018-02-24 16:55:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/181#issuecomment-368240095  

More simple repro:  
```cpp  
    {  
        std::string s;  
        BOOST_TEST(test_attr("ab", lexeme[char_ >> char_], s));  
        BOOST_TEST_EQ("ab", s); // OK  
        s.clear();  
        BOOST_TEST(test_attr("ab", eps >> lexeme[char_ >> char_], s));  
        BOOST_TEST_EQ("ab", s); // FAIL  
        s.clear();  
        BOOST_TEST(test_attr("ab", eps >> no_skip[char_ >> char_], s));  
        BOOST_TEST_EQ("ab", s); // FAIL  
        s.clear();  
        BOOST_TEST(test_attr("ab", eps >> no_case[char_ >> char_], s));  
        BOOST_TEST_EQ("ab", s); // FAIL  
    }  
```

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-02-25 01:13:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/181#issuecomment-368274323  

Superseded by #370

---
