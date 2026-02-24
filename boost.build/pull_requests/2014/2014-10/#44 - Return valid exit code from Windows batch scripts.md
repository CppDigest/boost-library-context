# #44 Return valid exit code from Windows batch scripts. [Closed]

> Username: jurko-gospodnetic  
> Created at: 2014-10-19 19:52:53 UTC  
> Updated at: 2021-10-02 22:35:42 UTC  
> Closed at: 2014-10-24 07:03:48 UTC  
> Url: https://github.com/boostorg/build/pull/44  

Technique used for setting Windows batch script exit codes in Boost.Build worked  
correctly when the script is called from a Windows shell process and then checked  
from inside that process. However, when run from a temporary shell process that  
needs to terminate after running the script, such a process would always return  
exit code 0.  
  
This prevented anyone automating those scripts from detecting their success/  
failure status by using their exit code without adding an additional batch script  
layer.  
  
For example, consider the following two script files:  
  
ret666.cmd: `exit /b 666`  
wrapper.cmd: `call ret666.cmd`  
  
They both 'should return the value 666' and when run directly from a `cmd.exe`  
console indeed both do (they set the shell process's `ERRORLEVEL` environment  
variable to 666). However, when run using `cmd /c <script-name>` running  
`ret666.cmd` causes the temporary `cmd.exe` process to exit with exit code 666,  
while running `wrapper.cmd` causes it to exit with exit code 0.

---

## Comment 1

> Username: jurko-gospodnetic  
> Created_at: 2014-10-19 21:36:59 UTC  
> Url: https://github.com/boostorg/build/pull/44#issuecomment-59666742  

After this is merged, a similar change should be done to bootstrap.bat in the root Boost library folder, but that is not stored in this repo.

---

## Comment 2

> Username: vprus  
> Created_at: 2014-10-24 07:03:48 UTC  
> Url: https://github.com/boostorg/build/pull/44#issuecomment-60351599  

Hi Jurko, welcome back! I've merged this, on the command line.

---

## Comment 3

> Username: jurko-gospodnetic  
> Created_at: 2014-10-24 07:24:46 UTC  
> Url: https://github.com/boostorg/build/pull/44#issuecomment-60353148  

Hi. Good to be back. :-)  
  
For anyone tracking down the changes from this pull request, they are included in commit f929602088e0f1cc57a5a94d8e554c071eca3734.  
  
Best regards,  
  Jurko Gospodnetić

---
