# #230 - boost\process\pipe.hpp(282,21): warning C4244: 'argument': conversion from 'ptrdiff_t' to 'int', possible loss of data [Open]

> Username: BullyWiiPlaza  
> Created at: 2021-11-08 16:42:05 UTC  
> Updated at: 2021-11-08 16:42:05 UTC  
> Url: https://github.com/boostorg/process/issues/230  

I'm using [vcpkg](https://github.com/microsoft/vcpkg) and when I implement some code with `Boost.process`, I get the following compilation warning(s):  
```  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(282,21): warning C4244: 'argument': conversion from 'ptrdiff_t' to 'int', possible loss of data  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(263): message : while compiling class template member function 'bool boost::process::basic_pipebuf<char,std::char_traits<char>>::_write_impl(void)'  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(191): message : see reference to function template instantiation 'bool boost::process::basic_pipebuf<char,std::char_traits<char>>::_write_impl(void)' being compiled  
1>C:\vcpkg\installed\x64-windows\include\boost\process\pipe.hpp(300): message : see reference to class template instantiation 'boost::process::basic_pipebuf<char,std::char_traits<char>>' being compiled  
1>D:\Cpp\MyApplication\Main.cpp(37): message : see reference to class template instantiation 'boost::process::basic_ipstream<char,std::char_traits<char>>' being compiled  
```  
  
The code in question can e.g. easily be fixed by adding an additional explicit `int` cast:  
  
```  
bool _write_impl()  
{  
	if (!_pipe.is_open())  
		return false;  
  
	auto base = this->pbase();  
  
	if (base == this->pptr())  
		return true;  
  
	std::ptrdiff_t wrt = _pipe.write(base,  
			static_cast<typename pipe_type::int_type>(this->pptr() - base));  
  
	std::ptrdiff_t diff = this->pptr() - base;  
  
	if (wrt < diff)  
		std::move(base + wrt, base + diff, base);  
	else if (wrt == 0) //broken pipe  
		return false;  
  
	this->pbump((int) - wrt); // <---  
  
	return true;  
}  
```
