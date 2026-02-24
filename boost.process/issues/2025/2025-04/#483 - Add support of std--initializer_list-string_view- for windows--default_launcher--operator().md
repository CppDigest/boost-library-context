# #483 - [Windows][MSVC] Add support of std::initializer_list<string_view> for windows::default_launcher::operator() [Open]

> Username: AlrondPrime  
> Created at: 2025-04-28 10:40:02 UTC  
> Updated at: 2025-04-28 15:47:11 UTC  
> Url: https://github.com/boostorg/process/issues/483  

Got a problem at Boost version 1.88  
  
This can be compiled  
```  
#define WIN32_LEAN_AND_MEAN  
#include "boost/process.hpp"  
  
int main()  
{  
	boost::asio::io_context ctx;  
  
	auto process = boost::process::process(ctx, "C:\\Windows\\System32\\cmd.exe", {});  
  
	return 0;  
}  
```  
  
And this can't  
```  
#define WIN32_LEAN_AND_MEAN  
#include "boost/process.hpp"  
  
int main()  
{  
	boost::asio::io_context ctx;  
	boost::process::default_process_launcher launcher;  
  
	auto process = launcher(ctx, "C:\\Windows\\System32\\cmd.exe", {});  
  
	return 0;  
}  
```  
  
Because the basic_process constructor supports std::initializer_list<string_view>, but the windows::default_launcher::operator() does not.  
I would like this functionality to be implemented by the developers/community.

---

## Comment 1

> Username: AlrondPrime  
> Created at: 2025-04-28 11:02:49 UTC  
> Url: https://github.com/boostorg/process/issues/483#issuecomment-2834876396  

I have source code to resolve this issue. Hou can I make a pull request?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-04-28 15:47:10 UTC  
> Url: https://github.com/boostorg/process/issues/483#issuecomment-2835696716  

Fork the repo, push the changes to your fork and then create a PR.
