# #472 - No contents under tools/build [Closed]

> Username: ktkim91  
> Created at: 2021-01-26 11:59:32 UTC  
> Updated at: 2021-01-27 05:45:30 UTC  
> Closed at: 2021-01-27 05:45:30 UTC  
> Url: https://github.com/boostorg/boost/issues/472  

Hi, There are no files/directories under tools/build :  
  
ll boost-boost-1.74.0/tools/build/  
total 8  
drwxr-xr-x 2 ekimkit eusers 4096 Aug 11 23:53 .  
drwxr-xr-x 14 ekimkit eusers 4096 Aug 11 23:53 ..  
  
  
However, there are some contents in boost-1.74.0 downloaded from http://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.zip :  
  
ll boost-1.74.0/tools/build/  
total 100  
drwxr-xr-x 7 ekimkit eusers 4096 Aug 11 23:57 .  
drwxr-xr-x 14 ekimkit eusers 4096 Aug 11 23:55 ..  
rw-rr- 1 ekimkit eusers 5498 Aug 11 23:57 CONTRIBUTING.adoc  
rw-rr- 1 ekimkit eusers 7282 Aug 11 23:57 Jamroot.jam  
rw-rr- 1 ekimkit eusers 1338 Aug 11 23:57 LICENSE.txt  
rw-rr- 1 ekimkit eusers 1426 Aug 11 23:57 README.adoc  
rw-rr- 1 ekimkit eusers 21141 Aug 11 23:57 azure-pipelines.yml  
rw-rr- 1 ekimkit eusers 267 Aug 11 23:57 boost-build.jam  
rw-rr- 1 ekimkit eusers 690 Aug 11 23:57 bootstrap.bat  
-rwxr-xr-x 1 ekimkit eusers 629 Aug 11 23:57 bootstrap.sh  
rw-rr- 1 ekimkit eusers 1111 Aug 11 23:57 bootstrap_vms.com  
drwxr-xr-x 4 ekimkit eusers 4096 Aug 12 00:02 doc  
drwxr-xr-x 24 ekimkit eusers 4096 Aug 11 23:57 example  
drwxr-xr-x 2 ekimkit eusers 4096 Aug 11 23:57 notes  
drwxr-xr-x 9 ekimkit eusers 4096 Aug 11 23:57 src  
drwxr-xr-x 14 ekimkit eusers 12288 Aug 11 23:57 test  
  
Is this reasonable? Why are there these differences?

---

## Comment 1

> Username: mclow  
> Created at: 2021-01-26 16:15:40 UTC  
> Url: https://github.com/boostorg/boost/issues/472#issuecomment-767653823  

I'm not sure what you're asking here.  Are you asking "Why did someone delete these files on my system?"

---

## Comment 2

> Username: ktkim91  
> Created at: 2021-01-27 05:17:25 UTC  
> Updated at: 2021-01-27 05:22:41 UTC  
> Url: https://github.com/boostorg/boost/issues/472#issuecomment-768037193  

Sorry for not enough explanation.  
  
I downloaded the version of 1.74.0 from two links, https://github.com/boostorg/boost/archive/boost-1.74.0.zip (this site) and http://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.zip respectively.  
  
and then I looked into directory of tools/build both and as I mentioned above, I saw that the contents (files/subdirectories) from github.com are empty and there are some contents from dl.bintray.com  
  
I wonder if these differences can be an issue.

---

## Comment 3

> Username: mclow  
> Created at: 2021-01-27 05:45:30 UTC  
> Url: https://github.com/boostorg/boost/issues/472#issuecomment-768047060  

The official releases are the ones on bintray (as linked to from boost.org).
