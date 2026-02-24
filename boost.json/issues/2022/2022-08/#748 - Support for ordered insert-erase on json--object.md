# #748 - Support for ordered insert/erase on json::object [Open]

> Username: gummif  
> Created at: 2022-08-28 14:10:24 UTC  
> Updated at: 2022-08-28 14:32:28 UTC  
> Url: https://github.com/boostorg/json/issues/748  

I have a json that needs to be manipulated and maintaining the order of the keys in the object is really imported (even though it is supposed to be unordered key-value map).  
  
Currently insertions are ordered so creating a json::object and inserting at the end will preserve the order. A work around I have now is something like this:  
```c++  
template<class K, class P>  
void ordered_insert_before(boost::json::object& o, K&& key, P&& p)  
{  
	auto x = boost::json::object(o.storage());  
	x.reserve(o.size() + 1);  
	bool added = false;  
	for (auto&& [k, v] : o)  
	{  
		if (k == key && !added)  
		{  
			x.insert(std::forward<P>(p));  
			added = true;  
		}  
		x.insert(boost::json::object::value_type{std::move(k), std::move(v)});  
	}  
	if (!added)  
	{  
		x.insert(std::forward<P>(p));  
	}  
	o.swap(x);  
}  
  
template<class K>  
void ordered_erase(boost::json::object& o, K&& key)  
{  
	auto x = boost::json::object(o.storage());  
	x.reserve(o.size());  
	for (auto&& [k, v] : o)  
	{  
		if (k != key)  
		{  
			x.insert(boost::json::object::value_type{std::move(k), std::move(v)});  
		}  
	}  
	o.swap(x);  
}  
```  
  
There are of course O(N). Is there interest for adding these as member functions or free functions with stronger ordering guarantees but worse O(N) complexity + allocations?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-28 14:32:28 UTC  
> Url: https://github.com/boostorg/json/issues/748#issuecomment-1229473058  

In order to `memmove` the elements in the `object` correctly, the implementation would have to calculate the hash of each moved element, and adjust its index in the hash table.
