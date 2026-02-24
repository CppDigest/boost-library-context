# #87 - Trac #7404 "filesystem::canonical fails on UNC paths on Windows" still an issue in 1.68 [Closed]

> Username: Bigpet  
> Created at: 2018-10-23 10:46:10 UTC  
> Updated at: 2021-06-06 19:59:04 UTC  
> Closed at: 2021-06-06 19:59:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/87  

Original text:  
  
> The filesystem library function canonical(p, base) fails for all UNC paths on Windows because it gets an "invalid path" error when it calls symlink_status at line 816 of operations.cpp. Example: For the path "\\server\share\file", the first time execution reaches line 816 of operations.cpp, it calls is_symlink(detail::symlink_status("server", ec)) and ec is set to the Windows "invalid path" system error code.   
  
This is still an issue. Minimal program to reproduce:  
  
```  
#include <boost/filesystem.hpp>  
#include <cstdio>  
  
namespace fs = boost::filesystem;  
  
int main(int argc, const char** argv)  
{  
	fs::path p(R"(\\localhost\c$)");  
	boost::system::error_code ec;  
	auto p2 = fs::canonical(p,ec);  
	printf("%d", ec.value());  
}  
```  
Only happens on Windows platforms, specifically because of the call to boost::filesystem::detail::symlink_status during filesystem::canonical

---

## Comment 1

> Username: j-renggli  
> Created at: 2019-09-02 11:16:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/87#issuecomment-527109755  

Also a problem with relative paths because it's calling `weakly_canonical` internally. Here's a snippet to reproduce:  
```  
fs::path root("\\\\localhost\\foo");  
fs::path file = root / "bar";  
auto rel_path = fs::relative(file, root);  
// throws boost::filesystem::relative: Incorrect function: "\\localhost\foo"  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2021-06-06 16:41:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/87#issuecomment-855426725  

If Windows doesn't allow to query file status on a remote share then I don't see what I can do in Boost.Filesystem about that.
