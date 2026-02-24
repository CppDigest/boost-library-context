# #14 - detail::fields_count<T>() returns different value for same type [Closed]

> Username: rAzoR8  
> Created at: 2017-10-13 12:13:35 UTC  
> Updated at: 2017-10-15 09:01:37 UTC  
> Closed at: 2017-10-14 20:02:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/14  

Hello there, i ran into a problem where for the same type (typeid(T).hash_code is identical) the fields count is 0 when i call it from within another templated function. Its possible that this is a compiler (lastest msvc) problem and not directly related to your code as i have the same problem with a different approach. Maybe you have some insights regardless.   
  
I filed a question at stackoverlfow: https://stackoverflow.com/questions/46675239/ describing my setup.  
  
`	template <class T, bool Assemble>  
	constexpr bool is_var_t(/*const*/ var_t<T, Assemble>& _Member) { return true; }  
  
	template <class T>  
	constexpr bool is_var_t(T& _Member) { return false; }  
  
	template <class T>  
	void InitVar(T& _Member) { std::cout << typeid(T).name() << std::endl; }  
  
	template <class T, bool Assemble>  
	void InitVar(var_t<T, Assemble>& _Member)	{std::cout << typeid(T).name() << std::endl;	}  
  
	template <size_t n, size_t N, class T>  
	void InitStruct(T& _Struct)  
	{  
		std::cout << "n " << n << " N " << N << std::endl;  
		if constexpr(n < N)  
		{  
			decltype(auto) member = hlx::get<n>(_Struct);  
			using MemberType = std::remove_cv_t<decltype(member)>;  
			std::cout << typeid(MemberType).hash_code() << std::endl;  
  
			if (is_var_t(member))  
			{  
				InitVar(member);  
				InitStruct<n + 1, N, T>(_Struct);  
			}  
			else  
			{  
				constexpr size_t M = boost::pfr::detail::fields_count<T>(); // asserts because result is always 0  
				InitStruct<0, M, decltype(member)>(member);  
			}  
		}  
	}  
  
	template <class T, bool Assemble>  
	void InitializeStruct(T& _Struct)  
	{  
		if constexpr(Assemble)  
		{  
			constexpr size_t N = boost::pfr::detail::fields_count<T>();  
			InitStruct<0, N, T>(_Struct);  
		}  
	}`  
the same behaviour is visible here: http://coliru.stacked-crooked.com/a/b25a84454d53d8de  
  
structs are:  
  
`struct B  
{  
	var_t<float4_t, true> f4;  
};  
  
struct A  
{  
	B b;  
};`  
  
even if replace the var_t<> with a fundamental type like int, the problem persists.  
Would you guess this is a compiler problem or is there a code solution?

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-10-13 18:31:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/14#issuecomment-336532903  

It's a problem with MemberType. In your example it's actually a reference to type.  
Change it's definition to   
```using MemberType = std::remove_reference_t<std::remove_cv_t<decltype(member)>>;```  
and everything will work as expected.  
  
I've added some checks for that case into the library.

---

## Comment 2

> Username: rAzoR8  
> Created at: 2017-10-15 09:01:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/14#issuecomment-336696478  

Yeah that was the problem, thanks for the fast response. typeid(MemberType).hash_code() got me confused when it returned tha same value for ref and non ref type.
