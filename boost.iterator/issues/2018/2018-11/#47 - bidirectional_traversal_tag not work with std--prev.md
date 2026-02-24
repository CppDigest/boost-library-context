# #47 - bidirectional_traversal_tag not work with std::prev [Closed]

> Username: tower120  
> Created at: 2018-11-25 21:54:04 UTC  
> Updated at: 2018-12-02 01:45:17 UTC  
> Closed at: 2018-12-01 10:16:21 UTC  
> Url: https://github.com/boostorg/iterator/issues/47  

In the following code `bidirectional_traversal_tag` does not work with `std::prev`. Iterator does not recognized as bidirirectional.  
  
```cpp  
#include <iostream>  
#include <boost/iterator/iterator_facade.hpp>  
  
class node_iterator  
	: public boost::iterator_facade  
	  <  
		node_iterator  
		, std::pair<int, int>  
		, boost::bidirectional_traversal_tag  
		, std::pair<int, int>  
	  >  
{  
public:  
	node_iterator()  
		: m_i(0) {}  
  
	explicit node_iterator(int i)  
		: m_i(i) {}  
  
private:  
	friend class boost::iterator_core_access;  
  
	void increment() { m_i++; }  
        void decrement() { m_i--; }  
  
	bool equal(node_iterator const& other) const  
	{  
		return this->m_i == other.m_i;  
	}  
  
	std::pair<int, int> dereference() const { return { m_i, m_i}; }  
public:  
	int m_i;  
};  
  
  
int main()  
{  
	auto i1  = node_iterator(1);  
	auto i10 = node_iterator(10);  
  
       std::next(i1);  
       std::prev(i1);  
	  
	std::cout <<"aaa";  
}  
  
```  
  
Online : [https://wandbox.org/permlink/T51UwajOX4Lf8aif](https://wandbox.org/permlink/T51UwajOX4Lf8aif)

---

## Comment 1

> Username: MikeGitb  
> Created at: 2018-11-26 16:55:57 UTC  
> Updated at: 2018-11-26 17:07:18 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-441713874  

I've just encountered a probably related problem (I can post it as a separate issue if you prefer), namely that tag dispatch based on `std::iterator_traits::iterator_category` apparently doesn't work for `boost::transform_iterator`.   
The following code   
  
    #include <boost/iterator/transform_iterator.hpp>  
    #include <iterator>  
  
    int foo(std::random_access_iterator_tag);  
  
    void bar(){  
        using iter_t = boost::transform_iterator<int(*)(int),int* >;  
      
        foo( std::iterator_traits<int*>::iterator_category{} );  
        foo( std::iterator_traits<iter_t>::iterator_category{} );  
    }  
  
e.g. generates the following error message on clang 6:  
  
	<source>:10:9: error: no matching function for call to 'foo'  
			foo( std::iterator_traits<iter_t>::iterator_category{} );  
			^~~  
  
	<source>:4:9: note: candidate function not viable: no known conversion from 'std::iterator_traits<iter_t>::iterator_category' (aka 'boost::iterators::detail::iterator_category_with_traversal<std::input_iterator_tag, boost::iterators::random_access_traversal_tag>') to 'std::random_access_iterator_tag' for 1st argument  
		int foo(std::random_access_iterator_tag);  
		    ^  
  
	1 error generated.  
	Compiler returned: 1  
  
https://godbolt.org/

---

## Comment 2

> Username: tower120  
> Created at: 2018-12-01 09:10:50 UTC  
> Updated at: 2018-12-01 09:11:35 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443412103  

I think I found the reason why my example does not have std::bidirectional tag.  
According to this https://www.boost.org/doc/libs/1_68_0/libs/iterator/doc/iterator_facade.html :   
```  
iterator-category(C,R,V) :=  
   if (C is convertible to std::input_iterator_tag  
       || C is convertible to std::output_iterator_tag  
   )  
       return C  
  
   else if (C is not convertible to incrementable_traversal_tag)  
       the program is ill-formed  
  
   else return a type X satisfying the following two constraints:  
  
      1. X is convertible to X1, and not to any more-derived  
         type, where X1 is defined by:  
  
           if (R is a reference type  
               && C is convertible to forward_traversal_tag)  
           {  
               if (C is convertible to random_access_traversal_tag)  
                   X1 = random_access_iterator_tag  
               else if (C is convertible to bidirectional_traversal_tag)  
                   X1 = bidirectional_iterator_tag  
               else  
                   X1 = forward_iterator_tag  
           }  
           else  
           {  
               if (C is convertible to single_pass_traversal_tag  
                   && R is convertible to V)  
                   X1 = input_iterator_tag  
               else  
                   X1 = C  
           }  
  
      2. category-to-traversal(X) is convertible to the most  
         derived traversal tag type to which X is also  
         convertible, and not to any more-derived traversal tag  
         type.  
```  
  
In order to have forward_iterator_tag, bidirectional_iterator_tag or random_access_iterator_tag, reference type must be reference, in my case - I return value.  
  
So - is  there a reason, why value dereferencing iterator is limited to input_iterator_tag only?

---

## Comment 3

> Username: Lastique  
> Created at: 2018-12-01 09:38:41 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443413574  

> So - is there a reason, why value dereferencing iterator is limited to input_iterator_tag only?  
  
This is according to [forward.iterators]/1, forward iterators require that the `reference` type is actually a reference. This is an important difference from input iterators, which are allowed to construct a value on dereferencing, as opposed to forward iterators, which refer to an existing value.

---

## Comment 4

> Username: tower120  
> Created at: 2018-12-01 09:43:55 UTC  
> Updated at: 2018-12-01 09:48:14 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443413812  

> This is according to [forward.iterators]/1 ...  
  
Standard-wise I agree.  
  
----  
  
But range-v3 view::transform somehow overcome this.... Example:  
```  
    std::vector<int> list{1,2,3,4};  
    auto a = list | ranges::view::transform([](int& i) { return std::pair<int&, int&>(i, i); });  
    a[1].first = 20;  
    // list now {1, 20, 3, 4}  
```  
  
Besides, if I just not specify reference type - everything _seems_ to work. Like this:  
```cpp  
class node_iterator  
	: public boost::iterator_facade  
	  <  
		node_iterator  
		, std::pair<int, int>  
		, boost::bidirectional_traversal_tag  
	  >  
{  
public:  
	node_iterator()  
		: m_i(0) {}  
  
	explicit node_iterator(int i)  
		: m_i(i) {}  
  
private:  
	friend class boost::iterator_core_access;  
  
	void increment() { m_i++; }  
        void decrement() { m_i--; }  
  
	bool equal(node_iterator const& other) const  
	{  
		return this->m_i == other.m_i;  
	}  
  
	std::pair<int, int> dereference() const {   
           return std::pair<int, int>{ m_i, m_i };  
        }  
public:  
	int m_i;  
};  
```  
  
But now, technically `dereference()` return value, while reference_type is a reference.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-12-01 10:08:07 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443415153  

The `reference` type by default is `value_type&`, so I suspect, `operator*()` should either fail to compile or you would get a dangling reference from it. (To compile this, the compiler would have to bind a non-const reference to rvalue, and I know some versions of MSVC allowed that; not sure if that's still the case.)

---

## Comment 6

> Username: tower120  
> Created at: 2018-12-01 10:16:21 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443415590  

Yes, you're right - this is MSVC related behavior.  
I'm closing this "issue".

---

## Comment 7

> Username: tower120  
> Created at: 2018-12-01 10:20:20 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443415802  

By the way. Solution for me was - dereference to rvalue:  
```cpp  
class node_iterator  
	: public boost::iterator_facade  
	  <  
		node_iterator  
		, std::pair<int, int>  
		, boost::bidirectional_traversal_tag  
                , std::pair<int, int>&&  
	  >  
{  
public:  
	node_iterator()  
		: m_i(0) {}  
  
	explicit node_iterator(int i)  
		: m_i(i) {}  
  
private:  
	friend class boost::iterator_core_access;  
  
	void increment() { m_i++; }  
        void decrement() { m_i--; }  
  
	bool equal(node_iterator const& other) const  
	{  
		return this->m_i == other.m_i;  
	}  
  
	std::pair<int, int>&& dereference() const {   
           return std::pair<int, int>{ m_i, m_i };  
        }  
public:  
	int m_i;  
};  
```

---

## Comment 8

> Username: Lastique  
> Created at: 2018-12-01 10:43:40 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443417023  

> Solution for me was - dereference to rvalue  
  
This still leaves you with a dangling reference.

---

## Comment 9

> Username: MikeGitb  
> Created at: 2018-12-01 12:56:35 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443424316  

@Lastique: And idea if my problem is related? Otherwise I'll just open a separate issue.

---

## Comment 10

> Username: tower120  
> Created at: 2018-12-01 13:08:04 UTC  
> Updated at: 2018-12-01 13:59:06 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443425018  

> This still leaves you with a dangling reference.  
  
Can something, like "operator_arrow_dispatch::proxy" be used to have random_access iterator with value return?  
  
---  
  
Also, does overriding boost detected `iterator_category` is a bad thing? Like:  
  
```  
struct MyIter: public boost::iterator_facade{  
    /// .....  
    using iterator_category = std::random_access_iterator_tag;  
}  
```  
  
---  
  
BTW, https://www.boost.org/doc/libs/1_68_0/libs/iterator/doc/transform_iterator.html seems not follow this rule.   
  
If `Iterator` - RandomAcess, and Unary function will return value, according to doc, resulting iterator will have random_access tag AND have `reference` of value type.

---

## Comment 11

> Username: Lastique  
> Created at: 2018-12-01 21:41:46 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443460713  

@MikeGitb I didn't look closely, but it probably is the same problem or related. Transform iterators should not be random access iterators, unless the transform function returns a reference.  
  
@tower120 The arrow proxy has nothing to do with this. You're returning a dangling reference from `dereference()`, long before the proxy gets involved. If you really want your iterator to have a more advanced category, you need to cache the value inside the iterator and return a (lvalue) reference to it from `dereference()`. See https://github.com/boostorg/iterator/blob/develop/include/boost/iterator/function_input_iterator.hpp or https://github.com/boostorg/iterator/blob/develop/include/boost/generator_iterator.hpp for examples.

---

## Comment 12

> Username: tower120  
> Created at: 2018-12-01 23:30:58 UTC  
> Updated at: 2018-12-01 23:32:07 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443467430  

>  you need to cache the value inside the iterator and return a (lvalue) reference to it from dereference()  
  
Thanks, for that example with cached value in `std::optional`. Helped me to understand how to deal with non-dereferenceable end iterator.  
_But even "caching" pair of pointers increase size of iterator substentially... Not saying that this  caching can prevent some compiler optimisations. [at least it looks so]_  
  
----  
  
Besides current standard requirements, it this really that important to RandomAcess iterator dereference to lvalue?  
I mean - can this brake something?  
  
For example, `std::vector<bool>` does not follow this rule... They explicitly return value.

---

## Comment 13

> Username: Lastique  
> Created at: 2018-12-01 23:47:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443468338  

> For example, `std::vector<bool>` does not follow this rule...  
  
That's why it's so infamous.

---

## Comment 14

> Username: tower120  
> Created at: 2018-12-01 23:49:42 UTC  
> Updated at: 2018-12-02 01:45:17 UTC  
> Url: https://github.com/boostorg/iterator/issues/47#issuecomment-443468425  

@MikeGitb Try `ranges::view::transform` - it works as expected (by preserving original iterator_category).  
https://ericniebler.github.io/range-v3/index.html  
  
UPDATE:  
It seems that range-v3 fallback to InputIterator too, alas:  
https://wandbox.org/permlink/k3GYWNIgaLVvpV91  
  
Though, you can actually random access it...  
  
---  
  
This is where `boost::transform_iterator` downgrade you original iterator_category :  
https://www.boost.org/doc/libs/1_68_0/libs/iterator/doc/transform_iterator.html#id4  
  
> The transform_iterator models the **most refined** standard traversal concept that is modeled by the Iterator argument.
