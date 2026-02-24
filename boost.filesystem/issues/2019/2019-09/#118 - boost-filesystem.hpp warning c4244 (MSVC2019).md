# #118 - boost/filesystem.hpp warning c4244 (MSVC2019) [Closed]

> Username: klasing  
> Created at: 2019-09-15 09:58:40 UTC  
> Updated at: 2019-09-15 12:34:52 UTC  
> Closed at: 2019-09-15 12:34:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/118  

Inside a C++ class I created the following member function.  
`	VOID write_logfile(const tuple_logging& tl)  
	{  
		// write tuple to file  
		wofs.open(p, std::ios_base::app);  
		// results in a compiler warning:  
		// warning C4244: ...  
		// TODO: get rid of this warning  
		wofs << boost::tuples::set_delimiter(L'#') << tl << L'\n';  
		wofs.close();  
	}  
`  
The relevant member variables are:  
`boost::filesystem::wofstream wofs;`  
The tuple, named tl, is constructed with std::wstring elements  
The relevant includes are:  
`#include <boost/filesystem.hpp>  
#include <boost/tuple/tuple.hpp>  
#include <boost/tuple/tuple_io.hpp>  
 `  
The compiler inside the MSVC2019 IDE gives me the following warning.  
 `warning C4244:  'argument': conversion from 'const CharType' to 'const char', possible loss of data` (and a lot more of this stuff) On the following statement.  
`wofs << boost::tuples::set_delimiter(L'#') << tl << L'\n';`  
Can someone help me to get rid of this warning?  
`

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-15 10:28:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/118#issuecomment-531553726  

Can you provide the complete warning text?

---

## Comment 2

> Username: klasing  
> Created at: 2019-09-15 11:59:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/118#issuecomment-531559640  

Hello Andrey, here comes the complete compiler output, when I build the project, named ManageServer_1.  
I hope this can be of any help to you. Thanks in advance for your response.  
Martijn  
  
1>------ Rebuild All started: Project: ManageServer_1, Configuration: Debug Win32 ------  
1>http_server_async.cpp  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(71,4): warning C4390:  ';': empty controlled statement found; is this the intent?  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:  'argument': conversion from 'const CharType' to 'const char', possible loss of data  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         with  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         [  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:             CharType=wchar_t  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         ]  
1>main.cpp  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :  see reference to function template instantiation 'boost::tuples::tuple_manipulator<wchar_t> boost::tuples::set_delimiter<wchar_t>(const CharType)' being compiled  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         with  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         [  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :             CharType=wchar_t  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         ]  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(71,4): warning C4390:  ';': empty controlled statement found; is this the intent?  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:  'argument': conversion from 'const CharType' to 'const char', possible loss of data  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         with  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         [  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:             CharType=wchar_t  
1>C:\Program Files\boost\boost_1_70_0\boost\tuple\tuple_io.hpp(142,71): warning C4244:         ]  
1>Generating Code...  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :  see reference to function template instantiation 'boost::tuples::tuple_manipulator<wchar_t> boost::tuples::set_delimiter<wchar_t>(const CharType)' being compiled  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         with  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         [  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :             CharType=wchar_t  
1>C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\ManageServer_1\ServerLogging.hpp(85): message :         ]  
1>ManageServer_1.vcxproj -> C:\Users\Klasing\MyProgramming\MyCpp\Projects_2019\martha\martha\Debug\ManageServer_1.exe  
1>Done building project "ManageServer_1.vcxproj".  
========== Rebuild All: 1 succeeded, 0 failed, 0 skipped ==========  
  
Verzonden vanuit Mail voor Windows 10  
  
Van: Andrey Semashev  
Verzonden: zondag 15 september 2019 12:28  
Aan: boostorg/filesystem  
CC: klasing; Author  
Onderwerp: Re: [boostorg/filesystem] boost/filesystem.hpp warning c4244(MSVC2019) (#118)  
  
Can you provide the complete warning text?  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-09-15 12:34:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/118#issuecomment-531562005  

The problem is in Boost.Tuple. The implicit conversion that causes the warning happens because of [this](https://github.com/boostorg/tuple/blob/0b724234cedad3302d47a5a432998bf42c3640ba/include/boost/tuple/tuple_io.hpp#L105) constructor argument, which should have type `CharType`.
