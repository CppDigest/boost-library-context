# #273 - Archive iterator to use algorithms to populate archives [Closed]

> Username: correaa  
> Created at: 2022-11-28 00:48:26 UTC  
> Updated at: 2023-10-23 17:28:10 UTC  
> Closed at: 2023-10-23 17:28:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/273  

I wonder if something like this exists already, the analogue to `std::ostream_iterator` but for Archives.  
  
I find myself doing this often:  
  
```cpp  
	package_oarchive poa(p);  
	while(count--) { poa << *first++; }  
```  
  
with a _hypothetical_ `boost::serialization::oarchive_iterator` I could use a proper algorithm for this.  
  
```cpp  
	package_oarchive poa(p);  
	std::copy_n(first, count, boost::serialization::oarchive_iterator<>(poa));  
```  
  
Please let me know if it exists already, or if someone else ever needed something like this.  
  
Ideally, I wish I could reuse `std::ostream_iterator` for this, but its constructors only accept things derived from `std::ostream&` and not Boost Serialization Archives.  
  
For what it is worth I propose adding it to the library.  
The implementation is relatively easy to layout, but there are a lot of design details to discuss.

---

## Comment 1

> Username: robertramey  
> Created at: 2023-08-24 18:53:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/273#issuecomment-1692242268  

I would think it would be almost trivial to implement.  (Famous last words!)  If you want to do this then ask me to include it in the library I will look at it.  A couple of notes:  
  
a) if you're going to to oarchive_iterator, you should do archive_iterator as well.  
b) it should work for all archives  
c) needs a test  
d) needs an update to the documentation.  
  
So even a "trivial" entails a lot of work.  If I can accept I will.  You'll get full credit in the appropriate places.  Might be a nice addition to your resume
