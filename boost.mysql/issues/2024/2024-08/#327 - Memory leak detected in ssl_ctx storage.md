# #327 - Memory leak detected in ssl_ctx storage [Closed]

> Username: sagunkho  
> Created at: 2024-08-06 08:35:05 UTC  
> Updated at: 2024-08-10 12:35:28 UTC  
> Closed at: 2024-08-10 12:35:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/327  

file.hpp  
```cpp  
  
class DatabaseProcess : public MainframeComponent  
{  
public:  
	// MainframeComponent dispatch module type is set to Main because DatabaseProcess doesn't run on its own thread.  
	DatabaseProcess() : MainframeComponent(Horizon::System::RUNTIME_DATABASE) { }  
	~DatabaseProcess()   
	{   
		_connection.reset();  
	}  
	  
	void initialize(int segment_number = 1) override   
	{  
		set_segment_number(segment_number);  
		HLog(error) << "Database not configured";   
		_is_initialized.exchange(true);  
	}  
  
	void initialize(boost::asio::io_context &io_context, int segment_number, std::string host, int port, std::string user, std::string pass, std::string database);  
  
	void finalize() override   
	{  
		// Close connection object.  
		if (_connection != nullptr) {  
			_connection->close();  
		}  
		  
		_is_finalized.exchange(true);  
	}  
  
	std::shared_ptr<boost::mysql::tcp_ssl_connection> get_connection() { return _connection; }  
  
	bool is_initialized() override { return _is_initialized.load(); }  
	bool is_finalized() override { return _is_finalized.load(); }  
  
protected:  
	boost::asio::ssl::context _ssl_ctx{boost::asio::ssl::context::tls_client};  
    std::shared_ptr<boost::mysql::tcp_ssl_connection> _connection{nullptr};  
	std::atomic<bool> _is_initialized{false};  
	std::atomic<bool> _is_finalized{false};  
};  
```  
file.cpp  
```cpp  
void DatabaseProcess::initialize(boost::asio::io_context &io_context, int segment_number, std::string host, int port, std::string user, std::string pass, std::string database)  
{  
	set_segment_number(segment_number);  
	  
	try {  
		_connection = std::make_shared<boost::mysql::tcp_ssl_connection>(io_context.get_executor(), _ssl_ctx);  
		boost::asio::ip::tcp::resolver resolver(io_context.get_executor());  
		auto endpoints = resolver.resolve(host, std::to_string(port));  
		boost::mysql::handshake_params params(user, pass, database);  
		_connection->connect(*endpoints.begin(), params);  
		_is_initialized.exchange(true);  
	} catch (boost::mysql::error_with_diagnostics &error) {  
		HLog(error) << error.what();  
	}  
}  
```  
On application exit -  
```  
Detected memory leaks!  
Dumping objects ->  
{16447} normal block at 0x000001E457607670, 3 bytes long.  
 Data: <>  > 3E 1C 20  
{14981} normal block at 0x000001E457604BA0, 448 bytes long.  
 Data: <  Ue12    mB \  > F6 DF 55 65 31 32 1D 99 B1 F1 6D 42 83 5C C8 0A  
{14980} normal block at 0x000001E4576049A0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14979} normal block at 0x000001E4576048A0, 184 bytes long.  
 Data: <@  U            > 40 1B BF 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14978} normal block at 0x000001E4576047A0, 184 bytes long.  
 Data: <   U            > E0 A8 C5 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14976} normal block at 0x000001E4576046B0, 176 bytes long.  
 Data: < G`W     H`W    > A0 47 60 57 E4 01 00 00 A0 48 60 57 E4 01 00 00  
{14975} normal block at 0x000001E455BCBE70, 296 bytes long.  
 Data: <        0  U    > 00 00 00 00 00 00 00 00 30 FE C4 55 E4 01 00 00  
{14974} normal block at 0x000001E457602D80, 40 bytes long.  
 Data: <   U    p  U    > 90 A6 B9 55 E4 01 00 00 70 BE BC 55 E4 01 00 00  
{14971} normal block at 0x000001E4576044B0, 448 bytes long.  
 Data: <  W w  E        > 04 F6 57 03 77 08 FC 45 F7 12 E0 C0 15 97 02 EA  
{14970} normal block at 0x000001E4576042B0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14969} normal block at 0x000001E4576041B0, 184 bytes long.  
 Data: <@  U            > 40 1B BF 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14968} normal block at 0x000001E4576040B0, 184 bytes long.  
 Data: <   U            > E0 A8 C5 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14966} normal block at 0x000001E455C1D050, 176 bytes long.  
 Data: < @`W     A`W    > B0 40 60 57 E4 01 00 00 B0 41 60 57 E4 01 00 00  
{14965} normal block at 0x000001E455BCBFE0, 296 bytes long.  
 Data: <        0  U    > 00 00 00 00 00 00 00 00 30 FE C4 55 E4 01 00 00  
{14964} normal block at 0x000001E457603170, 40 bytes long.  
 Data: <   U       U    > 90 A6 B9 55 E4 01 00 00 E0 BF BC 55 E4 01 00 00  
{14962} normal block at 0x000001E4575FB1F0, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14959} normal block at 0x000001E457603EB0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14958} normal block at 0x000001E457603DB0, 184 bytes long.  
 Data: <   U            > E0 A8 C5 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14957} normal block at 0x000001E457603BB0, 448 bytes long.  
 Data: < H    D#)l f _  > 91 48 EC 89 D3 02 44 23 29 6C C5 66 9E 5F 0C A4  
{14956} normal block at 0x000001E4576039B0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14955} normal block at 0x000001E4576038B0, 184 bytes long.  
 Data: <@  U            > 40 1B BF 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14954} normal block at 0x000001E4575F7460, 184 bytes long.  
 Data: <   U            > E0 A8 C5 55 E4 01 00 00 00 00 00 00 00 00 00 00  
{14940} normal block at 0x000001E455C7EF10, 176 bytes long.  
 Data: <`t_W     8`W    > 60 74 5F 57 E4 01 00 00 B0 38 60 57 E4 01 00 00  
{14939} normal block at 0x000001E455BCC880, 296 bytes long.  
 Data: <  _W    0  U    > F0 B1 5F 57 E4 01 00 00 30 FE C4 55 E4 01 00 00  
{14938} normal block at 0x000001E4575F4A40, 40 bytes long.  
 Data: <   U       U    > 90 A6 B9 55 E4 01 00 00 80 C8 BC 55 E4 01 00 00  
{14931} normal block at 0x000001E4575FBC40, 16 bytes long.  
 Data: <0  U            > 30 FE C4 55 E4 01 00 00 01 00 00 00 00 00 00 00  
{14930} normal block at 0x000001E4575F4960, 40 bytes long.  
 Data: <p  U    @ _W    > 70 B5 B9 55 E4 01 00 00 40 BC 5F 57 E4 01 00 00  
{14898} normal block at 0x000001E4575FB510, 9 bytes long.  
 Data: <SEED-SRC > 53 45 45 44 2D 53 52 43 00  
{14897} normal block at 0x000001E455B9B570, 200 bytes long.  
 Data: < G U            > F0 47 C0 55 E4 01 00 00 C4 00 00 00 00 00 00 00  
{14853} normal block at 0x000001E4575FB970, 9 bytes long.  
 Data: <CTR-DRBG > 43 54 52 2D 44 52 42 47 00  
{14852} normal block at 0x000001E455B9A690, 200 bytes long.  
 Data: < G U            > F0 47 C0 55 E4 01 00 00 C1 00 00 00 00 00 00 00  
{14098} normal block at 0x000001E455C7E4F0, 84 bytes long.  
 Data: < lobal default l> 00 6C 6F 62 61 6C 20 64 65 66 61 75 6C 74 20 6C  
{11776} normal block at 0x000001E455BBF950, 12 bytes long.  
 Data: <AES-256-CTR > 41 45 53 2D 32 35 36 2D 43 54 52 00  
{11774} normal block at 0x000001E455BF1B40, 240 bytes long.  
 Data: <                > 8A 03 00 00 01 00 00 00 20 00 00 00 10 00 00 00  
{11459} normal block at 0x000001E455BBAE70, 12 bytes long.  
 Data: <AES-256-ECB > 41 45 53 2D 32 35 36 2D 45 43 42 00  
{11457} normal block at 0x000001E455C5A8E0, 240 bytes long.  
 Data: <                > AA 01 00 00 10 00 00 00 20 00 00 00 00 00 00 00  
{11423} normal block at 0x000001E455BBA8D0, 19 bytes long.  
 Data: <BIO to Core filt> 42 49 4F 20 74 6F 20 43 6F 72 65 20 66 69 6C 74  
{11422} normal block at 0x000001E455C1EF00, 112 bytes long.  
 Data: <           U    > 19 04 00 00 00 00 00 00 D0 A8 BB 55 E4 01 00 00  
{11421} normal block at 0x000001E455C4FE30, 24 bytes long.  
 Data: < G U     j J    > F0 47 C0 55 E4 01 00 00 C0 6A C7 4A F7 7F 00 00  
{11420} normal block at 0x000001E455BBAE20, 8 bytes long.  
 Data: <default > 64 65 66 61 75 6C 74 00  
{11419} normal block at 0x000001E455C50430, 32 bytes long.  
 Data: <                > 00 00 00 00 CD CD CD CD 00 00 00 00 00 00 00 00  
{11418} normal block at 0x000001E455BBAF60, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11417} normal block at 0x000001E455BBADD0, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11416} normal block at 0x000001E455BBAD80, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11415} normal block at 0x000001E455C047F0, 224 bytes long.  
 Data: <        `  U    > 01 00 00 00 00 00 00 00 60 AF BB 55 E4 01 00 00  
{8037} normal block at 0x000001E455C05BD0, 840 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
Object dump complete.  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-07 06:42:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2272735850  

Hi @sagunkho,  
  
Could you please elaborate why do you think the leak is caused by `ssl_ctx`? I can see some objects that look like allocated by OpenSSL in your dump, but they may be being kept alive by other components. Without knowing your application structure (i.e. without knowing how `DatabaseProcess` and other objects are created and destroyed), I can't help much more.  
  
I'd note that this pattern:  
  
```  
boost::asio::ssl::context _ssl_ctx{boost::asio::ssl::context::tls_client};  
   std::shared_ptr<boost::mysql::tcp_ssl_connection> _connection{nullptr};  
```  
  
Seems error-prone. The SSL context should be kept alive for the lifetime of the connection, at least, which this is not guaranteeing. If shared ownership semantics are needed, I suggest that you group these two into a `struct` and create a `shared_ptr` to that struct (which I think I already suggested in a previous issue).  
  
If you're using Boost 1.85+ and you don't mind using experimental features, you might also consider using [`any_connection`](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/any_connection.html), which takes care of the context creation for you.

---

## Comment 2

> Username: sagunkho  
> Created at: 2024-08-07 07:55:24 UTC  
> Updated at: 2024-08-07 07:55:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2272851139  

https://github.com/horizonxyz/horizon/actions/runs/10269112883/job/28413796095#step:4:309  
  
Is there some kind of cleanup that is required?

---

## Comment 3

> Username: anarthal  
> Created at: 2024-08-07 09:00:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2272977542  

There shouldn't be. Let me have a look at your logs.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-08-07 09:19:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2273013063  

Which test is the one detecting the memory leak? Can't find the test name from the logs

---

## Comment 5

> Username: sagunkho  
> Created at: 2024-08-07 13:05:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2273429352  

This is the file that is causing the leak  -   
https://github.com/horizonxyz/horizon/blob/concurrent-architecture-2/src/Tests/AuthServerTest.cpp

---

## Comment 6

> Username: sagunkho  
> Created at: 2024-08-07 13:08:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2273434316  

The initialize of DatabaseProcess is called here - https://github.com/horizonxyz/horizon/blob/825ecb6a323e0080c5f7278fd908052c9532ba00/src/Server/Common/Server.cpp#L299  
And the initialize method definition is here -  
https://github.com/horizonxyz/horizon/blob/825ecb6a323e0080c5f7278fd908052c9532ba00/src/Server/Common/Server.cpp#L187

---

## Comment 7

> Username: sagunkho  
> Created at: 2024-08-07 13:10:25 UTC  
> Updated at: 2024-08-07 13:10:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2273438750  

These are just the test files, there are the actual targets which are build and there is no memory dump from here. However, it is weird that this is happening in the test files.  
Auth, Zone, Char logs -   
https://github.com/horizonxyz/horizon/actions/runs/10269112883/job/28419790529  
Thanks for taking a look.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-08-07 17:44:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2274000611  

I've been having a look and I'm still missing info to come to a conclusion. Which tool are you using to detect the leaks? If it's [this one](https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170) could you please follow the instructions and define `_CRTDBG_MAP_ALLOC` in your debug builds so we can get a better understanding on where was the memory allocated?  
  
Also, the log seems to contain an error line, which I'd say indicates lack of connectivity to the database in your test run. Does the leak report happen when no error is reported, too?

---

## Comment 9

> Username: sagunkho  
> Created at: 2024-08-08 04:58:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2274953623  

I'm using the standard MSVC built-in tool, if there is any. There is no  
special tool used. MSVC is used to compile. The errors are not detected in  
GCC and Clang.  
  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.www.avast.com  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Wed, Aug 7, 2024 at 11:15 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> I've been having a look and I'm still missing info to come to a  
> conclusion. Which tool are you using to detect the leaks? If it's this one  
> <https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170>  
> could you please follow the instructions and define _CRTDBG_MAP_ALLOC in  
> your debug builds so we can get a better understanding on where was the  
> memory allocated?  
>  
> Also, the log seems to contain an error line, which I'd say indicates lack  
> of connectivity to the database in your test run. Does the leak report  
> happen when no error is reported, too?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2274000611>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGYZZUWI7S3TBQPK25ULZQJMKDAVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDENZUGAYDANRRGE>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 10

> Username: anarthal  
> Created at: 2024-08-08 10:36:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2275497634  

I've been trying to fork your repo and reproduce the reported leak (https://github.com/anarthal/horizon/actions/runs/10300441653/job/28509832971) but I'm getting errors. Are you using self-hosted GitHub runners?  
  
The problem here is that the failing build has a lot of complexity, with a lots of components involved. In order to investigate it, I need a minimum reproducible example, if possible only using Boost.MySQL and no other components. Otherwise, it's impossible to know where exactly the leak is coming from.

---

## Comment 11

> Username: sagunkho  
> Created at: 2024-08-08 12:27:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2275697837  

Hi Anarthal,  
  
I've reverted the repo to the builds which are passing. The latest commit  
on that repository will be passing in a few hours. I am using self-hosted  
Github Runners.  
  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.www.avast.com  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Thu, Aug 8, 2024 at 4:06 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> I've been trying to fork your repo and reproduce the reported leak (  
> https://github.com/anarthal/horizon/actions/runs/10300441653/job/28509832971)  
> but I'm getting errors. Are you using self-hosted GitHub runners?  
>  
> The problem here is that the failing build has a lot of complexity, with a  
> lots of components involved. In order to investigate it, I need a minimum  
> reproducible example, if possible only using Boost.MySQL and no other  
> components. Otherwise, it's impossible to know where exactly the leak is  
> coming from.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2275497634>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGY72CNZLQKJ6XW2GJRTZQNC3DAVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDENZVGQ4TONRTGQ>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 12

> Username: anarthal  
> Created at: 2024-08-08 16:04:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2276179865  

Please post a link to the build you're mentioning and to the code diff that fixes the problem - I may be able to provide further guidance.

---

## Comment 13

> Username: sagunkho  
> Created at: 2024-08-09 02:56:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2277049855  

This is the build -  
Concurrent Architecture with Horizon New Design ·  
***@***.*** (github.com)  
<https://github.com/horizonxyz/horizon/actions/runs/10299497300>  
Haven't found a fix yet so there's no code-diff. The build was earlier  
failing because I was trying to provide shared libraries as an option in  
configuring the project.  
The library is statically linked now and not shared, there is no option to  
link it as shared. It has nothing to do with the mysql memory leak problem.  
The files are still the same, the code is still the same where the problem  
generates. I had only reverted the code to  
`61fed78cd9eb5e6c612bb6f7ac0c42d8a6bc5d90` on branch  
`concurrent-architecture-2`.  
Thanks for helping with the problem.  
  
On Thu, Aug 8, 2024 at 9:34 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> Please post a link to the build you're mentioning and to the code diff  
> that fixes the problem - I may be able to provide further guidance.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2276179865>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGY6UDLGVDPQWDQP32H3ZQOJJXAVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDENZWGE3TSOBWGU>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>  
  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.www.avast.com  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>

---

## Comment 14

> Username: anarthal  
> Created at: 2024-08-09 09:48:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2277573559  

I've finally found the tool you're using - it's [a Boost.Test feature activated on MSVC debug builds](https://live.boost.org/doc/libs/1_85_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/detect_memory_leaks.html) that uses [the CRT leak detector](https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170) underneath.  
  
We need to know where the allocations came from. Can you please issue a build defining the `_CRTDBG_MAP_ALLOC` macro in all your translation units (as per [these docs](https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170)) and see if we get more info?  
  
Thanks.

---

## Comment 15

> Username: sagunkho  
> Created at: 2024-08-10 05:28:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2279392778  

I've created a test case -  
```cpp  
#include "Server/Auth/Auth.hpp"  
  
#define BOOST_TEST_MODULE AuthServerTest  
#include <boost/test/included/unit_test.hpp>  
  
#define _CRTDBG_MAP_ALLOC  
#include <stdlib.h>  
#include <crtdbg.h>  
  
// One one test in this file as the server is a singleton and we can't have  
multiple instances of it.  
// If another test was created, the object would be destroyed and the  
second test would fail / crash.  
BOOST_AUTO_TEST_CASE(AuthServerTest)  
{  
    _CrtSetDbgFlag ( _CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF );  
    try {  
        std::thread thread = std::thread([&]() {  
            sAuth->general_conf().set_test_run(TEST_RUN_MINIMAL);  
  
            sAuth->general_conf().set_config_file_path(  
"config/auth-server.lua.dist");  
            sAuth->read_config();  
            set_shutdown_signal(SHUTDOWN_INITIATED);  
            sAuth->initialize();  
        });  
        thread.join();  
    } catch(std::length_error &e) {  
        std::cerr << "Exception caught: " << e.what() << std::endl;  
    } catch(std::bad_alloc &e) {  
        std::cerr << "Exception caught: " << e.what() << std::endl;  
    } catch(std::exception &e) {  
        std::cerr << "Exception caught: " << e.what() << std::endl;  
    } catch(...) {  
        std::cerr << "Unknown exception caught." << std::endl;  
    }  
    _CrtSetReportMode(_CRT_WARN, _CRTDBG_MODE_DEBUG);  
    _CrtDumpMemoryLeaks();  
}  
```  
  
And within this, the following leak was dumped. Please see attached file.  
  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.www.avast.com  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Fri, Aug 9, 2024 at 3:18 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> I've finally found the tool you're using - it's a Boost.Test feature  
> activated on MSVC debug builds  
> <https://live.boost.org/doc/libs/1_85_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/detect_memory_leaks.html>  
> that uses the CRT leak detector  
> <https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170>  
> underneath.  
>  
> We need to know where the allocations came from. Can you please issue a  
> build defining the _CRTDBG_MAP_ALLOC macro in all your translation units  
> (as per these docs  
> <https://learn.microsoft.com/en-us/cpp/c-runtime-library/find-memory-leaks-using-the-crt-library?view=msvc-170>)  
> and see if we get more info?  
>  
> Thanks.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2277573559>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGY4KP5BFYKXH2EIAQGLZQSF7BAVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDENZXGU3TGNJVHE>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>  
  
{12194} normal block at 0x000002D008E52A70, 88 bytes long.  
 Data: < ?      PR`     > E0 3F E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12193} normal block at 0x000002D008E4AC30, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 90 D0 E4 08 D0 02 00 00  
{12192} normal block at 0x000002D008E4ABD0, 24 bytes long.  
 Data: < 2      0       > 02 32 00 00 00 00 00 00 30 AC E4 08 D0 02 00 00  
{12191} normal block at 0x000002D008DF4BD0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12189} normal block at 0x000002D008DADDC0, 13 bytes long.  
 Data: <ARIA-192-ECB > 41 52 49 41 2D 31 39 32 2D 45 43 42 00  
{12187} normal block at 0x000002D008E57A10, 240 bytes long.  
 Data: <.               > 2E 04 00 00 10 00 00 00 18 00 00 00 00 00 00 00  
{12185} normal block at 0x000002D008E4A7B0, 32 bytes long.  
 Data: < P              > A0 50 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12184} normal block at 0x000002D008E55960, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12183} normal block at 0x000002D008E55660, 128 bytes long.  
 Data: <`Y              > 60 59 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12182} normal block at 0x000002D008E55360, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12181} normal block at 0x000002D008E53970, 88 bytes long.  
 Data: <`S      PR`     > 60 53 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12180} normal block at 0x000002D008E4A750, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 B0 A7 E4 08 D0 02 00 00  
{12179} normal block at 0x000002D008E4A6F0, 24 bytes long.  
 Data: < <      P       > 02 3C 00 00 00 00 00 00 50 A7 E4 08 D0 02 00 00  
{12178} normal block at 0x000002D008DF50A0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12176} normal block at 0x000002D008DAD870, 13 bytes long.  
 Data: <ARIA-256-ECB > 41 52 49 41 2D 32 35 36 2D 45 43 42 00  
{12174} normal block at 0x000002D008E577B0, 240 bytes long.  
 Data: <3               > 33 04 00 00 10 00 00 00 20 00 00 00 00 00 00 00  
{12172} normal block at 0x000002D008E4A510, 32 bytes long.  
 Data: < T              > 90 54 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12171} normal block at 0x000002D008E552A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12170} normal block at 0x000002D008E543A0, 128 bytes long.  
 Data: < R              > A0 52 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12169} normal block at 0x000002D008E558A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12168} normal block at 0x000002D008E531F0, 88 bytes long.  
 Data: < X      PR`     > A0 58 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12167} normal block at 0x000002D008E4A390, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 A5 E4 08 D0 02 00 00  
{12166} normal block at 0x000002D008E4B890, 24 bytes long.  
 Data: < 9              > 02 39 00 00 00 00 00 00 90 A3 E4 08 D0 02 00 00  
{12165} normal block at 0x000002D008DF5490, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12163} normal block at 0x000002D008DAD690, 13 bytes long.  
 Data: <ARIA-128-CCM > 41 52 49 41 2D 31 32 38 2D 43 43 4D 00  
{12161} normal block at 0x000002D008E56E30, 240 bytes long.  
 Data: <`               > 60 04 00 00 01 00 00 00 10 00 00 00 0C 00 00 00  
{12159} normal block at 0x000002D008E4A5D0, 32 bytes long.  
 Data: < J              > 80 4A DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12158} normal block at 0x000002D008E55DE0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12157} normal block at 0x000002D008E54520, 128 bytes long.  
 Data: < ]              > E0 5D E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12156} normal block at 0x000002D008E54A60, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12155} normal block at 0x000002D008E52890, 88 bytes long.  
 Data: <`J      PR`     > 60 4A E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12154} normal block at 0x000002D008E4B770, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 D0 A5 E4 08 D0 02 00 00  
{12153} normal block at 0x000002D008E4AB70, 24 bytes long.  
 Data: <        p       > 02 04 00 00 00 00 00 00 70 B7 E4 08 D0 02 00 00  
{12152} normal block at 0x000002D008DF4A80, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12150} normal block at 0x000002D008DA72C0, 13 bytes long.  
 Data: <ARIA-192-CCM > 41 52 49 41 2D 31 39 32 2D 43 43 4D 00  
{12148} normal block at 0x000002D008E565E0, 240 bytes long.  
 Data: <a               > 61 04 00 00 01 00 00 00 18 00 00 00 0C 00 00 00  
{12146} normal block at 0x000002D008E4A210, 32 bytes long.  
 Data: < M              > 90 4D DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12145} normal block at 0x000002D008E55D20, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12144} normal block at 0x000002D008E551E0, 128 bytes long.  
 Data: < ]              > 20 5D E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12143} normal block at 0x000002D008E557E0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12142} normal block at 0x000002D008E529D0, 88 bytes long.  
 Data: < W      PR`     > E0 57 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12141} normal block at 0x000002D008E4A810, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 A2 E4 08 D0 02 00 00  
{12140} normal block at 0x000002D008E4A870, 24 bytes long.  
 Data: < !              > 02 21 00 00 00 00 00 00 10 A8 E4 08 D0 02 00 00  
{12139} normal block at 0x000002D008DF4D90, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12137} normal block at 0x000002D008DA7950, 13 bytes long.  
 Data: <ARIA-256-CCM > 41 52 49 41 2D 32 35 36 2D 43 43 4D 00  
{12135} normal block at 0x000002D008E578E0, 240 bytes long.  
 Data: <b               > 62 04 00 00 01 00 00 00 20 00 00 00 0C 00 00 00  
{12133} normal block at 0x000002D008E4B710, 32 bytes long.  
 Data: < G              > E0 47 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12132} normal block at 0x000002D008E55060, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12131} normal block at 0x000002D008E554E0, 128 bytes long.  
 Data: <`P              > 60 50 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12130} normal block at 0x000002D008E55C60, 128 bytes long.  
 Data: < :              > B0 3A 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{12129} normal block at 0x000002D008E52BB0, 88 bytes long.  
 Data: <`\      PR`     > 60 5C E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12128} normal block at 0x000002D008E4B650, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 B7 E4 08 D0 02 00 00  
{12127} normal block at 0x000002D008E4B530, 24 bytes long.  
 Data: < 1      P       > 02 31 00 00 00 00 00 00 50 B6 E4 08 D0 02 00 00  
{12126} normal block at 0x000002D008DF47E0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12124} normal block at 0x000002D008DA7900, 13 bytes long.  
 Data: <ARIA-128-GCM > 41 52 49 41 2D 31 32 38 2D 47 43 4D 00  
{12122} normal block at 0x000002D008E56F60, 240 bytes long.  
 Data: <c               > 63 04 00 00 01 00 00 00 10 00 00 00 0C 00 00 00  
{12120} normal block at 0x000002D008E4B4D0, 32 bytes long.  
 Data: < E              > B0 45 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12119} normal block at 0x000002D008E54E20, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12118} normal block at 0x000002D008E54820, 128 bytes long.  
 Data: < N              > 20 4E E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12117} normal block at 0x000002D008E549A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12116} normal block at 0x000002D008E53C90, 88 bytes long.  
 Data: < I      PR`     > A0 49 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12115} normal block at 0x000002D008E4A2D0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 D0 B4 E4 08 D0 02 00 00  
{12114} normal block at 0x000002D008E4A1B0, 24 bytes long.  
 Data: <                > 02 0B 00 00 00 00 00 00 D0 A2 E4 08 D0 02 00 00  
{12113} normal block at 0x000002D008DF45B0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12111} normal block at 0x000002D008DA7450, 13 bytes long.  
 Data: <ARIA-192-GCM > 41 52 49 41 2D 31 39 32 2D 47 43 4D 00  
{12109} normal block at 0x000002D008E57090, 240 bytes long.  
 Data: <d               > 64 04 00 00 01 00 00 00 18 00 00 00 0C 00 00 00  
{12107} normal block at 0x000002D008E4B3B0, 32 bytes long.  
 Data: < J              > 10 4A DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12106} normal block at 0x000002D008E54760, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12105} normal block at 0x000002D008E54FA0, 128 bytes long.  
 Data: <`G              > 60 47 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12104} normal block at 0x000002D008E55EA0, 128 bytes long.  
 Data: <06              > 30 36 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{12103} normal block at 0x000002D008E53D30, 88 bytes long.  
 Data: < ^      PR`     > A0 5E E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12102} normal block at 0x000002D008E4AAB0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 B0 B3 E4 08 D0 02 00 00  
{12101} normal block at 0x000002D008E4B290, 24 bytes long.  
 Data: <                > 02 0E 00 00 00 00 00 00 B0 AA E4 08 D0 02 00 00  
{12100} normal block at 0x000002D008DF4A10, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12098} normal block at 0x000002D008DA7220, 13 bytes long.  
 Data: <ARIA-256-GCM > 41 52 49 41 2D 32 35 36 2D 47 43 4D 00  
{12096} normal block at 0x000002D008E56D00, 240 bytes long.  
 Data: <e               > 65 04 00 00 01 00 00 00 20 00 00 00 0C 00 00 00  
{12094} normal block at 0x000002D008E4B410, 32 bytes long.  
 Data: < Q              > 80 51 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12093} normal block at 0x000002D008E55AE0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12092} normal block at 0x000002D008E55BA0, 128 bytes long.  
 Data: < Z              > E0 5A E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12091} normal block at 0x000002D008E555A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12090} normal block at 0x000002D008E538D0, 88 bytes long.  
 Data: < U      PR`     > A0 55 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12089} normal block at 0x000002D008E4B230, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 B4 E4 08 D0 02 00 00  
{12088} normal block at 0x000002D008E4AD50, 24 bytes long.  
 Data: <        0       > 02 94 00 00 00 00 00 00 30 B2 E4 08 D0 02 00 00  
{12087} normal block at 0x000002D008DF5180, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12085} normal block at 0x000002D008E4B0B0, 24 bytes long.  
 Data: <AES-256-CBC-HMAC> 41 45 53 2D 32 35 36 2D 43 42 43 2D 48 4D 41 43  
{12083} normal block at 0x000002D008E57550, 240 bytes long.  
 Data: <                > B6 03 00 00 10 00 00 00 20 00 00 00 10 00 00 00  
{12082} normal block at 0x000002D008E4B6B0, 24 bytes long.  
 Data: <0q       0      > 30 71 DA 08 D0 02 00 00 F0 30 84 0A D0 02 00 00  
{12081} normal block at 0x000002D008E4AB10, 24 bytes long.  
 Data: <AES-256-CBC-HMAC> 41 45 53 2D 32 35 36 2D 43 42 43 2D 48 4D 41 43  
{12080} normal block at 0x000002D008DA7130, 16 bytes long.  
 Data: <                > 10 AB E4 08 D0 02 00 00 94 00 00 00 00 00 00 00  
{12078} normal block at 0x000002D008E4AF90, 32 bytes long.  
 Data: < T              > 20 54 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12077} normal block at 0x000002D008E55120, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12076} normal block at 0x000002D008E542E0, 128 bytes long.  
 Data: < Q              > 20 51 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12075} normal block at 0x000002D008E55720, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12074} normal block at 0x000002D008E53150, 88 bytes long.  
 Data: < W      PR`     > 20 57 E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12073} normal block at 0x000002D008E4AF30, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 90 AF E4 08 D0 02 00 00  
{12072} normal block at 0x000002D008E4AC90, 24 bytes long.  
 Data: <        0       > 02 93 00 00 00 00 00 00 30 AF E4 08 D0 02 00 00  
{12071} normal block at 0x000002D008DF5420, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12069} normal block at 0x000002D008E4A9F0, 24 bytes long.  
 Data: <AES-128-CBC-HMAC> 41 45 53 2D 31 32 38 2D 43 42 43 2D 48 4D 41 43  
{12067} normal block at 0x000002D008E56BD0, 240 bytes long.  
 Data: <                > B4 03 00 00 10 00 00 00 10 00 00 00 10 00 00 00  
{12066} normal block at 0x000002D008E4A450, 24 bytes long.  
 Data: < p       ^      > E0 70 DA 08 D0 02 00 00 B0 5E 84 0A D0 02 00 00  
{12065} normal block at 0x000002D008E4A570, 24 bytes long.  
 Data: <AES-128-CBC-HMAC> 41 45 53 2D 31 32 38 2D 43 42 43 2D 48 4D 41 43  
{12064} normal block at 0x000002D008DA70E0, 16 bytes long.  
 Data: <p               > 70 A5 E4 08 D0 02 00 00 93 00 00 00 00 00 00 00  
{12062} normal block at 0x000002D008E4A630, 32 bytes long.  
 Data: <0I              > 30 49 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12061} normal block at 0x000002D008E548E0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12060} normal block at 0x000002D008E54EE0, 128 bytes long.  
 Data: < H              > E0 48 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12059} normal block at 0x000002D008E54D60, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12058} normal block at 0x000002D008E52930, 88 bytes long.  
 Data: <`M      PR`     > 60 4D E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12057} normal block at 0x000002D008E4AED0, 32 bytes long.  
 Data: <        0       > 01 00 00 00 00 00 00 00 30 A6 E4 08 D0 02 00 00  
{12056} normal block at 0x000002D008E4B7D0, 24 bytes long.  
 Data: <                > 02 92 00 00 00 00 00 00 D0 AE E4 08 D0 02 00 00  
{12055} normal block at 0x000002D008DF4930, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12053} normal block at 0x000002D008E4A150, 22 bytes long.  
 Data: <AES-256-CBC-HMAC> 41 45 53 2D 32 35 36 2D 43 42 43 2D 48 4D 41 43  
{12051} normal block at 0x000002D008E57B40, 240 bytes long.  
 Data: <                > 96 03 00 00 10 00 00 00 20 00 00 00 10 00 00 00  
{12050} normal block at 0x000002D008E4B2F0, 24 bytes long.  
 Data: < v              > D0 76 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12049} normal block at 0x000002D008E4A990, 22 bytes long.  
 Data: <AES-256-CBC-HMAC> 41 45 53 2D 32 35 36 2D 43 42 43 2D 48 4D 41 43  
{12048} normal block at 0x000002D008DA76D0, 16 bytes long.  
 Data: <                > 90 A9 E4 08 D0 02 00 00 92 00 00 00 00 00 00 00  
{12046} normal block at 0x000002D008E4ACF0, 32 bytes long.  
 Data: < L              > B0 4C DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12045} normal block at 0x000002D008E55420, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12044} normal block at 0x000002D008E546A0, 128 bytes long.  
 Data: < T              > 20 54 E5 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12043} normal block at 0x000002D008E55A20, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12042} normal block at 0x000002D008E52390, 88 bytes long.  
 Data: < Z      PR`     > 20 5A E5 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12041} normal block at 0x000002D008E4A4B0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 F0 AC E4 08 D0 02 00 00  
{12040} normal block at 0x000002D008E4B170, 24 bytes long.  
 Data: <                > 02 91 00 00 00 00 00 00 B0 A4 E4 08 D0 02 00 00  
{12039} normal block at 0x000002D008DF4CB0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12037} normal block at 0x000002D008E4AE70, 22 bytes long.  
 Data: <AES-128-CBC-HMAC> 41 45 53 2D 31 32 38 2D 43 42 43 2D 48 4D 41 43  
{12035} normal block at 0x000002D008DF1FB0, 240 bytes long.  
 Data: <                > 94 03 00 00 10 00 00 00 10 00 00 00 10 00 00 00  
{12034} normal block at 0x000002D008E4A3F0, 24 bytes long.  
 Data: <`s              > 60 73 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12033} normal block at 0x000002D008E4A930, 22 bytes long.  
 Data: <AES-128-CBC-HMAC> 41 45 53 2D 31 32 38 2D 43 42 43 2D 48 4D 41 43  
{12032} normal block at 0x000002D008DA7360, 16 bytes long.  
 Data: <0               > 30 A9 E4 08 D0 02 00 00 91 00 00 00 00 00 00 00  
{12030} normal block at 0x000002D008E4B050, 32 bytes long.  
 Data: < R              > D0 52 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12029} normal block at 0x000002D008DF43A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12028} normal block at 0x000002D008DF3F20, 128 bytes long.  
 Data: < C              > A0 43 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12027} normal block at 0x000002D008DF3E60, 128 bytes long.  
 Data: < ?       F      > 20 3F DF 08 D0 02 00 00 A0 46 E5 08 D0 02 00 00  
{12026} normal block at 0x000002D008DF27E0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12025} normal block at 0x000002D008E53290, 88 bytes long.  
 Data: < '      PR`     > E0 27 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12024} normal block at 0x000002D008E4B1D0, 32 bytes long.  
 Data: <        P       > 01 00 00 00 00 00 00 00 50 B0 E4 08 D0 02 00 00  
{12023} normal block at 0x000002D008E4B470, 24 bytes long.  
 Data: <                > 02 90 00 00 00 00 00 00 D0 B1 E4 08 D0 02 00 00  
{12022} normal block at 0x000002D008DF52D0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12020} normal block at 0x000002D008E4ADB0, 21 bytes long.  
 Data: <AES-128-WRAP-PAD> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 50 41 44  
{12018} normal block at 0x000002D008DF1500, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 10 00 00 00 04 00 00 00  
{12017} normal block at 0x000002D008E4B830, 24 bytes long.  
 Data: ***@***.***              > 40 70 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12016} normal block at 0x000002D008DA7A90, 20 bytes long.  
 Data: <AES128-WRAP-PAD-> 41 45 53 31 32 38 2D 57 52 41 50 2D 50 41 44 2D  
{12015} normal block at 0x000002D008DA7040, 16 bytes long.  
 Data: < z              > 90 7A DA 08 D0 02 00 00 90 00 00 00 00 00 00 00  
{12014} normal block at 0x000002D008E4A690, 24 bytes long.  
 Data: <p|              > 70 7C DA 08 D0 02 00 00 10 9B 84 0A D0 02 00 00  
{12013} normal block at 0x000002D008E4A0F0, 21 bytes long.  
 Data: <AES-128-WRAP-PAD> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 50 41 44  
{12012} normal block at 0x000002D008DA7C70, 16 bytes long.  
 Data: <                > F0 A0 E4 08 D0 02 00 00 90 00 00 00 00 00 00 00  
{12010} normal block at 0x000002D008E4B110, 32 bytes long.  
 Data: < Q              > 10 51 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12009} normal block at 0x000002D008DF42E0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12008} normal block at 0x000002D008DF2A20, 128 bytes long.  
 Data: < B              > E0 42 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{12007} normal block at 0x000002D008DF3DA0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{12006} normal block at 0x000002D008E53830, 88 bytes long.  
 Data: < =      PR`     > A0 3D DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{12005} normal block at 0x000002D008E4A330, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 B1 E4 08 D0 02 00 00  
{12004} normal block at 0x000002D008E4AA50, 24 bytes long.  
 Data: <        0       > 02 8F 00 00 00 00 00 00 30 A3 E4 08 D0 02 00 00  
{12003} normal block at 0x000002D008DF5110, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{12001} normal block at 0x000002D008E4B5F0, 21 bytes long.  
 Data: <AES-192-WRAP-PAD> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 50 41 44  
{11999} normal block at 0x000002D008DF12A0, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 18 00 00 00 04 00 00 00  
{11998} normal block at 0x000002D008E4AE10, 24 bytes long.  
 Data: < |              > 20 7C DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11997} normal block at 0x000002D008DA6FF0, 20 bytes long.  
 Data: <AES192-WRAP-PAD-> 41 45 53 31 39 32 2D 57 52 41 50 2D 50 41 44 2D  
{11996} normal block at 0x000002D008DA7C20, 16 bytes long.  
 Data: < o              > F0 6F DA 08 D0 02 00 00 8F 00 00 00 00 00 00 00  
{11995} normal block at 0x000002D008E4A270, 24 bytes long.  
 Data: < }              > B0 7D DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11994} normal block at 0x000002D008E4B350, 21 bytes long.  
 Data: <AES-192-WRAP-PAD> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 50 41 44  
{11993} normal block at 0x000002D008DA7DB0, 16 bytes long.  
 Data: <P               > 50 B3 E4 08 D0 02 00 00 8F 00 00 00 00 00 00 00  
{11991} normal block at 0x000002D008E4A8D0, 32 bytes long.  
 Data: <`R              > 60 52 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11990} normal block at 0x000002D008DF3CE0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11989} normal block at 0x000002D008DF2960, 128 bytes long.  
 Data: < <              > E0 3C DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11988} normal block at 0x000002D008DF3C20, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11987} normal block at 0x000002D008E52B10, 88 bytes long.  
 Data: < <      PR`     > 20 3C DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11986} normal block at 0x000002D008E4AFF0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 D0 A8 E4 08 D0 02 00 00  
{11985} normal block at 0x000002D008E4B590, 24 bytes long.  
 Data: <                > 02 8E 00 00 00 00 00 00 F0 AF E4 08 D0 02 00 00  
{11984} normal block at 0x000002D008DF5260, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11982} normal block at 0x000002D008E49310, 21 bytes long.  
 Data: <AES-256-WRAP-PAD> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 50 41 44  
{11980} normal block at 0x000002D008DF1D50, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 20 00 00 00 04 00 00 00  
{11979} normal block at 0x000002D008E49490, 24 bytes long.  
 Data: < q              > 80 71 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11978} normal block at 0x000002D008DA6F00, 20 bytes long.  
 Data: <AES256-WRAP-PAD-> 41 45 53 32 35 36 2D 57 52 41 50 2D 50 41 44 2D  
{11977} normal block at 0x000002D008DA7180, 16 bytes long.  
 Data: < o              > 00 6F DA 08 D0 02 00 00 8E 00 00 00 00 00 00 00  
{11976} normal block at 0x000002D008E492B0, 24 bytes long.  
 Data: < x       Q      > 10 78 DA 08 D0 02 00 00 90 51 84 0A D0 02 00 00  
{11975} normal block at 0x000002D008E490D0, 21 bytes long.  
 Data: <AES-256-WRAP-PAD> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 50 41 44  
{11974} normal block at 0x000002D008DA7810, 16 bytes long.  
 Data: <                > D0 90 E4 08 D0 02 00 00 8E 00 00 00 00 00 00 00  
{11972} normal block at 0x000002D008E49070, 32 bytes long.  
 Data: < I              > A0 49 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11971} normal block at 0x000002D008DF4220, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11970} normal block at 0x000002D008DF4160, 128 bytes long.  
 Data: < B              > 20 42 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11969} normal block at 0x000002D008DF3B60, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11968} normal block at 0x000002D008E52070, 88 bytes long.  
 Data: <`;      PR`     > 60 3B DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11967} normal block at 0x000002D008E49010, 32 bytes long.  
 Data: <        p       > 01 00 00 00 00 00 00 00 70 90 E4 08 D0 02 00 00  
{11966} normal block at 0x000002D008E48D70, 24 bytes long.  
 Data: <                > 02 8D 00 00 00 00 00 00 10 90 E4 08 D0 02 00 00  
{11965} normal block at 0x000002D008DF49A0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11963} normal block at 0x000002D008DA7680, 17 bytes long.  
 Data: <AES-128-WRAP-INV> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 49 4E 56  
{11961} normal block at 0x000002D008DF1760, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 10 00 00 00 08 00 00 00  
{11960} normal block at 0x000002D008E48FB0, 24 bytes long.  
 Data: <pw              > 70 77 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11959} normal block at 0x000002D008DA6F50, 16 bytes long.  
 Data: <AES128-WRAP-INV > 41 45 53 31 32 38 2D 57 52 41 50 2D 49 4E 56 00  
{11958} normal block at 0x000002D008DA7770, 16 bytes long.  
 Data: <Po              > 50 6F DA 08 D0 02 00 00 8D 00 00 00 00 00 00 00  
{11957} normal block at 0x000002D008E48DD0, 24 bytes long.  
 Data: < t              > 00 74 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11956} normal block at 0x000002D008DA7D10, 17 bytes long.  
 Data: <AES-128-WRAP-INV> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 49 4E 56  
{11955} normal block at 0x000002D008DA7400, 16 bytes long.  
 Data: < }              > 10 7D DA 08 D0 02 00 00 8D 00 00 00 00 00 00 00  
{11953} normal block at 0x000002D008E48CB0, 32 bytes long.  
 Data: < H              > C0 48 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11952} normal block at 0x000002D008DF3AA0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11951} normal block at 0x000002D008DF36E0, 128 bytes long.  
 Data: < :              > A0 3A DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11950} normal block at 0x000002D008DF2720, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11949} normal block at 0x000002D008E521B0, 88 bytes long.  
 Data: < '      PR`     > 20 27 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11948} normal block at 0x000002D008E48C50, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 B0 8C E4 08 D0 02 00 00  
{11947} normal block at 0x000002D008E49250, 24 bytes long.  
 Data: <        P       > 02 8C 00 00 00 00 00 00 50 8C E4 08 D0 02 00 00  
{11946} normal block at 0x000002D008DF48C0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11944} normal block at 0x000002D008DA7B30, 17 bytes long.  
 Data: <AES-192-WRAP-INV> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 49 4E 56  
{11942} normal block at 0x000002D008DF1E80, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 18 00 00 00 08 00 00 00  
{11941} normal block at 0x000002D008E4A090, 24 bytes long.  
 Data: < 0              > 90 30 DA 08 D0 02 00 00 D0 20 84 0A D0 02 00 00  
{11940} normal block at 0x000002D008DA7B80, 16 bytes long.  
 Data: <AES192-WRAP-INV > 41 45 53 31 39 32 2D 57 52 41 50 2D 49 4E 56 00  
{11939} normal block at 0x000002D008DA3090, 16 bytes long.  
 Data: < {              > 80 7B DA 08 D0 02 00 00 8C 00 00 00 00 00 00 00  
{11938} normal block at 0x000002D008E4A030, 24 bytes long.  
 Data: < /       6      > A0 2F DA 08 D0 02 00 00 D0 36 E4 08 D0 02 00 00  
{11937} normal block at 0x000002D008DA2FF0, 17 bytes long.  
 Data: <AES-192-WRAP-INV> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 49 4E 56  
{11936} normal block at 0x000002D008DA2FA0, 16 bytes long.  
 Data: < /              > F0 2F DA 08 D0 02 00 00 8C 00 00 00 00 00 00 00  
{11934} normal block at 0x000002D008E49EB0, 32 bytes long.  
 Data: < S              > B0 53 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11933} normal block at 0x000002D008DF3620, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11932} normal block at 0x000002D008DF3560, 128 bytes long.  
 Data: < 6              > 20 36 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11931} normal block at 0x000002D008DF39E0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11930} normal block at 0x000002D008DE8DA0, 88 bytes long.  
 Data: < 9      PR`     > E0 39 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11929} normal block at 0x000002D008E49E50, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 B0 9E E4 08 D0 02 00 00  
{11928} normal block at 0x000002D008E49FD0, 24 bytes long.  
 Data: <        P       > 02 8B 00 00 00 00 00 00 50 9E E4 08 D0 02 00 00  
{11927} normal block at 0x000002D008DF53B0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11925} normal block at 0x000002D008DA39F0, 17 bytes long.  
 Data: <AES-256-WRAP-INV> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 49 4E 56  
{11923} normal block at 0x000002D008DF1170, 240 bytes long.  
 Data: <                > 00 00 00 00 08 00 00 00 20 00 00 00 08 00 00 00  
{11922} normal block at 0x000002D008E49C70, 24 bytes long.  
 Data: <P9              > 50 39 DA 08 D0 02 00 00 D0 96 E4 08 D0 02 00 00  
{11921} normal block at 0x000002D008DA30E0, 16 bytes long.  
 Data: <AES256-WRAP-INV > 41 45 53 32 35 36 2D 57 52 41 50 2D 49 4E 56 00  
{11920} normal block at 0x000002D008DA3950, 16 bytes long.  
 Data: < 0              > E0 30 DA 08 D0 02 00 00 8B 00 00 00 00 00 00 00  
{11919} normal block at 0x000002D008E49BB0, 24 bytes long.  
 Data: < 8      p       > B0 38 DA 08 D0 02 00 00 70 D8 E4 08 D0 02 00 00  
{11918} normal block at 0x000002D008DA2F00, 17 bytes long.  
 Data: <AES-256-WRAP-INV> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 49 4E 56  
{11917} normal block at 0x000002D008DA38B0, 16 bytes long.  
 Data: < /              > 00 2F DA 08 D0 02 00 00 8B 00 00 00 00 00 00 00  
{11915} normal block at 0x000002D008E49AF0, 32 bytes long.  
 Data: <pG              > 70 47 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11914} normal block at 0x000002D008DF25A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11913} normal block at 0x000002D008DF34A0, 128 bytes long.  
 Data: < %              > A0 25 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11912} normal block at 0x000002D008DF3260, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11911} normal block at 0x000002D008DE7900, 88 bytes long.  
 Data: <`2      PR`     > 60 32 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11910} normal block at 0x000002D008E49130, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 F0 9A E4 08 D0 02 00 00  
{11909} normal block at 0x000002D008E48B30, 24 bytes long.  
 Data: <        0       > 02 12 00 00 00 00 00 00 30 91 E4 08 D0 02 00 00  
{11908} normal block at 0x000002D008DF4770, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11906} normal block at 0x000002D008DA2AA0, 17 bytes long.  
 Data: <AES-128-WRAP-PAD> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 50 41 44  
{11904} normal block at 0x000002D008DF2340, 240 bytes long.  
 Data: <                > 81 03 00 00 08 00 00 00 10 00 00 00 04 00 00 00  
{11903} normal block at 0x000002D008E49550, 24 bytes long.  
 Data: ***@***.***              > 40 30 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11902} normal block at 0x000002D008DA33B0, 16 bytes long.  
 Data: <AES128-WRAP-PAD > 41 45 53 31 32 38 2D 57 52 41 50 2D 50 41 44 00  
{11901} normal block at 0x000002D008DA3040, 16 bytes long.  
 Data: < 3              > B0 33 DA 08 D0 02 00 00 12 00 00 00 00 00 00 00  
{11900} normal block at 0x000002D008E48E30, 24 bytes long.  
 Data: < 3              > 10 33 DA 08 D0 02 00 00 90 A6 E4 08 D0 02 00 00  
{11899} normal block at 0x000002D008DA3220, 17 bytes long.  
 Data: <AES-128-WRAP-PAD> 41 45 53 2D 31 32 38 2D 57 52 41 50 2D 50 41 44  
{11898} normal block at 0x000002D008DA3310, 16 bytes long.  
 Data: < 2              > 20 32 DA 08 D0 02 00 00 12 00 00 00 00 00 00 00  
{11896} normal block at 0x000002D008E49190, 32 bytes long.  
 Data: < H              > 10 48 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11895} normal block at 0x000002D008DF2D20, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11894} normal block at 0x000002D008DF37A0, 128 bytes long.  
 Data: < -              > 20 2D DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11893} normal block at 0x000002D008DF3320, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11892} normal block at 0x000002D008DE77C0, 88 bytes long.  
 Data: < 3      PR`     > 20 33 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11891} normal block at 0x000002D008E48D10, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 90 91 E4 08 D0 02 00 00  
{11890} normal block at 0x000002D008E497F0, 24 bytes long.  
 Data: <                > 02 0D 00 00 00 00 00 00 10 8D E4 08 D0 02 00 00  
{11889} normal block at 0x000002D008E04810, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11887} normal block at 0x000002D008DA2E60, 17 bytes long.  
 Data: <AES-192-WRAP-PAD> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 50 41 44  
{11885} normal block at 0x000002D008DF20E0, 240 bytes long.  
 Data: <                > 84 03 00 00 08 00 00 00 18 00 00 00 04 00 00 00  
{11884} normal block at 0x000002D008E49790, 24 bytes long.  
 Data: < ,      P4      > 80 2C DA 08 D0 02 00 00 50 34 84 0A D0 02 00 00  
{11883} normal block at 0x000002D008DA2E10, 16 bytes long.  
 Data: <AES192-WRAP-PAD > 41 45 53 31 39 32 2D 57 52 41 50 2D 50 41 44 00  
{11882} normal block at 0x000002D008DA2C80, 16 bytes long.  
 Data: < .              > 10 2E DA 08 D0 02 00 00 0D 00 00 00 00 00 00 00  
{11881} normal block at 0x000002D008E48BF0, 24 bytes long.  
 Data: <`8      0%      > 60 38 DA 08 D0 02 00 00 30 25 E4 08 D0 02 00 00  
{11880} normal block at 0x000002D008DA2CD0, 17 bytes long.  
 Data: <AES-192-WRAP-PAD> 41 45 53 2D 31 39 32 2D 57 52 41 50 2D 50 41 44  
{11879} normal block at 0x000002D008DA3860, 16 bytes long.  
 Data: < ,              > D0 2C DA 08 D0 02 00 00 0D 00 00 00 00 00 00 00  
{11877} normal block at 0x000002D008E49730, 32 bytes long.  
 Data: < >              > E0 3E E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11876} normal block at 0x000002D008DF28A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11875} normal block at 0x000002D008DF2660, 128 bytes long.  
 Data: < (              > A0 28 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11874} normal block at 0x000002D008DF3020, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11873} normal block at 0x000002D008DE8C60, 88 bytes long.  
 Data: < 0      PR`     > 20 30 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11872} normal block at 0x000002D008E49970, 32 bytes long.  
 Data: <        0       > 01 00 00 00 00 00 00 00 30 97 E4 08 D0 02 00 00  
{11871} normal block at 0x000002D008E48F50, 24 bytes long.  
 Data: <        p       > 02 15 00 00 00 00 00 00 70 99 E4 08 D0 02 00 00  
{11870} normal block at 0x000002D008E03EE0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11868} normal block at 0x000002D008DA3810, 17 bytes long.  
 Data: <AES-256-WRAP-PAD> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 50 41 44  
{11866} normal block at 0x000002D008DF1040, 240 bytes long.  
 Data: <                > 87 03 00 00 08 00 00 00 20 00 00 00 04 00 00 00  
{11865} normal block at 0x000002D008E49DF0, 24 bytes long.  
 Data: < -      p       > C0 2D DA 08 D0 02 00 00 70 12 E4 08 D0 02 00 00  
{11864} normal block at 0x000002D008DA2D20, 16 bytes long.  
 Data: <AES256-WRAP-PAD > 41 45 53 32 35 36 2D 57 52 41 50 2D 50 41 44 00  
{11863} normal block at 0x000002D008DA2DC0, 16 bytes long.  
 Data: < -              > 20 2D DA 08 D0 02 00 00 15 00 00 00 00 00 00 00  
{11862} normal block at 0x000002D008E49430, 24 bytes long.  
 Data: <P/              > 50 2F DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11861} normal block at 0x000002D008DA31D0, 17 bytes long.  
 Data: <AES-256-WRAP-PAD> 41 45 53 2D 32 35 36 2D 57 52 41 50 2D 50 41 44  
{11860} normal block at 0x000002D008DA2F50, 16 bytes long.  
 Data: < 1              > D0 31 DA 08 D0 02 00 00 15 00 00 00 00 00 00 00  
{11858} normal block at 0x000002D008E49F10, 32 bytes long.  
 Data: < F              > C0 46 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11857} normal block at 0x000002D008DF2F60, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11856} normal block at 0x000002D008DF40A0, 128 bytes long.  
 Data: <`/              > 60 2F DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11855} normal block at 0x000002D008DF3FE0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11854} normal block at 0x000002D008DE8BC0, 88 bytes long.  
 Data: < ?      PR`     > E0 3F DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11853} normal block at 0x000002D008E49D30, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 9F E4 08 D0 02 00 00  
{11852} normal block at 0x000002D008E491F0, 24 bytes long.  
 Data: < >      0       > 02 3E 00 00 00 00 00 00 30 9D E4 08 D0 02 00 00  
{11851} normal block at 0x000002D008E046C0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11849} normal block at 0x000002D008DA36D0, 13 bytes long.  
 Data: <AES-128-WRAP > 41 45 53 2D 31 32 38 2D 57 52 41 50 00  
{11847} normal block at 0x000002D008DF1C20, 240 bytes long.  
 Data: <                > 14 03 00 00 08 00 00 00 10 00 00 00 08 00 00 00  
{11846} normal block at 0x000002D008E494F0, 24 bytes long.  
 Data: < 5              > E0 35 DA 08 D0 02 00 00 B0 11 E4 08 D0 02 00 00  
{11845} normal block at 0x000002D008DA37C0, 12 bytes long.  
 Data: <AES128-WRAP > 41 45 53 31 32 38 2D 57 52 41 50 00  
{11844} normal block at 0x000002D008DA35E0, 16 bytes long.  
 Data: < 7      >       > C0 37 DA 08 D0 02 00 00 3E 00 00 00 00 00 00 00  
{11843} normal block at 0x000002D008E49A90, 24 bytes long.  
 Data: < 9      pT      > 00 39 DA 08 D0 02 00 00 70 54 E4 08 D0 02 00 00  
{11842} normal block at 0x000002D008DA2EB0, 13 bytes long.  
 Data: <AES-128-WRAP > 41 45 53 2D 31 32 38 2D 57 52 41 50 00  
{11841} normal block at 0x000002D008DA3900, 16 bytes long.  
 Data: < .      >       > B0 2E DA 08 D0 02 00 00 3E 00 00 00 00 00 00 00  
{11839} normal block at 0x000002D008E49C10, 32 bytes long.  
 Data: < >              > 00 3E E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11838} normal block at 0x000002D008DF31A0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11837} normal block at 0x000002D008DF2EA0, 128 bytes long.  
 Data: < 1              > A0 31 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11836} normal block at 0x000002D008DF3920, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11835} normal block at 0x000002D008DE8B20, 88 bytes long.  
 Data: < 9      PR`     > 20 39 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11834} normal block at 0x000002D008E49A30, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 9C E4 08 D0 02 00 00  
{11833} normal block at 0x000002D008E49F70, 24 bytes long.  
 Data: <        0       > 02 17 00 00 00 00 00 00 30 9A E4 08 D0 02 00 00  
{11832} normal block at 0x000002D008E03E00, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11830} normal block at 0x000002D008DA3360, 13 bytes long.  
 Data: <AES-192-WRAP > 41 45 53 2D 31 39 32 2D 57 52 41 50 00  
{11828} normal block at 0x000002D008DF2210, 240 bytes long.  
 Data: <                > 15 03 00 00 08 00 00 00 18 00 00 00 08 00 00 00  
{11827} normal block at 0x000002D008E496D0, 24 bytes long.  
 Data: < 1              > 80 31 DA 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11826} normal block at 0x000002D008DA2C30, 12 bytes long.  
 Data: <AES192-WRAP > 41 45 53 31 39 32 2D 57 52 41 50 00  
{11825} normal block at 0x000002D008DA3180, 16 bytes long.  
 Data: <0,              > 30 2C DA 08 D0 02 00 00 17 00 00 00 00 00 00 00  
{11824} normal block at 0x000002D008E498B0, 24 bytes long.  
 Data: < 4      0       > F0 34 DA 08 D0 02 00 00 30 BB E4 08 D0 02 00 00  
{11823} normal block at 0x000002D008DA3630, 13 bytes long.  
 Data: <AES-192-WRAP > 41 45 53 2D 31 39 32 2D 57 52 41 50 00  
{11822} normal block at 0x000002D008DA34F0, 16 bytes long.  
 Data: <06              > 30 36 DA 08 D0 02 00 00 17 00 00 00 00 00 00 00  
{11820} normal block at 0x000002D008E49910, 32 bytes long.  
 Data: < =              > 20 3D E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11819} normal block at 0x000002D008DF4460, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11818} normal block at 0x000002D008DF33E0, 128 bytes long.  
 Data: <`D              > 60 44 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11817} normal block at 0x000002D008DF2C60, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11816} normal block at 0x000002D008DE92A0, 88 bytes long.  
 Data: <`,      PR`     > 60 2C DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11815} normal block at 0x000002D008E48AD0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 99 E4 08 D0 02 00 00  
{11814} normal block at 0x000002D008E48A70, 24 bytes long.  
 Data: < .              > 02 2E 00 00 00 00 00 00 D0 8A E4 08 D0 02 00 00  
{11813} normal block at 0x000002D008E03D20, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11811} normal block at 0x000002D008DA32C0, 13 bytes long.  
 Data: <AES-256-WRAP > 41 45 53 2D 32 35 36 2D 57 52 41 50 00  
{11809} normal block at 0x000002D008DF0590, 240 bytes long.  
 Data: <                > 16 03 00 00 08 00 00 00 20 00 00 00 08 00 00 00  
{11808} normal block at 0x000002D008E49670, 24 bytes long.  
 Data: ***@***.***              > 40 31 DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11807} normal block at 0x000002D008DA3270, 12 bytes long.  
 Data: <AES256-WRAP > 41 45 53 32 35 36 2D 57 52 41 50 00  
{11806} normal block at 0x000002D008DC3140, 16 bytes long.  
 Data: <p2      .       > 70 32 DA 08 D0 02 00 00 2E 00 00 00 00 00 00 00  
{11805} normal block at 0x000002D008E49B50, 24 bytes long.  
 Data: < ,       '      > E0 2C DC 08 D0 02 00 00 F0 27 84 0A D0 02 00 00  
{11804} normal block at 0x000002D008DC2D80, 13 bytes long.  
 Data: <AES-256-WRAP > 41 45 53 2D 32 35 36 2D 57 52 41 50 00  
{11803} normal block at 0x000002D008DC2CE0, 16 bytes long.  
 Data: < -      .       > 80 2D DC 08 D0 02 00 00 2E 00 00 00 00 00 00 00  
{11801} normal block at 0x000002D008E48A10, 32 bytes long.  
 Data: <PF              > 50 46 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11800} normal block at 0x000002D008DF2DE0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11799} normal block at 0x000002D008DF30E0, 128 bytes long.  
 Data: < -              > E0 2D DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11798} normal block at 0x000002D008DF2BA0, 128 bytes long.  
 Data: < B              > 90 42 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{11797} normal block at 0x000002D008DE8A80, 88 bytes long.  
 Data: < +      PR`     > A0 2B DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11796} normal block at 0x000002D008E49370, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 8A E4 08 D0 02 00 00  
{11795} normal block at 0x000002D008E48950, 24 bytes long.  
 Data: < )      p       > 02 29 00 00 00 00 00 00 70 93 E4 08 D0 02 00 00  
{11794} normal block at 0x000002D008E04650, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11792} normal block at 0x000002D008DC3050, 12 bytes long.  
 Data: <AES-128-CCM > 41 45 53 2D 31 32 38 2D 43 43 4D 00  
{11790} normal block at 0x000002D008DF0DE0, 240 bytes long.  
 Data: <                > 80 03 00 00 01 00 00 00 10 00 00 00 0C 00 00 00  
{11788} normal block at 0x000002D008E49610, 32 bytes long.  
 Data: < ;              > D0 3B E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11787} normal block at 0x000002D008DF3860, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11786} normal block at 0x000002D008DF2AE0, 128 bytes long.  
 Data: <`8              > 60 38 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11785} normal block at 0x000002D008DEF240, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11784} normal block at 0x000002D008DE8F80, 88 bytes long.  
 Data: <@       PR`     > 40 F2 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11783} normal block at 0x000002D008E49CD0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 10 96 E4 08 D0 02 00 00  
{11782} normal block at 0x000002D008E48B90, 24 bytes long.  
 Data: < :              > 02 3A 00 00 00 00 00 00 D0 9C E4 08 D0 02 00 00  
{11781} normal block at 0x000002D008E03BD0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11779} normal block at 0x000002D008DC2E20, 12 bytes long.  
 Data: <AES-192-CCM > 41 45 53 2D 31 39 32 2D 43 43 4D 00  
{11777} normal block at 0x000002D008DF06C0, 240 bytes long.  
 Data: <                > 83 03 00 00 01 00 00 00 18 00 00 00 0C 00 00 00  
{11775} normal block at 0x000002D008E49850, 32 bytes long.  
 Data: < H              > 80 48 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11774} normal block at 0x000002D008DEF180, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11773} normal block at 0x000002D008DEF0C0, 128 bytes long.  
 Data: <                > 80 F1 DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11772} normal block at 0x000002D008DEED00, 128 bytes long.  
 Data: <pG              > 70 47 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{11771} normal block at 0x000002D008DE86C0, 88 bytes long.  
 Data: <        PR`     > 00 ED DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11770} normal block at 0x000002D008E499D0, 32 bytes long.  
 Data: <        P       > 01 00 00 00 00 00 00 00 50 98 E4 08 D0 02 00 00  
{11769} normal block at 0x000002D008E49D90, 24 bytes long.  
 Data: < ,              > 02 2C 00 00 00 00 00 00 D0 99 E4 08 D0 02 00 00  
{11768} normal block at 0x000002D008E04880, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11766} normal block at 0x000002D008DC2FB0, 12 bytes long.  
 Data: <AES-256-CCM > 41 45 53 2D 32 35 36 2D 43 43 4D 00  
{11764} normal block at 0x000002D008DF0B80, 240 bytes long.  
 Data: <                > 86 03 00 00 01 00 00 00 20 00 00 00 0C 00 00 00  
{11762} normal block at 0x000002D008E48EF0, 32 bytes long.  
 Data: <pE              > 70 45 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11761} normal block at 0x000002D008DEEAC0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11760} normal block at 0x000002D008DEF900, 128 bytes long.  
 Data: <                > C0 EA DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11759} normal block at 0x000002D008DEEA00, 128 bytes long.  
 Data: <p5              > 70 35 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{11758} normal block at 0x000002D008DE8120, 88 bytes long.  
 Data: <        PR`     > 00 EA DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11757} normal block at 0x000002D008E489B0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 F0 8E E4 08 D0 02 00 00  
{11756} normal block at 0x000002D008E493D0, 24 bytes long.  
 Data: <                > 02 1B 00 00 00 00 00 00 B0 89 E4 08 D0 02 00 00  
{11755} normal block at 0x000002D008E04570, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11753} normal block at 0x000002D008DC2C90, 12 bytes long.  
 Data: <AES-128-GCM > 41 45 53 2D 31 32 38 2D 47 43 4D 00  
{11751} normal block at 0x000002D008DF0920, 240 bytes long.  
 Data: <                > 7F 03 00 00 01 00 00 00 10 00 00 00 0C 00 00 00  
{11749} normal block at 0x000002D008E488F0, 32 bytes long.  
 Data: < E              > 00 45 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11748} normal block at 0x000002D008DEEE80, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11747} normal block at 0x000002D008DEEC40, 128 bytes long.  
 Data: <                > 80 EE DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11746} normal block at 0x000002D008DF0440, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11745} normal block at 0x000002D008DE7FE0, 88 bytes long.  
 Data: <@       PR`     > 40 04 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11744} normal block at 0x000002D008E495B0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 F0 88 E4 08 D0 02 00 00  
{11743} normal block at 0x000002D008E48E90, 24 bytes long.  
 Data: < 3              > 02 33 00 00 00 00 00 00 B0 95 E4 08 D0 02 00 00  
{11742} normal block at 0x000002D008E04500, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11740} normal block at 0x000002D008DC2C40, 12 bytes long.  
 Data: <AES-192-GCM > 41 45 53 2D 31 39 32 2D 47 43 4D 00  
{11738} normal block at 0x000002D008DF0F10, 240 bytes long.  
 Data: <                > 82 03 00 00 01 00 00 00 18 00 00 00 0C 00 00 00  
{11736} normal block at 0x000002D008E47F30, 32 bytes long.  
 Data: <`I              > 60 49 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11735} normal block at 0x000002D008DEF840, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11734} normal block at 0x000002D008DF0380, 128 bytes long.  
 Data: <@               > 40 F8 DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11733} normal block at 0x000002D008DEF780, 128 bytes long.  
 Data: < 7              > B0 37 84 0A D0 02 00 00 00 00 00 00 00 00 00 00  
{11732} normal block at 0x000002D008DE9200, 88 bytes long.  
 Data: <        PR`     > 80 F7 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11731} normal block at 0x000002D008E47ED0, 32 bytes long.  
 Data: <        0       > 01 00 00 00 00 00 00 00 30 7F E4 08 D0 02 00 00  
{11730} normal block at 0x000002D008E47D50, 24 bytes long.  
 Data: < $       ~      > 02 24 00 00 00 00 00 00 D0 7E E4 08 D0 02 00 00  
{11729} normal block at 0x000002D008E04960, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11727} normal block at 0x000002D008DC2BF0, 12 bytes long.  
 Data: <AES-256-GCM > 41 45 53 2D 32 35 36 2D 47 43 4D 00  
{11725} normal block at 0x000002D008DF1AF0, 240 bytes long.  
 Data: <                > 85 03 00 00 01 00 00 00 20 00 00 00 0C 00 00 00  
{11723} normal block at 0x000002D008E47390, 32 bytes long.  
 Data: < D              > 90 44 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11722} normal block at 0x000002D008DEE880, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11721} normal block at 0x000002D008DF02C0, 128 bytes long.  
 Data: <                > 80 E8 DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11720} normal block at 0x000002D008DF0140, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11719} normal block at 0x000002D008DE93E0, 88 bytes long.  
 Data: <@       PR`     > 40 01 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11718} normal block at 0x000002D008E47CF0, 32 bytes long.  
 Data: <         s      > 01 00 00 00 00 00 00 00 90 73 E4 08 D0 02 00 00  
{11717} normal block at 0x000002D008E47330, 24 bytes long.  
 Data: <         |      > 02 8A 00 00 00 00 00 00 F0 7C E4 08 D0 02 00 00  
{11716} normal block at 0x000002D008E04490, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11714} normal block at 0x000002D008DC32D0, 16 bytes long.  
 Data: <AES-256-GCM-SIV > 41 45 53 2D 32 35 36 2D 47 43 4D 2D 53 49 56 00  
{11712} normal block at 0x000002D008DF07F0, 240 bytes long.  
 Data: <                > 00 00 00 00 01 00 00 00 20 00 00 00 0C 00 00 00  
{11711} normal block at 0x000002D008E472D0, 24 bytes long.  
 Data: < 3              > 20 33 DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11710} normal block at 0x000002D008DC33C0, 16 bytes long.  
 Data: <AES-256-GCM-SIV > 41 45 53 2D 32 35 36 2D 47 43 4D 2D 53 49 56 00  
{11709} normal block at 0x000002D008DC3320, 16 bytes long.  
 Data: < 3              > C0 33 DC 08 D0 02 00 00 8A 00 00 00 00 00 00 00  
{11707} normal block at 0x000002D008E471B0, 32 bytes long.  
 Data: <p>              > 70 3E E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11706} normal block at 0x000002D008DEF540, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11705} normal block at 0x000002D008DEF300, 128 bytes long.  
 Data: <@               > 40 F5 DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11704} normal block at 0x000002D008DEF480, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11703} normal block at 0x000002D008DE7C20, 88 bytes long.  
 Data: <        PR`     > 80 F4 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11702} normal block at 0x000002D008E47C90, 32 bytes long.  
 Data: <         q      > 01 00 00 00 00 00 00 00 B0 71 E4 08 D0 02 00 00  
{11701} normal block at 0x000002D008E47150, 24 bytes long.  
 Data: <         |      > 02 89 00 00 00 00 00 00 90 7C E4 08 D0 02 00 00  
{11700} normal block at 0x000002D008E03E70, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11698} normal block at 0x000002D008DC2F60, 16 bytes long.  
 Data: <AES-192-GCM-SIV > 41 45 53 2D 31 39 32 2D 47 43 4D 2D 53 49 56 00  
{11696} normal block at 0x000002D008DF13D0, 240 bytes long.  
 Data: <                > 00 00 00 00 01 00 00 00 18 00 00 00 0C 00 00 00  
{11695} normal block at 0x000002D008E48890, 24 bytes long.  
 Data: < 2              > 80 32 DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11694} normal block at 0x000002D008DC2F10, 16 bytes long.  
 Data: <AES-192-GCM-SIV > 41 45 53 2D 31 39 32 2D 47 43 4D 2D 53 49 56 00  
{11693} normal block at 0x000002D008DC3280, 16 bytes long.  
 Data: < /              > 10 2F DC 08 D0 02 00 00 89 00 00 00 00 00 00 00  
{11691} normal block at 0x000002D008E47270, 32 bytes long.  
 Data: < C              > B0 43 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11690} normal block at 0x000002D008DEFD80, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11689} normal block at 0x000002D008DEEDC0, 128 bytes long.  
 Data: <                > 80 FD DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11688} normal block at 0x000002D008DEE580, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11687} normal block at 0x000002D008DE89E0, 88 bytes long.  
 Data: <        PR`     > 80 E5 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11686} normal block at 0x000002D008E486B0, 32 bytes long.  
 Data: <        pr      > 01 00 00 00 00 00 00 00 70 72 E4 08 D0 02 00 00  
{11685} normal block at 0x000002D008E47C30, 24 bytes long.  
 Data: <                > 02 88 00 00 00 00 00 00 B0 86 E4 08 D0 02 00 00  
{11684} normal block at 0x000002D008E043B0, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11682} normal block at 0x000002D008DC31E0, 16 bytes long.  
 Data: <AES-128-GCM-SIV > 41 45 53 2D 31 32 38 2D 47 43 4D 2D 53 49 56 00  
{11680} normal block at 0x000002D008DF19C0, 240 bytes long.  
 Data: <                > 00 00 00 00 01 00 00 00 10 00 00 00 0C 00 00 00  
{11679} normal block at 0x000002D008E476F0, 24 bytes long.  
 Data: <02              > 30 32 DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11678} normal block at 0x000002D008DC2B00, 16 bytes long.  
 Data: <AES-128-GCM-SIV > 41 45 53 2D 31 32 38 2D 47 43 4D 2D 53 49 56 00  
{11677} normal block at 0x000002D008DC3230, 16 bytes long.  
 Data: < +              > 00 2B DC 08 D0 02 00 00 88 00 00 00 00 00 00 00  
{11675} normal block at 0x000002D008E47690, 32 bytes long.  
 Data: < A              > 10 41 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11674} normal block at 0x000002D008DEFCC0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11673} normal block at 0x000002D008DEF3C0, 128 bytes long.  
 Data: <                > C0 FC DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11672} normal block at 0x000002D008DEF000, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11671} normal block at 0x000002D008DE88A0, 88 bytes long.  
 Data: <        PR`     > 00 F0 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11670} normal block at 0x000002D008E48650, 32 bytes long.  
 Data: <         v      > 01 00 00 00 00 00 00 00 90 76 E4 08 D0 02 00 00  
{11669} normal block at 0x000002D008E47630, 24 bytes long.  
 Data: <        P       > 02 87 00 00 00 00 00 00 50 86 E4 08 D0 02 00 00  
{11668} normal block at 0x000002D008E04110, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11666} normal block at 0x000002D008DC1CA0, 12 bytes long.  
 Data: <AES-256-SIV > 41 45 53 2D 32 35 36 2D 53 49 56 00  
{11664} normal block at 0x000002D008DF1890, 240 bytes long.  
 Data: <        @       > 00 00 00 00 01 00 00 00 40 00 00 00 00 00 00 00  
{11663} normal block at 0x000002D008E47450, 24 bytes long.  
 Data: <                > E0 1D DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11662} normal block at 0x000002D008DC2A60, 12 bytes long.  
 Data: <AES-256-SIV > 41 45 53 2D 32 35 36 2D 53 49 56 00  
{11661} normal block at 0x000002D008DC1DE0, 16 bytes long.  
 Data: <`*              > 60 2A DC 08 D0 02 00 00 87 00 00 00 00 00 00 00  
{11659} normal block at 0x000002D008E48350, 32 bytes long.  
 Data: <`;              > 60 3B E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11658} normal block at 0x000002D008DEFB40, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11657} normal block at 0x000002D008DEFF00, 128 bytes long.  
 Data: <@               > 40 FB DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11656} normal block at 0x000002D008DF0080, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11655} normal block at 0x000002D008DE8940, 88 bytes long.  
 Data: <        PR`     > 80 00 DF 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11654} normal block at 0x000002D008E47DB0, 32 bytes long.  
 Data: <        P       > 01 00 00 00 00 00 00 00 50 83 E4 08 D0 02 00 00  
{11653} normal block at 0x000002D008E47BD0, 24 bytes long.  
 Data: <         }      > 02 86 00 00 00 00 00 00 B0 7D E4 08 D0 02 00 00  
{11652} normal block at 0x000002D008E03B60, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11650} normal block at 0x000002D008DC1B10, 12 bytes long.  
 Data: <AES-192-SIV > 41 45 53 2D 31 39 32 2D 53 49 56 00  
{11648} normal block at 0x000002D008DF0CB0, 240 bytes long.  
 Data: <        0       > 00 00 00 00 01 00 00 00 30 00 00 00 00 00 00 00  
{11647} normal block at 0x000002D008E470F0, 24 bytes long.  
 Data: <                > B0 1B DC 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11646} normal block at 0x000002D008DC1C00, 12 bytes long.  
 Data: <AES-192-SIV > 41 45 53 2D 31 39 32 2D 53 49 56 00  
{11645} normal block at 0x000002D008DC1BB0, 16 bytes long.  
 Data: <                > 00 1C DC 08 D0 02 00 00 86 00 00 00 00 00 00 00  
{11643} normal block at 0x000002D008E484D0, 32 bytes long.  
 Data: < :              > 80 3A E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11642} normal block at 0x000002D008DEE7C0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11641} normal block at 0x000002D008DEFA80, 128 bytes long.  
 Data: <                > C0 E7 DE 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11640} normal block at 0x000002D008DEFE40, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11639} normal block at 0x000002D008DE7AE0, 88 bytes long.  
 Data: <@       PR`     > 40 FE DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11638} normal block at 0x000002D008E47B70, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00 D0 84 E4 08 D0 02 00 00  
{11637} normal block at 0x000002D008E482F0, 24 bytes long.  
 Data: <        p{      > 02 85 00 00 00 00 00 00 70 7B E4 08 D0 02 00 00  
{11636} normal block at 0x000002D008E03A80, 40 bytes long.  
 Data: < 5      pH      > E0 35 E1 08 D0 02 00 00 70 48 E4 08 D0 02 00 00  
{11634} normal block at 0x000002D008DC1890, 12 bytes long.  
 Data: <AES-128-SIV > 41 45 53 2D 31 32 38 2D 53 49 56 00  
{11632} normal block at 0x000002D008DF1630, 240 bytes long.  
 Data: <                > 00 00 00 00 01 00 00 00 20 00 00 00 00 00 00 00  
{11631} normal block at 0x000002D008E48830, 24 bytes long.  
 Data: <         j      > E0 18 DC 08 D0 02 00 00 B0 6A 84 0A D0 02 00 00  
{11630} normal block at 0x000002D008DC1980, 12 bytes long.  
 Data: <AES-128-SIV > 41 45 53 2D 31 32 38 2D 53 49 56 00  
{11629} normal block at 0x000002D008DC18E0, 16 bytes long.  
 Data: <                > 80 19 DC 08 D0 02 00 00 85 00 00 00 00 00 00 00  
{11627} normal block at 0x000002D008E485F0, 32 bytes long.  
 Data: < D              > 20 44 E0 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11626} normal block at 0x000002D008DF0200, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11625} normal block at 0x000002D008DEEF40, 128 bytes long.  
 Data: <                > 00 02 DF 08 D0 02 00 00 00 00 00 00 00 00 00 00  
{11624} normal block at 0x000002D008DEF9C0, 128 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11623} normal block at 0x000002D008DE79A0, 88 bytes long.  
 Data: <        PR`     > C0 F9 DE 08 D0 02 00 00 50 52 60 9E F6 7F 00 00  
{11622} normal block at 0x000002D008E475D0, 32 bytes long.  
 Data: <                > 01 00 00 00 00 00 00 00

---

## Comment 16

> Username: anarthal  
> Created at: 2024-08-10 09:51:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2280705587  

Okay, this didn't add any information. But that's because you defined `_CRTDBG_MAP_ALLOC` after including your own headers, so any allocations made in files included before the macro was defined are probably not tracked. Also, Boost.Test will do `_CrtDumpMemoryLeaks` for you. Doing it in your test case will report false positives, since there's memory that hasn't been released yet at that point.  
  
My suggestion to move forward:  
* Remove any `_CrtXXXXX` calls from your code.  
* Define `_CRTDBG_MAP_ALLOC` first thing in your test. The easiest way to do it is to add a `target_compile_definitions(you_executable PRIVATE _CRTDBG_MAP_ALLOC)` command in your CMake.  
  
Sorry that I didn't explain myself better in my previous post.  
  
Regards,  
Ruben.

---

## Comment 17

> Username: sagunkho  
> Created at: 2024-08-10 12:03:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2281335721  

Thanks for the reply.  
I added the `target_compile_definitions` line to the cmake file and removed  
the _Crt calls from the code. The output was similar and not any different.  
*** No errors detected  
Detected memory leaks!  
Dumping objects ->  
{16193} normal block at 0x000001DEC38A2E80, 3 bytes long.  
 Data: <>  > 3E 1C 20  
{14740} normal block at 0x000001DEC38A09B0, 448 bytes long.  
 Data: <        \   l   > 04 1C B4 05 C5 A2 9C B6 5C F1 A5 09 6C A1 C4 AE  
{14739} normal block at 0x000001DEC38A07B0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14738} normal block at 0x000001DEC38A06B0, 184 bytes long.  
 Data: < =              > F0 3D D8 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14737} normal block at 0x000001DEC38A05B0, 184 bytes long.  
 Data: < U              > 80 55 D9 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14735} normal block at 0x000001DEC38A04C0, 176 bytes long.  
 Data: <                > B0 05 8A C3 DE 01 00 00 B0 06 8A C3 DE 01 00 00  
{14734} normal block at 0x000001DEC1D44160, 296 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 20 B5 DC C1 DE 01 00 00  
{14733} normal block at 0x000001DEC389B3E0, 40 bytes long.  
 Data: <@       `A      > 40 F0 D0 C1 DE 01 00 00 60 41 D4 C1 DE 01 00 00  
{14730} normal block at 0x000001DEC38A02C0, 448 bytes long.  
 Data: <F "D        qD  > 46 B3 22 44 E2 AA C3 09 CA 9F E0 A6 71 44 9A DC  
{14729} normal block at 0x000001DEC38A00C0, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14728} normal block at 0x000001DEC389FD80, 184 bytes long.  
 Data: < =              > F0 3D D8 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14727} normal block at 0x000001DEC389FC80, 184 bytes long.  
 Data: < U              > 80 55 D9 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14725} normal block at 0x000001DEC389FB90, 176 bytes long.  
 Data: <                > 80 FC 89 C3 DE 01 00 00 80 FD 89 C3 DE 01 00 00  
{14724} normal block at 0x000001DEC1D442D0, 296 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 20 B5 DC C1 DE 01 00 00  
{14723} normal block at 0x000001DEC389C8E0, 40 bytes long.  
 Data: <@        B      > 40 F0 D0 C1 DE 01 00 00 D0 42 D4 C1 DE 01 00 00  
{14721} normal block at 0x000001DEC38950A0, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14718} normal block at 0x000001DEC389F990, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14717} normal block at 0x000001DEC389F890, 184 bytes long.  
 Data: < U              > 80 55 D9 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14716} normal block at 0x000001DEC389F690, 448 bytes long.  
 Data: < 6  7\ {  #A c m> A7 36 E1 AC 37 5C A1 7B 20 84 23 41 E3 63 05 6D  
{14715} normal block at 0x000001DEC389F490, 448 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{14714} normal block at 0x000001DEC1D85890, 184 bytes long.  
 Data: < =              > F0 3D D8 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14713} normal block at 0x000001DEC1D85790, 184 bytes long.  
 Data: < U              > 80 55 D9 C1 DE 01 00 00 00 00 00 00 00 00 00 00  
{14699} normal block at 0x000001DEC1D253A0, 176 bytes long.  
 Data: < W       X      > 90 57 D8 C1 DE 01 00 00 90 58 D8 C1 DE 01 00 00  
{14698} normal block at 0x000001DEC1D43FF0, 296 bytes long.  
 Data: < P              > A0 50 89 C3 DE 01 00 00 20 B5 DC C1 DE 01 00 00  
{14697} normal block at 0x000001DEC389B5A0, 40 bytes long.  
 Data: <@        ?      > 40 F0 D0 C1 DE 01 00 00 F0 3F D4 C1 DE 01 00 00  
{14690} normal block at 0x000001DEC3895B40, 16 bytes long.  
 Data: <                > 20 B5 DC C1 DE 01 00 00 01 00 00 00 00 00 00 00  
{14689} normal block at 0x000001DEC389B610, 40 bytes long.  
 Data: <P       @[      > 50 E0 D0 C1 DE 01 00 00 40 5B 89 C3 DE 01 00 00  
{14657} normal block at 0x000001DEC3895690, 9 bytes long.  
 Data: <SEED-SRC > 53 45 45 44 2D 53 52 43 00  
{14656} normal block at 0x000001DEC1D0E050, 200 bytes long.  
 Data: <`               > 60 1F D9 C1 DE 01 00 00 C4 00 00 00 00 00 00 00  
{14612} normal block at 0x000001DEC38958C0, 9 bytes long.  
 Data: <CTR-DRBG > 43 54 52 2D 44 52 42 47 00  
{14611} normal block at 0x000001DEC1D0F040, 200 bytes long.  
 Data: <`               > 60 1F D9 C1 DE 01 00 00 C1 00 00 00 00 00 00 00  
{13857} normal block at 0x000001DEC1D729C0, 84 bytes long.  
 Data: < lobal default l> 00 6C 6F 62 61 6C 20 64 65 66 61 75 6C 74 20 6C  
{11535} normal block at 0x000001DEC1D248A0, 12 bytes long.  
 Data: <AES-256-CTR > 41 45 53 2D 32 35 36 2D 43 54 52 00  
{11533} normal block at 0x000001DEC1D83DF0, 240 bytes long.  
 Data: <                > 8A 03 00 00 01 00 00 00 20 00 00 00 10 00 00 00  
{11218} normal block at 0x000001DEC1D430E0, 12 bytes long.  
 Data: <AES-256-ECB > 41 45 53 2D 32 35 36 2D 45 43 42 00  
{11216} normal block at 0x000001DEC1D95580, 240 bytes long.  
 Data: <                > AA 01 00 00 10 00 00 00 20 00 00 00 00 00 00 00  
{11182} normal block at 0x000001DEC1D43090, 19 bytes long.  
 Data: <BIO to Core filt> 42 49 4F 20 74 6F 20 43 6F 72 65 20 66 69 6C 74  
{11181} normal block at 0x000001DEC1DADFB0, 112 bytes long.  
 Data: <         0      > 19 04 00 00 00 00 00 00 90 30 D4 C1 DE 01 00 00  
{11180} normal block at 0x000001DEC1DCB520, 24 bytes long.  
 Data: <`         pV    > 60 1F D9 C1 DE 01 00 00 20 8B 70 56 F6 7F 00 00  
{11179} normal block at 0x000001DEC1D42F50, 8 bytes long.  
 Data: <default > 64 65 66 61 75 6C 74 00  
{11178} normal block at 0x000001DEC1DCB4C0, 32 bytes long.  
 Data: <                > 00 00 00 00 CD CD CD CD 00 00 00 00 00 00 00 00  
{11177} normal block at 0x000001DEC1D42BE0, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11176} normal block at 0x000001DEC1D43360, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11175} normal block at 0x000001DEC1D37F70, 16 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
{11174} normal block at 0x000001DEC1D91F60, 224 bytes long.  
 Data: <         +      > 01 00 00 00 00 00 00 00 E0 2B D4 C1 DE 01 00 00  
{7796} normal block at 0x000001DEC1D937A0, 840 bytes long.  
 Data: <                > 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
Object dump complete.  
  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.www.avast.com  
<https://www.avast.com/sig-email?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Sat, Aug 10, 2024 at 3:22 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> Okay, this didn't add any information. But that's because you defined  
> _CRTDBG_MAP_ALLOC after including your own headers, so any allocations  
> made in files included before the macro was defined are probably not  
> tracked. Also, Boost.Test will do _CrtDumpMemoryLeaks for you. Doing it  
> in your test case will report false positives, since there's memory that  
> hasn't been released yet at that point.  
>  
> My suggestion to move forward:  
>  
>    - Remove any _CrtXXXXX calls from your code.  
>    - Define _CRTDBG_MAP_ALLOC first thing in your test. The easiest way  
>    to do it is to add a target_compile_definitions(you_executable PRIVATE  
>    _CRTDBG_MAP_ALLOC) command in your CMake.  
>  
> Sorry that I didn't explain myself better in my previous post.  
>  
> Regards,  
> Ruben.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2280705587>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGY2EZTZRVCZK5XYFGKTZQXPEFAVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDEOBQG4YDKNJYG4>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 18

> Username: anarthal  
> Created at: 2024-08-10 12:15:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2281404188  

Oh :( let's try isolating this a little bit, and just testing the database component alone. For instance, let's check if this produces any leak report:  
  
```cpp  
#include "Server/Auth/Auth.hpp"  
  
#define BOOST_TEST_MODULE DatabaseProcessTest  
#include <boost/test/included/unit_test.hpp>  
  
  
BOOST_AUTO_TEST_CASE(DatabaseProcessTest)  
{  
    // Feel free to change any of these values to what you see suit  
    std::string host = "127.0.0.1";  
    int port = 3306;  
    std::string user = "root";  
    std::string pass = "";  
    std::string database = "mydb";  
    int segment_number = -1;  
  
    // Create the component  
    boost::asio::io_context ctx;  
    DatabaseProcess proc;  
  
    // Use it  
    try {  
        proc.initialize(ctx, segment_number, host, port, user, pass, database);  
    } catch(const std::exception &e) {  
        std::cerr << "Exception caught: " << e.what() << std::endl;  
    }  
}  
```

---

## Comment 19

> Username: sagunkho  
> Created at: 2024-08-10 12:30:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2281476171  

It works! There was no leak produced. And all ran smoothly.  
I think there would be an issue with something else that is causing the  
leaks?  
  
On Sat, Aug 10, 2024 at 5:46 PM Anarthal (Rubén Pérez) <  
***@***.***> wrote:  
  
> Oh :( let's try isolating this a little bit, and just testing the database  
> component alone. For instance, let's check if this produces any leak report:  
>  
> #include "Server/Auth/Auth.hpp"  
>  
> #define BOOST_TEST_MODULE DatabaseProcessTest  
> #include <boost/test/included/unit_test.hpp>  
>  
> BOOST_AUTO_TEST_CASE(DatabaseProcessTest)  
> {  
>     // Feel free to change any of these values to what you see suit  
>     std::string host = "127.0.0.1";  
>     int port = 3306;  
>     std::string user = "root";  
>     std::string pass = "";  
>     std::string database = "mydb";  
>     int segment_number = -1;  
>  
>     // Create the component  
>     boost::asio::io_context ctx;  
>     DatabaseProcess proc;  
>  
>     // Use it  
>     try {  
>         proc.initialize(ctx, segment_number, host, port, user, pass, database);  
>     } catch(const std::exception &e) {  
>         std::cerr << "Exception caught: " << e.what() << std::endl;  
>     }  
> }  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mysql/issues/327#issuecomment-2281404188>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABBCGYYWDDKEUCO7L4AV6F3ZQYAA5AVCNFSM6AAAAABMB2MQHCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDEOBRGQYDIMJYHA>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 20

> Username: anarthal  
> Created at: 2024-08-10 12:35:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/327#issuecomment-2281502938  

Great! I'd say the leak is probably located in your codebase, and outside of `DatabaseProcess`. Something is keeping a `DatabaseProcess` alive and not deleting it correctly on termination.  
  
A potential cause can be a cyclic reference using a `std::shared_ptr`. These need to be broken manually.  
  
The process I'd follow is to attempt to write smaller tests with individual components until one of them spits out the leak.  
  
Also, I'd revise your usage of `std::shared_ptr`, in general. For instance, if the horizon framework makes the guarantee that any `MainframeComponent`'s are kept alive until the application exits, you're safe to change `get_connection` to:  
  
```cpp  
boost::mysql::tcp_ssl_connection& get_connection() { return _connection; }  
```  
  
`shared_ptr` can generate nasty stuff like the one you saw.  
  
I'll close the issue now. If you have any more trouble or questions, don't hesitate to ask.  
  
Regards,  
Ruben.
