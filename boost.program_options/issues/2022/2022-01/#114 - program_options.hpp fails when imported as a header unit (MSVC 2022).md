# #114 - program_options.hpp fails when imported as a header unit (MSVC 2022). [Open]

> Username: LoneBoco  
> Created at: 2022-01-06 02:50:53 UTC  
> Updated at: 2022-01-06 17:23:52 UTC  
> Url: https://github.com/boostorg/program_options/issues/114  

The program fails to compile when trying to import `program_options.hpp` as a header unit in Visual Studio 2022 (C++20).  
  
main.cpp  
```  
import <boost/program_options.hpp>;  
  
int main(int argc, char** argv)  
{  
	namespace po = boost::program_options;  
	po::options_description desc;  
	desc.add_options()  
		("stringtest", po::value<std::string>()->implicit_value("test"), "string test")  
		;  
  
	return 0;  
}  
```  
  
You'll get the following error:  
`Error  -  C3084  -  'boost::exception::~exception': a destructor cannot be 'abstract'  -  solution  -  E:\Projects\CPP\boost\boost\exception\exception.hpp  -  297`  
  
I haven't had any troubles with any other boost headers.  This is the only one I've run into with this issue.  This was tested with the latest master branch.

---

## Comment 1

> Username: LoneBoco  
> Created at: 2022-01-06 02:52:39 UTC  
> Url: https://github.com/boostorg/program_options/issues/114#issuecomment-1006242761  

The full compiler output for the error is:  
```  
1>main.cpp  
1>E:\Projects\CPP\boost\boost\exception\exception.hpp(297,6): error C3084: 'boost::exception::~exception': a destructor cannot be 'abstract'  
1>E:\Projects\CPP\boost\boost\function\function_template.hpp(761): message : see reference to function template instantiation 'void boost::throw_exception<boost::bad_function_call>(const E &)' being compiled  
1>        with  
1>        [  
1>            E=boost::bad_function_call  
1>        ]  
1>E:\Projects\CPP\boost\boost\function\function_template.hpp(759): message : while compiling class template member function 'void boost::function1<void,const T &>::operator ()(T0) const'  
1>        with  
1>        [  
1>            T=std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>,  
1>            T0=const std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>> &  
1>        ]  
1>E:\Projects\CPP\boost\boost\program_options\detail\value_semantic.hpp(45): message : see reference to function template instantiation 'void boost::function1<void,const T &>::operator ()(T0) const' being compiled  
1>        with  
1>        [  
1>            T=std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>,  
1>            T0=const std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>> &  
1>        ]  
1>E:\Projects\CPP\boost\boost\program_options\value_semantic.hpp(373): message : see reference to class template instantiation 'boost::function1<void,const T &>' being compiled  
1>        with  
1>        [  
1>            T=std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>  
1>        ]  
1>E:\Projects\CPP\ModuleTest\main.cpp(15): message : see reference to class template instantiation 'boost::program_options::typed_value<std::vector<std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::allocator<std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>,char>' being compiled  
```

---

## Comment 2

> Username: vprus  
> Created at: 2022-01-06 09:15:50 UTC  
> Url: https://github.com/boostorg/program_options/issues/114#issuecomment-1006400742  

Thank you for the report! Unfortunately, I know next to nothing about import functionality and even less about how it might work with a compiled library, so I don't have any solution.  
  
You might want to investigate further, for example by clarifying why you expect this to work, or trying to import exception headers directly and seeing if that works.

---

## Comment 3

> Username: LoneBoco  
> Created at: 2022-01-06 17:23:52 UTC  
> Url: https://github.com/boostorg/program_options/issues/114#issuecomment-1006767378  

Importing a header compiles it as a header unit.  A header unit is kinda like a precompiled header, in a way.  The big thing is that the preprocessor state before the import has no effect on the contents of the header unit.  It's not a simple text replacement.  The compiler is processing the header file as its own separate entity.  This means that you can't `#define` a symbol that changes how the header is imported.  Basically, you can't `#define _UNICODE` and then `import <Windows.h>;` because when the compiler goes to compile Windows.h, it doesn't see that `#define`.  
  
As for why this is happening, I have no idea!  The problem seems to stem from `boost::throw_exception`.  Specifically with `wrapexcept`.  `boost::bad_function_call` derives from `std::runtime_error`, but for some reason it fails to convert to a boost exception properly and results in this error.  
  
I had expected this to work since boost libraries don't depend on your preprocessor state at all.  They are all self-contained and none of the other parts of boost failed to import.  
  
But I just now tried to import stacktrace and it failed to link, so I'm thinking that maybe some of the complicated boost configuration stuff just doesn't work with modules (maybe when compiler support improves that will change).  I would bet that any boost libs that aren't header-only will fail in one way or another.  So I'll just stick to using `#include` when dealing with boost.
