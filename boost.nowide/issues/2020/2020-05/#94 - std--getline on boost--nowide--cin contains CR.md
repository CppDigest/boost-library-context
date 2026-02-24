# #94 - std::getline on boost::nowide::cin contains CR [Closed]

> Username: Alexander12365  
> Created at: 2020-05-04 11:34:02 UTC  
> Updated at: 2020-05-04 18:47:15 UTC  
> Closed at: 2020-05-04 18:47:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/94  

Dear Team of boost!  
  
I have an issue with the recently added library nowide.  
  
Compiler: tdm64-gcc-9.2.0.exe from https://jmeubank.github.io/tdm-gcc/ which is MinGW-w64 based  
OS: Windows 10 64bit  
Target: Building a Console Application using CodeBlocks.  
  
The problem arises when I am using getline during an endless loop with console input.  
If invalid user input is given with the CTRL-Key then the getline command keeps failing to read the following inputs.  
  
When I was using wcout, the issue was solved calling fflush(stdin) after an incorrect input which does not work when using boost::nowide.  
  
Here is an example source code (renamed extension from .cpp to .txt so I was able to upload it)  
[getline_issue.txt](https://github.com/boostorg/nowide/files/4574327/getline_issue.txt)  
  
Regards,  
Alexander Zinkl

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-05-04 12:27:38 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623434011  

Can you elaborate what you mean by "invalid user input is given with the CTRL-Key"? What exactly do you have to input into the console to trigger this bug?  
  
Will try to reproduce later when I'm back on my Win machine.

---

## Comment 2

> Username: Alexander12365  
> Created at: 2020-05-04 12:58:54 UTC  
> Updated at: 2020-05-04 13:01:00 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623448352  

When I am pressing Ctrl+Any other key to intentionally produce invalid input, such that the Console displays:  
![Ctrl+Other Keys](https://user-images.githubusercontent.com/128189/80967909-6cdc7280-8e17-11ea-8be2-4d58b48ae685.png)  
While Ctrl+C still works intendedly as the eof flag is set and I can return false in the endless loop.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-05-04 16:27:54 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623567265  

I was unable to reproduce this issue with MSVC 2019. However I noticed that contrary to `std::cin` control characters are not handled specially. So you see e.g. `\r` in the string and any other control character. Besides that the next input works fine  
  
Is that maybe the issue you see? You are comparing to `"1"` not to `"1\r"` which might be why it fails.  
  
Why do you think `getline` keeps failing? The example code doesn't check it

---

## Comment 4

> Username: Alexander12365  
> Created at: 2020-05-04 16:38:13 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623572762  

No the issue im seeing is that when I input something invalid with the Control key  + Another key that all following inputs are invalid (meaning the getline doesnt read what im actually inputting?)  
![invalid inputs](https://user-images.githubusercontent.com/128189/80990149-3b26d400-8e36-11ea-9e68-f333566778a8.png)

---

## Comment 5

> Username: Flamefire  
> Created at: 2020-05-04 16:40:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623573778  

And if you don't input something with the control key, does your program work?     
For me it does not because the input is actually "1\r" not "1"

---

## Comment 6

> Username: Alexander12365  
> Created at: 2020-05-04 16:41:54 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623574708  

Indeed I just noticed that, which is also different from my wcout solution.

---

## Comment 7

> Username: Alexander12365  
> Created at: 2020-05-04 16:45:27 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623576403  

Apparently the escape sequence is still contained within the string whereas previously it was not.

---

## Comment 8

> Username: Flamefire  
> Created at: 2020-05-04 16:47:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623577587  

Ok, then this is the issue. I'm uncertain what to do with control characters, they are returned by the underlying API call.  
  
I'll experiment a bit and will try to match the cin behavior on MSVC 2019

---

## Comment 9

> Username: Alexander12365  
> Created at: 2020-05-04 16:48:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623578066  

Yes I just fixed my issue by erasing the last character, which I did not have to do before. Thank you for your quick responses!

---

## Comment 10

> Username: Flamefire  
> Created at: 2020-05-04 18:34:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623632989  

Please make sure you only trim trailing `\r` characters, not blindly remove the last char. I just opened a PR which will ignore the \r characters to match the std::cin implementation

---

## Comment 11

> Username: Alexander12365  
> Created at: 2020-05-04 18:47:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/94#issuecomment-623638932  

Thanks, I just changed that.  
  
I am going to close this issue then.
