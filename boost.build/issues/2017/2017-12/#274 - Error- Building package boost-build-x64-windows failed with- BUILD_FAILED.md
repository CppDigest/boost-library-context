# #274 - Error: Building package boost-build:x64-windows failed with: BUILD_FAILED [Closed]

> Username: Vehemos  
> Created at: 2017-12-21 18:56:32 UTC  
> Updated at: 2017-12-21 22:18:15 UTC  
> Closed at: 2017-12-21 22:18:15 UTC  
> Url: https://github.com/boostorg/build/issues/274  

Exact issue: https://github.com/Microsoft/vcpkg/issues/2415  
Pasting the contents below:  
__________________________________________________________________________________________________________  
[bootstrap.log](https://github.com/Microsoft/vcpkg/files/1578634/bootstrap.log)  
[bootstrap-x64-windows-err.log](https://github.com/Microsoft/vcpkg/files/1577063/bootstrap-x64-windows-err.log)  
[bootstrap-x64-windows-out.log](https://github.com/Microsoft/vcpkg/files/1577064/bootstrap-x64-windows-out.log)  
-- Testing integrity of cached file... OK  
-- Bootstrapping...  
CMake Error at scripts/cmake/vcpkg_execute_required_process.cmake:44 (message):  
    Command failed: C:/vcpkg/packages/boost-build_x64-windows/tools/boost-build/bootstrap.bat;msvc  
    Working Directory: C:/vcpkg/packages/boost-build_x64-windows/tools/boost-build  
    See logs for more information:  
      C:\vcpkg\buildtrees\boost-build\bootstrap-x64-windows-out.log  
      C:\vcpkg\buildtrees\boost-build\bootstrap-x64-windows-err.log  
  
Call Stack (most recent call first):  
  ports/boost-build/portfile.cmake:47 (vcpkg_execute_required_process)  
  scripts/ports.cmake:72 (include)  
  
  
Error: Building package boost-build:x64-windows failed with: BUILD_FAILED  
Please ensure you're using the latest portfiles with `.\vcpkg update`, then  
submit an issue at https://github.com/Microsoft/vcpkg/issues including:  
  Package: boost-build:x64-windows  
  Vcpkg version: 0.0.100-2017-12-19-6cb6a61aaf5ef2c143f974e9f731778bcd3f5cbe  
  
Additionally, attach any relevant sections from the log files above.  
  
==========  
  
I am running 64bit win10 Education version.  
  
What to do? :(  
  
Thank You!  
  
__________________________________________________________________________________________________________  
Also if you read the logs, manually creating the directory (i.e. md "bin.ntx86_64") works

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-21 19:53:15 UTC  
> Url: https://github.com/boostorg/build/issues/274#issuecomment-353442727  

AMDG  
  
On 12/21/2017 11:56 AM, Sarthak Tripathi wrote:  
> [MKDIR] bin.ntx86_64  
>'C:\Users\Blue' is not recognized as an internal or external command, operable program or batch file.  
>  
>    md "bin.ntx86_64"  
  
What do you get if you add -d1 -d+4 to the jam0 command?  
This should show the exact options passed to CreateProcess.  
I suspect the temporary batch script is not correctly quoted.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: Vehemos  
> Created at: 2017-12-21 19:56:36 UTC  
> Url: https://github.com/boostorg/build/issues/274#issuecomment-353443518  

Traveling atm with the laptop having the issue back at home, I will try  
later when I'm back in 2-3 days and will report. Thank You  
  
On 22-Dec-2017 1:23 am, "swatanabe" <notifications@github.com> wrote:  
  
> AMDG  
>  
> On 12/21/2017 11:56 AM, Sarthak Tripathi wrote:  
> > [MKDIR] bin.ntx86_64  
> >'C:\Users\Blue' is not recognized as an internal or external command,  
> operable program or batch file.  
> >  
> > md "bin.ntx86_64"  
>  
> What do you get if you add -d1 -d+4 to the jam0 command?  
> This should show the exact options passed to CreateProcess.  
> I suspect the temporary batch script is not correctly quoted.  
>  
> In Christ,  
> Steven Watanabe  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/build/issues/274#issuecomment-353442727>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALICaKzacCgP0vXbt_lL_9FEHnEsrj-iks5tCrcugaJpZM4RKN-U>  
> .  
>

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-12-21 20:07:51 UTC  
> Url: https://github.com/boostorg/build/issues/274#issuecomment-353445878  

AMDG  
  
On 12/21/2017 12:56 PM, Sarthak Tripathi wrote:  
> Traveling atm with the laptop having the issue back at home, I will try  
> later when I'm back in 2-3 days and will report. Thank You  
>   
  
I can reproduce the issue when %TEMP% points to a path  
containing a space.  
  
In Christ,  
Steven Watanabe
