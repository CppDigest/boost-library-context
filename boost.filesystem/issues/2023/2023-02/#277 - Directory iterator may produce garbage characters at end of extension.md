# #277 - Directory iterator may produce garbage characters at end of extension [Closed]

> Username: skalleAnka  
> Created at: 2023-02-02 20:29:55 UTC  
> Updated at: 2023-02-04 23:46:00 UTC  
> Closed at: 2023-02-04 21:29:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277  

Using static libraries with static runtime link in Windows, x86, Visual C++ 2022. Upgraded from 1.75 to 1.81 and found a problem that also seems to be present in 1.80.  
  
When doing a regular for loop iteration over files in a directory with **boost::filesystem::directory_iterator**, calling **iterator->path().extension().wstring()** inside the loop seems to sometimes produce garbage characters at the end of the extension string, causing compares to fail.  
  
Interestingly, it might be some kind of timing problem, as the problem disappears if I add a breakpoint right before it does this, and it only appears in optimized Release compile and not in Debug compile.  
  
Also interestingly, it appears to happen when the file name has only three characters before the extension, such as FOO.TXT

---

## Comment 1

> Username: Lastique  
> Created at: 2023-02-02 21:10:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1414377496  

Please provide a reproducer.

---

## Comment 2

> Username: skalleAnka  
> Created at: 2023-02-03 20:06:23 UTC  
> Updated at: 2023-02-03 20:07:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416347505  

I managed to boil it down to this. The problem disappears if I remove the call to is_regular_file. When the call is present, all three letter files report incorrect extension with this test program. Let me know if you need anything else.  
  
```  
#include <boost/filesystem.hpp>  
#include <boost/algorithm/string.hpp>  
#include <fstream>  
#include <iostream>  
#include <string>  
  
namespace fs = boost::filesystem;  
using boost::iequals;  
  
void create_files(const fs::path& p)  
{  
	if (fs::is_directory(p))  
		fs::remove_all(p);  
  
	fs::create_directories(p);  
	//Create some three letter files  
	for (auto c1 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
	{  
		for (auto c2 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
		{  
			for (auto c3 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
			{  
				const std::wstring fileName = { c1, c2, c3, L'.', L't', L'x', L't', L'\0' };  
				std::ofstream s((p / fileName).string());  
				s << "contents" << std::endl;  
			}  
		}  
	}  
	//Create some four letter files  
	for (auto c1 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
	{  
		for (auto c2 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
		{  
			for (auto c3 : { L'A', L'B', L'C', L'D', L'E', L'F' })  
			{  
				const std::wstring fileName = { c1, c2, c3, L'X', L'.', L't', L'x', L't', L'\0' };  
				std::ofstream s((p / fileName).string());  
				s << "contents" << std::endl;  
			}  
		}  
	}  
  
}  
  
void browse(fs::path p)  
{  
	for (fs::directory_iterator i(p.string().c_str()); i != fs::directory_iterator(); ++i)  
	{  
		if (iequals(i->path().extension().wstring(), L".TXT") && fs::is_regular_file(i->path()))  
			continue;  
  
		std::cout << i->path().string() << " reports wrong extension" << std::endl;  
	}  
}  
  
int main()  
{  
	auto path = fs::temp_directory_path() / L"directory.tmp" / L"subdir.tmp";  
	create_files(path);  
  
	browse(path);  
  
	fs::remove_all(path.parent_path());  
}  
  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2023-02-04 18:14:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416816751  

I cannot reproduce this on the latest develop, the test passes for me with no output. Tried debug/release, 32 and 64-bit. Visual Studio 2022 17.4.4, cl version 19.34.31937.  
  
What compiler options are you using? How do you link Boost.Filesystem? Is it possible that you're using mismatched Boost.Filesystem headers and library?

---

## Comment 4

> Username: skalleAnka  
> Created at: 2023-02-04 21:08:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416850552  

I just build boost by downloading the boost 7z release file, ran bootstrap.bat in the Visual Studio 2022 command prompt and ran b2 runtime-link=static link=static like I have done many times before.  
  
I will attach a self contained sample of the test program with headers and lib file. Sorry about the lzma-zipped tar, it was the only way I could think of to make a file that is both of a type that github will accept and small enough.  
[fs_test.zip](https://github.com/boostorg/filesystem/files/10609661/fs_test.zip)

---

## Comment 5

> Username: Lastique  
> Created at: 2023-02-04 21:29:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416854427  

I just compiled your project and it works for me. Sorry, it seems, this is a problem on your local machine. Check if some anti-virus software is playing tricks.

---

## Comment 6

> Username: skalleAnka  
> Created at: 2023-02-04 22:08:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416860692  

I did try this on multiple machines, one with Intel i9 running Windows 10, one with Intel i7 running Windows 11, both of them having different antivirus, and both are quite new. I also just tried it on my older Intel i7 desktop running Windows 10 and got the same problem.  
  
I still think something is going on here, but I don't think it's a problem on the local machine. Maybe a problem for certain processor types? What CPU are you using?

---

## Comment 7

> Username: Lastique  
> Created at: 2023-02-04 22:18:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416862264  

Mine is i7 12700K. I doubt it's the CPU, though.  
  
Are you using some unusual filesystem? I'm running NTFS, and the system is the latest Windows 10 in a VM.  
  
Are you building debug or release? Does it reproduce if you build 64-bit binaries? Are you able to step-through the code in the debugger?

---

## Comment 8

> Username: skalleAnka  
> Created at: 2023-02-04 22:25:33 UTC  
> Updated at: 2023-02-04 22:26:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416863300  

I am also using NTFS, but on actual machines, not VMs.  
  
If I build debug, it works as expected, it's only the release version that causes problems.  
  
I haven't tried a 64-bit build yet. I tried to debug earlier, but having breakpoints right before this happens seemed to remove the problem.  
  
I can try 64-bit builds and see what happens, and see if I can find a way to debug what is actually happening. I'll get back to you.

---

## Comment 9

> Username: Lastique  
> Created at: 2023-02-04 23:02:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416871081  

I managed to reproduce the problem with your test project in release mode. Previously I tried it in debug, which was the default.  
  
However, I also tried to build your project with my locally compiled Boost.Filesystem binaries, and it doesn't reproduce. I tried with Boost.Filesystem develop and 1.81.0 - works either way. So it looks like something went wrong with your build of Boost. BTW, I'm using this command to build Boost.Filesystem:  
  
```  
b2.exe -j 8 toolset=msvc-14.3 address-model=32 link=static runtime-link=static --with-filesystem stage  
```

---

## Comment 10

> Username: Lastique  
> Created at: 2023-02-04 23:40:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416877334  

Looks line it's a compiler optimizer bug. If you change `path::extension()` from `BOOST_FORCEINLINE` to `BOOST_NOINLINE` and `directory_entry::path()` to return by value, it no longer reproduces. The behavior is also dependent on the surrounding unrelated code, so definitely some codegen problem is involved. You should report this to MS, although I'm not sure how well maintained the 32-bit x86 target is nowdays.

---

## Comment 11

> Username: skalleAnka  
> Created at: 2023-02-04 23:45:20 UTC  
> Updated at: 2023-02-04 23:46:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/277#issuecomment-1416877893  

I tried to do some debugging, and had a look at the assembly code. I am now also suspecting a compiler bug. The code for extension() looked fine as far as I could tell, but the assembly code for some reason thought it was a good idea to terminate the string with a single byte 0 instead of a full wchar_t 0.  
  
Thank you for looking into this!
