# #180 - Feature Request: unordered_flat_map should support transparent operator[]&Co. [Closed]

> Username: J-Richter  
> Created at: 2023-01-27 16:31:42 UTC  
> Updated at: 2023-02-28 22:34:18 UTC  
> Closed at: 2023-02-28 22:34:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/180  

unordered_flat_map<string,string> m;  
    string_view key;  
    m[key] = "";  // does not work  
    m.insert_or_assign( key, "" );  // does not work  
  
I'd like to replace our usage of absl::flat_hash_map with boost::unordered_flat_map.   
But this missing functions cannot be simulated without performance losses.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-01-27 19:50:24 UTC  
> Url: https://github.com/boostorg/unordered/issues/180#issuecomment-1407003591  

This missing overload was described in P2363 here: https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2363r4.html  
  
We've implemented this here: https://github.com/boostorg/unordered/pull/177  
  
This was, unfortunately, done after the 1.81 release but the code is on the develop/master branch. So, if you can't wait until 1.82, you can safely use the code from develop or master in the interim.

---

## Comment 2

> Username: cmazakas  
> Created at: 2023-02-28 22:34:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/180#issuecomment-1449031021  

This is coming in 1.82. Going to mark as closed now.
