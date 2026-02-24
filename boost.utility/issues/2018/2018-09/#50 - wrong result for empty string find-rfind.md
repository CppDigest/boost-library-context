# #50 - wrong result for empty string find/rfind [Closed]

> Username: reluctantbugreporter  
> Created at: 2018-09-14 16:10:29 UTC  
> Updated at: 2020-11-30 05:48:41 UTC  
> Closed at: 2020-11-30 05:48:41 UTC  
> Url: https://github.com/boostorg/utility/issues/50  

```cpp  
 {  
    using string = std::string;  
    using string_view = boost::string_ref;  
    string es1;  
    string es2;  
    string_view ev1{es1};  
    string_view ev2{es2};  
    {  
      const auto rs = es1.find(es2);  
      const auto rv = ev1.find(ev2);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = es1.rfind(es2);  
      const auto rv = ev1.rfind(ev2);  
      assert(rs == rv);  
    }  
}  
```

---

## Comment 1

> Username: reluctantbugreporter  
> Created at: 2018-09-14 16:16:13 UTC  
> Url: https://github.com/boostorg/utility/issues/50#issuecomment-421409162  

Missing functions:  
```cpp  
    string s10{"0123456789"};  
    string_view v10{s10};  
    string s456789{"456789"};  
    string_view v456789{s456789};  
    {  
      const auto rs = s10.find_last_not_of(s456789, 2);  
      const auto rv = v10.find_last_not_of(v456789, 2);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.rfind("", 9);  
      const auto rv = string_view{"0123456789"}.rfind("", 9);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"01234567890123456789"}.rfind("", 19);  
      const auto rv = string_view{"01234567890123456789"}.rfind("", 19);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.rfind("89", 8);  
      const auto rv = string_view{"0123456789"}.rfind("89", 8);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.rfind("", 9);  
      const auto rv = string_view{"0123456789"}.rfind("", 9);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"01234567890123456789"}.rfind("", 19);  
      const auto rv = string_view{"01234567890123456789"}.rfind("", 19);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.rfind("89", 8);  
      const auto rv = string_view{"0123456789"}.rfind("89", 8);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.find_last_not_of("", 5);  
      const auto rv = string_view{"0123456789"}.find_last_not_of("", 5);  
      assert(rs == rv);  
    }  
    {  
      const auto rs = string{"0123456789"}.find_last_of("123456789", 1);  
      const auto rv = string_view{"0123456789"}.find_last_of("123456789", 1);  
      assert(rs == rv);  
    }  
```

---

## Comment 2

> Username: mclow  
> Created at: 2018-09-14 16:48:34 UTC  
> Url: https://github.com/boostorg/utility/issues/50#issuecomment-421418286  

Best to use `boost::string_view` instead. It has all those functions.  
The `find`/`rfind` thing is fixed in commit db05c11
