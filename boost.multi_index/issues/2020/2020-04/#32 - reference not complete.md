# #32 - reference [Class template mem_fun] not complete [Closed]

> Username: jhcarl0814  
> Created at: 2020-04-17 03:27:10 UTC  
> Updated at: 2020-04-20 16:26:19 UTC  
> Closed at: 2020-04-20 16:26:19 UTC  
> Url: https://github.com/boostorg/multi_index/issues/32  

[Class template mem_fun reference](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/key_extraction.html#mem_fun)  
```  
mem_fun<Class,Type,PtrToMemberFunction> is a model of:  
	Key Extractor from reference_wrapper<Class>,  
	Key Extractor from any chained pointer to Class.  
```  
but:  
&nbsp;&nbsp;&nbsp;&nbsp;if the member function returns non-const reference, the key extractor can be a read/write Key Extractor from reference_wrapper<Class> and any chained pointer to Class  
  
```cpp  
struct student  
{  
	int _i;  
	double _d;  
  
	friend bool operator<(student const& s1, student const& s2)  
	{  
		return std::tie(s1._i, s1._d) < std::tie(s2._i, s2._d);  
	}  
  
	int & i() { return this->_i; }  
  
	template<typename stream_t>  
	friend stream_t&& operator<<(stream_t&& stream, student *const& s)  
	{  
		return static_cast<stream_t && >(stream << "(" << s->_i << "," << s->_d << ")");  
	}  
};  
int main()  
{  
	typedef boost::multi_index::multi_index_container<  
		student*,  
		boost::multi_index::indexed_by<  
			boost::multi_index::ordered_unique<boost::multi_index::identity<student>>,  
			boost::multi_index::ordered_unique<boost::multi_index::mem_fun<student,int&,&student::i>>  
		>  
	>multi_index_student;  
	multi_index_student s({ new student{1,1.0},new student{3,3.0},new student{2,3.0} });  
	std::copy(std::begin(boost::get<1>(s)), std::end(boost::get<1>(s)), std::ostream_iterator<student*>(std::cout));  
	boost::get<1>(s).modify_key(std::begin(boost::get<1>(s)), [](int& i) {i = 5; });  
	std::copy(std::begin(boost::get<1>(s)), std::end(boost::get<1>(s)), std::ostream_iterator<student*>(std::cout));  
}  
```

---

## Comment 1

> Username: jhcarl0814  
> Created at: 2020-04-17 03:40:03 UTC  
> Url: https://github.com/boostorg/multi_index/issues/32#issuecomment-615021509  

same applies to the key extractors thereafter

---

## Comment 2

> Username: jhcarl0814  
> Created at: 2020-04-17 11:58:55 UTC  
> Url: https://github.com/boostorg/multi_index/issues/32#issuecomment-615205264  

there are some other things (if anyone is alive here, can go to fix)  
  
[Specialization of std::equal_to for composite_key results](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/key_extraction.html#equal_to_composite_key_result)  
```  
std::equal:to  
```  
  
[Specialization of std::less for composite_key results](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/key_extraction.html#less_composite_key_result)  
```  
std::less<CompositeKeyResult> is CopyConstructible, CopyConstructible and CopyAssignable  
```  
  
[Specialization of std::greater for composite_key results](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/key_extraction.html#greater_composite_key_result)  
```  
std::greater<CompositeKeyResult> is CopyConstructible, CopyConstructible and CopyAssignable  
```  
  
[Alias template key](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/key_extraction.html#key)  
```  
with ot without  
```  
  
[Manual simulation of a multi_index_container](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/performance.html#simulation)  
```  
ordered_non_unique<identity<int>, std::greater >,  
```

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-04-19 19:16:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/32#issuecomment-616209841  

Hi, thank you for the report, documentation was indeed incomplete about on which situations the predefined key extractors are read/write, fixed in 2374136ea890f3e1af03836ebd2eac40514038e6. Fixed also the typos you spotted (ffbdc49bf2c7263ef06946aba57d565d2c0ac5ed).

---

## Comment 4

> Username: jhcarl0814  
> Created at: 2020-04-20 11:13:05 UTC  
> Url: https://github.com/boostorg/multi_index/issues/32#issuecomment-616482602  

@joaquintides that's great! thanks for the fix
