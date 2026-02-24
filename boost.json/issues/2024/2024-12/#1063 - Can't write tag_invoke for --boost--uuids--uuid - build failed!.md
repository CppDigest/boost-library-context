# #1063 - Can't write tag_invoke for ::boost::uuids::uuid - build failed! [Open]

> Username: fsmoke  
> Created at: 2024-12-18 10:46:46 UTC  
> Updated at: 2024-12-19 09:05:47 UTC  
> Url: https://github.com/boostorg/json/issues/1063  

How to write tag_invoke for uuid? - it seems boost.json represents uuid as something like vector, so build failed  
  
Code:  
```  
// uuid argument  
inline ::boost::uuids::uuid tag_invoke(boost::json::value_to_tag<::boost::uuids::uuid>, boost::json::value const & val)  
{  
	namespace bj = boost::json;  
  
	static boost::uuids::string_generator sg;  
  
	return sg(bj::value_to<std::string>(val));  
}  
  
int main()  
{  
	namespace bj = boost::json;  
  
	bj::value v{};  
  
	auto u = bj::value_to<::boost::uuids::uuid>(v);  
}  
```  
  
Errors:  
  
```  
1>------ Build started: Project: json_rpc_test, Configuration: debug x64 ------  
1>Build started 18.12.2024 13:31:51.  
1>Target PrepareForBuild:  
1>  Structured output is enabled. The formatting of compiler diagnostics will reflect the error hierarchy. See https://aka.ms/cpp/structured-output for more details.  
1>Target InitializeBuildStatus:  
1>  Touching "json_rpc_test\x64\debug\json_rpc_test.tlog\unsuccessfulbuild".  
1>Target ClCompile:  
1>  All outputs are up-to-date.  
1>  json_rpc_test.cpp  
1>  C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.41.34120\include\iterator(150,27): error C2039: 'insert': is not a member of 'boost::uuids::uuid'  
1>  (compiling source file '/json_rpc_test.cpp')  
1>      D:\libraries\boost_1_86_0\boost\uuid\uuid.hpp(38,8):  
1>      see declaration of 'boost::uuids::uuid'  
1>      C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.41.34120\include\iterator(150,27):  
1>      the template instantiation context (the oldest one first) is  
1>          D:\json_rpc_test\json_rpc_test.cpp(35,15):  
1>          see reference to function template instantiation 'T boost::json::value_to<boost::uuids::uuid>(const boost::json::value &)' being compiled  
1>          with  
1>          [  
1>              T=boost::uuids::uuid  
1>          ]  
1>          D:\libraries\boost_1_86_0\boost\json\value_to.hpp(148,12):  
1>          see reference to function template instantiation 'T boost::json::value_to<boost::uuids::uuid,boost::json::detail::no_context>(const boost::json::value &,const Context &)' being compiled  
1>          with  
1>          [  
1>              T=boost::uuids::uuid,  
1>              Context=boost::json::detail::no_context  
1>          ]  
1>          D:\libraries\boost_1_86_0\boost\json\value_to.hpp(96,20):  
1>          see reference to function template instantiation 'T boost::json::detail::value_to_impl<boost::json::value_to::cat,boost::uuids::uuid,Context>(Impl,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)' being compiled  
1>          with  
1>          [  
1>              T=boost::uuids::uuid,  
1>              Context=boost::json::detail::no_context,  
1>              Impl=boost::json::value_to::cat,  
1>              Ctx=boost::json::detail::no_context  
1>          ]  
1>          D:\libraries\boost_1_86_0\boost\json\detail\value_to.hpp(926,12):  
1>          see reference to function template instantiation 'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl<T,std::tuple<boost::json::detail::allow_exceptions,Ctx>>(boost::json::detail::sequence_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const std::tuple<boost::json::detail::allow_exceptions,Ctx> &)' being compiled  
1>          with  
1>          [  
1>              T=boost::json::value_to::bare_T,  
1>              Ctx=boost::json::detail::no_context  
1>          ]  
1>          D:\libraries\boost_1_86_0\boost\json\detail\value_to.hpp(272,24):  
1>          see reference to class template instantiation 'std::insert_iterator<T>' being compiled  
1>          with  
1>          [  
1>              T=boost::json::value_to::bare_T  
1>          ]  
1>          C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.41.34120\include\iterator(149,35):  
1>          while compiling class template member function 'std::insert_iterator<T> &std::insert_iterator<T>::operator =(boost::uuids::uuid::value_type &&)'  
1>          with  
1>          [  
1>              T=boost::json::value_to::bare_T  
1>          ]  
1>              D:\libraries\boost_1_86_0\boost\json\detail\value_to.hpp(278,16):  
1>              see the first reference to 'std::insert_iterator<T>::operator =' in 'boost::json::detail::value_to_impl'  
1>          with  
1>          [  
1>              T=boost::json::value_to::bare_T  
1>          ]  
1>Done building target "ClCompile" in project "json_rpc_test.vcxproj" -- FAILED.  
1>  
1>Done building project "json_rpc_test.vcxproj" -- FAILED.  
1>  
1>Build FAILED.  
1>  
1>C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.41.34120\include\iterator(150,27): error C2039: 'insert': is not a member of 'boost::uuids::uuid'  
1>    0 Warning(s)  
1>    1 Error(s)  
```  
  
MSVC 2022 Version 17.11.4  
boost 86

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-12-18 11:54:43 UTC  
> Url: https://github.com/boostorg/json/issues/1063#issuecomment-2551130558  

`tag_invoke`-based customisation relies on ADL, which means that you need to put `tag_invoke` overloads into an associated namespace of one of your arguments.  
  
In your case, it means you need to put it into the namespace `boost::uuids`. Since that is considered a bad practice to put stuff into namespaces you don't own, I recommend you to use [contextual conversion](https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/conversion/contextual_conversions.html). This has the added benefit of avoiding a static object. https://godbolt.org/z/ebr7Tb5KW

---

## Comment 2

> Username: fsmoke  
> Created at: 2024-12-18 15:35:49 UTC  
> Url: https://github.com/boostorg/json/issues/1063#issuecomment-2551636882  

Thank you. Unfortunally i cant use contextual conversion cos i call value_to inside fold expression with variadic template (inside parsing my meta methods ) something like this   
  
```  
auto call = []<typename ResultT, arg_fixed_string Name, argument_entry ... Args>(  
				method<ResultT, Name, Args...> const & m, ParamsT const & params) -> result<ResultT>  
{  
				if constexpr (std::is_same_v<ParamsT, bj::object>)  
					return m(bj::value_to<std::decay_t<typename Args::type>>(params.at(Args::name))...);  
				else if constexpr (std::is_same_v<ParamsT, bj::array>)  
				{  
					//not implemented  
					throw 0;  
				}  
				else if constexpr (std::is_same_v<ParamsT, std::nullptr_t>)  
				{  
					static_assert(sizeof...(Args) == 0, "Arguments must be empty");  
					return m();  
				}  
				else  
					throw 0;  
};  
```  
So i simply don't know what type of argument will be at this point. It may be int, string uuid or anything else. So in case of contextual conversion i must to implement contexts for all types including fundamental - but is obviously irrational.  
  
Besides..  we already have alias of boost::uuids::uuid inside our big project -  for us it's looks like our_ns::uuid.  So for now i just writed tag_invoke for uuid inside "our_ns" namespace - and it's work for me !

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-12-19 09:05:45 UTC  
> Url: https://github.com/boostorg/json/issues/1063#issuecomment-2553135503  

You don't need to implement contextual conversions for every type. It falls back to contextless conversions, if there's no contextual overload. And you can even combine contextual conversions for different types. See this section: https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/conversion/contextual_conversions.html#json.conversion.contextual_conversions.combining_contexts. In the example two types use different contexts, and a third type (`std::vector`) uses the default contextless conversion.  
  
Also, I have a suspicion that putting the overload into the namespace of the alias works due to an MSVC non-conformance (essentialy, a bug).
