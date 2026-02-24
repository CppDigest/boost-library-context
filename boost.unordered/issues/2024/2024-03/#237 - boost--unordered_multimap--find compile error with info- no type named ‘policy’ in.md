# #237 - boost::unordered_multimap::find  compile error with info: no type named ‘policy’ in ... [Closed]

> Username: 530967061  
> Created at: 2024-03-15 12:34:31 UTC  
> Updated at: 2024-03-16 06:42:50 UTC  
> Closed at: 2024-03-16 06:42:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/237  

``` C++  
  boost::unordered_multimap<std::string, std::string> test;  
  test.find("", boost::hash<std::string>(), std::equal_to<>());  // this is OK  
  ((const boost::unordered_multimap<std::string, std::string> &)m)  
      .find("", boost::hash<std::string>(), std::equal_to<>());  // can not compile  
```  
  
```  
    template <class K, class T, class H, class P, class A>  
    template <class CompatibleKey, class CompatibleHash,  
      class CompatiblePredicate>  
    typename unordered_multimap<K, T, H, P, A>::iterator  
    unordered_multimap<K, T, H, P, A>::find(CompatibleKey const& k,  
      CompatibleHash const& hash, CompatiblePredicate const& eq)  
    {  
      return table_.transparent_find(k, hash, eq);  
    }  
  
    template <class K, class T, class H, class P, class A>  
    template <class CompatibleKey, class CompatibleHash,  
      class CompatiblePredicate>  
    typename unordered_multimap<K, T, H, P, A>::const_iterator  
    unordered_multimap<K, T, H, P, A>::find(CompatibleKey const& k,  
      CompatibleHash const& hash, CompatiblePredicate const& eq) const  
    {  
      return const_iterator(  
        table_.find_node_impl(table::policy::apply_hash(hash, k), k, eq));  // use 'return table_.transparent_find(k, hash, eq);' instead?  
    }  
```
