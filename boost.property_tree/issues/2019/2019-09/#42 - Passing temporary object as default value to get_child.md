# #42 - Passing temporary object as default value to get_child [Closed]

> Username: RewritableJ  
> Created at: 2019-09-16 15:19:56 UTC  
> Updated at: 2023-11-20 16:32:35 UTC  
> Closed at: 2023-11-20 16:32:35 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42  

Consider following example  
  
```  
#include <iostream>  
#include <sstream>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
  
int main()  
{  
	using namespace boost::property_tree;  
	  
	std::stringstream ss;  
	ss << "{\"someRandomNode\":false}";  
  
	ptree json;  
	read_json(ss, json);  
  
	const auto &node = json.get_child("otherNode", ptree{});  
	std::cout << node.size() << " " << (node.begin() == node.end()) << std::endl;  
  
	for (const auto &[k, v] : node)  
		std::cout << "Why did i get here?" << std::endl;  
  
	return 0;  
}  
```  
  
Output:  
> 0 0  
> Why did i get here?  
> Segmentation fault  
  
Obviously get_child returns broken reference leading to undefined behaviour.   
Tested with g++ (Gentoo 8.3.0-r1 p1.1) 8.3.0 and boost 1.70.0.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-17 18:37:46 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816910024  

The complication arises because get_child returns a reference to the node element; it cannot simply return the default value by value in fail cases. If we add another signature that accepts temporaries and returns by value, then it should make a copy of the child node if it succeeds. I can't see any straightforward solution for it.  
@pdimov, What do you think?

---

## Comment 2

> Username: pdimov  
> Created at: 2023-11-17 18:49:10 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816924065  

> If we add another signature that accepts temporaries and returns by value, then it should make a copy of the child node if it succeeds.  
  
That's probably what we should do, yes. In addition to  
```  
        /** Get the child at the given path, or return @p default_value. */  
        self_type &get_child(const path_type &path, self_type &default_value);  
  
        /** Get the child at the given path, or return @p default_value. */  
        const self_type &get_child(const path_type &path,  
                                   const self_type &default_value) const;  
```  
also have  
```  
        /** Get the child at the given path, or return @p default_value. */  
        self_type get_child(const path_type &path,  
                                   self_type && default_value) const;  
```  
Theoretically, we'd also need  
```  
        /** Get the child at the given path, or return @p default_value. */  
        self_type get_child(const path_type &path,  
                                   const self_type && default_value) const;  
```  
in case someone passes us a const temporary, but I doubt that anyone will notice if we omit it.  
  
I'm unclear on whether it's better to silently return by value (making a potentially expensive copy), or `= delete` these overloads.

---

## Comment 3

> Username: ashtum  
> Created at: 2023-11-17 19:10:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816947977  

> I'm unclear on whether it's better to silently return by value (making a potentially expensive copy), or `= delete` these overloads.  
  
Considering that `get_child` always needs to return an instance of `ptree`, there isn't much of a use case for it in the first place. Unlike the `get` function, which attempts to access leaf nodes and follows a usage pattern like:  
  
```C++  
auto port = settings.get("server.port", 3606);  
```  
  
The use cases for `get_child` are limited to scenarios like: https://github.com/boostorg/property_tree/blob/211c63820bfb0a430c6fe797082c4308763b0a39/examples/empty_ptree_trick.cpp#L27  
  
Considering this, I believe that deleting those overloads would be a better option.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-11-17 19:15:52 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816955165  

Let's delete it, then. (We'll only need a `const self_type&&` overload in this case, I think. It can return `void`.)

---

## Comment 5

> Username: ashtum  
> Created at: 2023-11-17 19:28:52 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816971150  

> Let's delete it, then. (We'll only need a `const self_type&&` overload in this case, I think. It can return `void`.)  
  
Do you mean a deleted overload that accepts `const self_type&&`?

---

## Comment 6

> Username: pdimov  
> Created at: 2023-11-17 19:31:00 UTC  
> Url: https://github.com/boostorg/property_tree/issues/42#issuecomment-1816973660  

Yes, adding  
```  
        void get_child(const path_type &path, const self_type &&default_value) const = delete;  
```  
should work, unless I'm missing something.
