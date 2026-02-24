# #68 - Except for ssh.exe in openssh, any other console program runs very well. [Open]

> Username: ooseven1975  
> Created at: 2019-01-08 09:38:31 UTC  
> Updated at: 2019-01-09 07:06:42 UTC  
> Url: https://github.com/boostorg/process/issues/68  

```cpp  
namespace bp = boost::process;  
BOOST_AUTO_TEST_CASE(stackful, *boost::unit_test::timeout(1500))  
{  
	using boost::unit_test::framework::master_test_suite;  
	bool did_something_else = false;  
	boost::asio::io_service ios;  
	boost::process::async_pipe StdOutPipe(ios);  
	auto stackful =  
		[&](boost::asio::yield_context yield_)  
	{  
		int ret =  
			bp::async_system(  
				ios, yield_,boost::process::windows::hide,  
				"C:/Program Files/OpenSSH/bin/ssh.exe localhost "  
				,boost::process::std_out > StdOutPipe  
				);  
	};  
  
	boost::asio::spawn(ios, stackful);  
	ios.post([&] {did_something_else = true; });  
  
	ios.run();  
	BOOST_CHECK(did_something_else);  
}  
```  
  
program always crash at   
  
```  
file：win_iocp_io_service.ipp ：  
function：size_t win_iocp_io_service::do_one(bool block, boost::system::error_code& ec)  
line 390 : op->Internal = reinterpret_cast<ulong_ptr_t>(&result_ec.category());  
```  
  
OS：win10  
boost version：boost_1_69_0  
c++ compliler：vc2017 x86  
openssh version：setupssh-7.9p1-1  
  
help me if I did anything wrong;  
thank a lot！

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-01-08 10:12:39 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452244843  

Can you try moving adding quotes between the command and the argument, i.e. `"C:/Program Files/OpenSSH/bin/ssh.exe", "localhost "`?

---

## Comment 2

> Username: ooseven1975  
> Created at: 2019-01-08 10:16:23 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452246034  

> Can you try moving adding quotes between the command and the argument, i.e. `"C:/Program Files/OpenSSH/bin/ssh.exe", "localhost "`?  
  
		int ret =  
			bp::async_system(  
				ios, yield_,boost::process::windows::hide,  
				//"Z:/MyVc2017Example/boost_CRC_测试/Bin/boost_CRC_测试.exe"  
				//"C:/Program Files/OpenSSH/bin/ssh.exe localhost \"C:/iGORobot/AIEngine/GTP/leelaZero/leelaz_0.16.exe -g -w C:/iGORobot/AIEngine/GTP/leelaZero/15b_gx5b"  
				"C:/Program Files/OpenSSH/bin/ssh.exe"  
				,"localhost"  
				,boost::process::std_out > StdOutPipe  
				//, boost::process::std_out > buffer  
				);  
I tried but still crash at same place

---

## Comment 3

> Username: ooseven1975  
> Created at: 2019-01-08 10:19:24 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452246878  

Except ssh.exe in openssh, other console programs work well, including plink.exe in putty and any other console programs.

---

## Comment 4

> Username: ooseven1975  
> Created at: 2019-01-08 10:24:28 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452248447  

I tried to call ssh.exe through CreatePipe and CreateProcess of Win32 api, which worked well without any problems.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2019-01-08 10:38:03 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452252693  

I don't get the crash though - do you get an error message?  
Can you add a `try-catch` block around `run` and print the error to see if you get a more informative exception?

---

## Comment 6

> Username: ooseven1975  
> Created at: 2019-01-08 11:00:04 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452259079  

Thank you for your help. I'll try as you say right away.

---

## Comment 7

> Username: ooseven1975  
> Created at: 2019-01-08 11:20:03 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452264154  

No exceptions were caught and crashed directly.  
I have sent you an email (klemens.morgenstern@gmx.net) and thank you again for your kind help.

---

## Comment 8

> Username: ooseven1975  
> Created at: 2019-01-08 11:59:35 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452273971  

When I removed the sentence boost:: process:: std_out > StdOutPipe, the program worked well, no problem.  
I recorded the second video and sent it to your mailbox again.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2019-01-09 06:16:11 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452584333  

I can't watch the video, because even though I live in Asia, I can't read chinese, sadly.  
  
What happens if you move the declaration of `async_pipe` into the coroutine?

---

## Comment 10

> Username: ooseven1975  
> Created at: 2019-01-09 06:19:08 UTC  
> Updated at: 2019-01-09 07:06:42 UTC  
> Url: https://github.com/boostorg/process/issues/68#issuecomment-452584873  

If ssh.exe is executed in boost:: process in a synchronous manner, everything is OK. I think it's boost:: asio.
