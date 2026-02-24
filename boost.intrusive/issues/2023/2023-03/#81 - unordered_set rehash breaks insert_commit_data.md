# #81 - unordered_set rehash breaks insert_commit_data [Closed]

> Username: paul203948  
> Created at: 2023-03-10 12:52:12 UTC  
> Updated at: 2024-03-23 17:10:49 UTC  
> Closed at: 2024-03-23 17:05:57 UTC  
> Url: https://github.com/boostorg/intrusive/issues/81  

Since boost 1.80.0 there is an issue performing insert_check, rehash and then insert_commit. The bucket_idx in insert_commit_data can become invalid. This was changed in commit 3c5c8cec3f0356a028a4b56ba6cac2256340dab1.  
  
The [documentation](https://www.boost.org/doc/libs/1_80_0/doc/html/boost/intrusive/unordered_set.html#idm61494-bb) explicitly states that this is allowed:  
  
> After a successful rehashing insert_commit_data remains valid.  
  
This worked in Boost 1.79.0.  
  
Example program:  
  
```cpp  
#include <boost/intrusive/unordered_set.hpp>  
  
struct MyClass : boost::intrusive::unordered_set_base_hook<>  
{  
    int n_;  
  
    MyClass(int n) : n_(n) { }  
  
    friend bool operator==(const MyClass &a, const MyClass &b)  
        { return a.n_ == b.n_; }  
    friend std::size_t hash_value(const MyClass &value)  
        { return std::size_t(value.n_); }  
};  
  
int main()  
{  
    using Set = boost::intrusive::unordered_set<MyClass>;  
  
    MyClass value(101);  
    Set::bucket_type buckets100[100];  
    Set::bucket_type buckets200[200];  
  
    Set set(Set::bucket_traits(buckets100, 100));  
  
    Set::insert_commit_data commitData;  
    set.insert_check(value, commitData);  
  
    set.rehash(Set::bucket_traits(buckets200, 200));  
  
    set.insert_commit(value, commitData);  
  
    assert(1 == set.count(101));  
  
    return 0;  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-03-23 17:10:48 UTC  
> Url: https://github.com/boostorg/intrusive/issues/81#issuecomment-2016550353  

This was clearly a regression. To support the faster commit version, a new operation that does not support commit after rehashing is added `insert_fast_commit()`, and insert_commit is again compatible with rehashing. Many thanks for the report.
