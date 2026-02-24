# #23 - error C2059: syntax error: ')' [Closed]

> Username: xR3b0rn  
> Created at: 2019-02-17 17:14:00 UTC  
> Updated at: 2019-08-29 22:31:52 UTC  
> Closed at: 2019-08-29 22:31:52 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/23  

Hey,  
  
I'm using Visual Studio 2017 with /std:c++latest. And I try to compile this piece of code:  
```  
struct O  
{  
	template <class T1, class T2>  
	struct A {};  
};  
template<class T>  
struct argument_type;  
template<class T, class U>  
struct argument_type<T(U)>  
{  
	using type = U;  
};  
#define DEF_VAR(TYPE, NAME) argument_type<void(TYPE)>::type NAME  
int main()  
{  
	#define MEM ((O::A<int*, int>))(val)  
	#define SEQ (MEM)  
	DEF_VAR  
	(  
		BOOST_PP_SEQ_ELEM(0, BOOST_PP_SEQ_HEAD(SEQ)),  
		BOOST_PP_SEQ_ELEM(1, BOOST_PP_SEQ_HEAD(SEQ))  
	);  
}  
```  
Resulting in a `error C2059: syntax error: ')'` error.  
  
Running the same code with C++14 (gcc 6.3) does work fine: https://ideone.com/qHrhB3  
  
  
EDIT:  
Sorry, I figured out  
```  
DEF_VAR  
(  
	((O::A<int*, int>)),  
	(val)  
);  
```   
Also produces this error. So I still have the error, but the preprocessor library does not have to do anything with it.

---

## Comment 1

> Username: eldiener  
> Created at: 2019-04-25 02:10:22 UTC  
> Updated at: 2019-04-25 15:52:42 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/23#issuecomment-486492898  

You seem to misunderstand what a seq is in the Boost preprocessor library. A seq is one or more single element tuples. Your SEQ above is expanded to:  
  
`(((O::A<int*, int>))(val))`  
  
That is a single element seq, not a two element seq.  A two element seq might look like:  
  
`((O::A<int*, int>))(val)`  
  
where the first tuple is:  
  
`((O::A<int*, int>))`  
  
and the second tuple is:  
  
`(val)`  
  
which are both single element tuples. That the contents of the first seq tuple happens to be a two-element tuple is irrelevant to the expression forming a seq since the actual tuple itself is a singl;e element tuple.  
  
Do you see the difference ?  
  
Therefore your statement:  
  
`BOOST_PP_SEQ_ELEM(1, BOOST_PP_SEQ_HEAD(SEQ))`  
  
gives an undefined result.
