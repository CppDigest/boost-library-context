# #108 - Wrong Error Code and Description [Closed]

> Username: heretic13  
> Created at: 2016-12-29 16:02:44 UTC  
> Updated at: 2017-02-25 17:39:49 UTC  
> Closed at: 2017-02-25 17:39:49 UTC  
> Url: https://github.com/boostorg/thread/issues/108  

Platform - Windows.  
  
Make a simple program:  
  
void func1()  
{  
	boost::this_thread::sleep_for(boost::chrono::hours(10));  
}  
  
int main()  
{  
	_tsetlocale(LC_ALL, _T("Russian"));  
  
	boost::thread_group myThreadGroup;  
  
	try  
	{  
		while (true)  
		{  
			myThreadGroup.create_thread(func1);  
		}  
	}  
	catch (const boost::thread_resource_error& ex)  
	{  
		printf("thread_resource_error. what: %s, native_error: %d; message: %s, value: %d", ex.what(), ex.native_error(),  
			ex.code().message().c_str(), ex.code().value());  
	}  
  
	getchar();  
  
    return 0;  
}  
  
Locale may be set another. I set Russian locale to display error messages correctly.  
  
And I get Result:  
  
thread_resource_error. what: boost::thread_resource_error: Была сделана попытка загрузить программу, имеющую неверный формат, native_error: 11; message: Была сделана попытка загрузить программу, имеющую неверный формат, value: 11  
  
This is not the correct error code. Native error code=11. This is windows Error code ERROR_BAD_FORMAT.  
"An attempt was made to load a program with an incorrect format." (that displayed in russian language).  
This error occurs when the calling LoadLibrary () function, and does not create a thread.  
  
But! There is the following definition in errno.h : EAGAIN (11) Resource temporarily unavailable  
I think that this is what was meant.  
  
You should not write errno-error codes in "native error" field (since this is the place for Windows-based error for OS Windows), and then do not try to decipher this code.  
You can provide a mechanism to set and decryption errno-error codes.

---

## Comment 1

> Username: viboes  
> Created at: 2016-12-30 02:42:00 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269724796  

Could you tell me what do you get if you don't set the locale?

---

## Comment 2

> Username: viboes  
> Created at: 2016-12-30 02:51:27 UTC  
> Updated at: 2016-12-30 02:52:26 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269725479  

Could you tell me what is the result of  
```  
std::cout << boost::system::error_code(boost::system::errc::resource_unavailable_try_again, boost::system::system_category()).message();  
```

---

## Comment 3

> Username: heretic13  
> Created at: 2016-12-30 06:56:25 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269739052  

sometimes display a message like this:  
  
thread_resource_error. what: boost::thread_resource_error: Unknown error, native_error: 11; message: Unknown error, value: 11  
  
50/50 %  for this message and message that I posted above.  
  
If I do not put the locale displays the following message:  
thread_resource_error. what: boost::thread_resource_error: Была сделана попытка загрузить программу, имеющую неверный формат, native_error: 11; message: Была сделана попытка загрузить программу, имеющую неверный формат, value: 11  
But it is not displayed correctly. This is a Windows specific problem  conversion encoding CP1251 (system code page for Windows Russian version) to the CP866 (code page for the console of Windows Russian version). This does not apply to our mistake.

---

## Comment 4

> Username: heretic13  
> Created at: 2016-12-30 07:06:41 UTC  
> Updated at: 2016-12-30 07:08:50 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269739710  

std::cout << boost::system::error_code(boost::system::errc::resource_unavailable_try_again, boost::system::system_category()).message();  
  
display in Russian:  
Была сделана попытка загрузить программу, имеющую неверный формат  
  
This is equivalent in English:  
"An attempt was made to load a program with an incorrect format."  
  
This is not the right message within the thread startup.  
  
Maybe you need to put some flags in initialization boost::system::error_code that code resource_unavailable_try_again displayed as "Resource temporarily unavailable".

---

## Comment 5

> Username: viboes  
> Created at: 2016-12-30 09:05:23 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269748316  

As I can not see the English message without the locale I don't know if this is an error in your configuration or in Boost.System.  
Please, could you check it with the Boost.System maintainer?

---

## Comment 6

> Username: heretic13  
> Created at: 2016-12-30 09:21:19 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269749794  

You can take in the English version of Windows and check the output from the  
  
std::cout << boost::system::error_code(boost::system::errc::resource_unavailable_try_again, boost::system::system_category()).message();  
  
The problem is not related to the operating system language. The problem is related to the incorrect error code interpretation - Windows error codes are quite different from the value of errno error codes. They can not be mixed.

---

## Comment 7

> Username: heretic13  
> Created at: 2016-12-30 09:47:00 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269751971  

std::cout << boost::system::error_code(boost::system::errc::resource_unavailable_try_again, boost::system::**generic_category**()).message();  
  
boost::system::errc::resource_unavailable_try_again is   
  
 //  generic error_conditions  
    namespace errc  
    {  
      enum errc_t  
     {  
....  
resource_unavailable_try_again, //EAGAIN  
...  
     }  
  }  
  
Why do you use **system_category**() (object identifying errors originating from the operating system) when you use the error code from the category of **generic error_conditions**?

---

## Comment 8

> Username: viboes  
> Created at: 2016-12-31 15:00:55 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-269868601  

It was not clear why this error was in the generic_category to me. This was like it is now since the beginning of the Boost.Thread implementation. I will fix it.

---

## Comment 9

> Username: viboes  
> Created at: 2017-01-22 10:50:15 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-274323490  

See https://github.com/boostorg/thread/commit/23cff22e5a8ad297c9ea1ddacb2aafcf97b38ca7  
  
This should be part of the next boost 1.64 release.

---

## Comment 10

> Username: viboes  
> Created at: 2017-02-25 17:39:49 UTC  
> Url: https://github.com/boostorg/thread/issues/108#issuecomment-282499373  

https://github.com/boostorg/thread/commit/336259c36a21d3d67dd71702ca0a6ef2d01e67d2#diff-ff20141995580da658ba4784eb8e942fL43
