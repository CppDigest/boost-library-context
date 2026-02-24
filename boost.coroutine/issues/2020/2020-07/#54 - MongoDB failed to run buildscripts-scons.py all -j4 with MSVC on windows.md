# #54 - MongoDB failed to run  buildscripts/scons.py all -j4 with MSVC on windows [Closed]

> Username: v-mingxi  
> Created at: 2020-07-31 09:47:58 UTC  
> Updated at: 2020-07-31 16:06:08 UTC  
> Closed at: 2020-07-31 16:06:08 UTC  
> Url: https://github.com/boostorg/coroutine/issues/54  

Environment:  
VS 2019 + Windows Server 2016 + MongoDB master branch latest revision  
  
MongoDB failed to run  buildscripts/scons.py all -j4 with MSVC on windows . It can be first reproduced on master revision 46af3f0. Could you help have a look about this issue? Thanks in advance!  
  
Steps to reproduce the behavior:  
1. git clone -c core.autocrlf=true https://github.com/mongodb/mongo D:\MongoDB\src  
2. Open a VS 2019 x64 command prompt and browse to D:\MongoDB\src  
3.Apply the three patches in the attachment in order： mongodb_addoption_bug943976.patch  mongodb_bug668740.patch mongodb_c2039,c3646.patch  
4. set VSCMD_SKIP_SENDTELEMETRY=1 & "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\Tools\VsDevCmd.bat" -host_arch=amd64 -arch=amd64  
5. pip3 install -r etc/pip/compile-requirements.txt   
6. set VSCMD_SKIP_SENDTELEMETRY=1 & "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\Tools\VsDevCmd.bat" -host_arch=amd64 -arch=amd64  
7. python buildscripts/scons.py all -j4   
  
[build.log](https://github.com/boostorg/coroutine/files/5006260/build.log)  
[patch.zip](https://github.com/boostorg/coroutine/files/5006288/patch.zip)  
  
Actual result:  
scons: Building targets ...  
scons: *** Do not know how to make File target `all' (F:\gitP\mongodb\mongo\all).  Stop.  
scons: building terminated because of errors.  
all failed: Do not know how to make File target `all' (F:\gitP\mongodb\mongo\all)

---

## Comment 1

> Username: olk  
> Created at: 2020-07-31 10:15:38 UTC  
> Url: https://github.com/boostorg/coroutine/issues/54#issuecomment-667048103  

MongoDB doesn't use boost.coroutine.  
No error/output from the compiler provided.
