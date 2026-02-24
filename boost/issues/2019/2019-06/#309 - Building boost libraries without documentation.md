# #309 - Building boost libraries without documentation [Open]

> Username: SubrataM3  
> Created at: 2019-06-27 15:27:31 UTC  
> Updated at: 2022-12-20 16:44:14 UTC  
> Url: https://github.com/boostorg/boost/issues/309  

Hi,  
  
I would like to build boost libraries without documentation files. with only header files and .so libraries.  
I have tried ./b2 --help or ./bootstrap --help but can't find any info there.  
Since i am using checkinstall to install it and there are lots of docs, it is taking a lot time to finish installation.  
So, kindly suggest how to exclude docs.  
  
Thanks.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-06-28 22:58:09 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-506900082  

AMDG  
  
On 6/27/19 9:27 AM, SubrataM3 wrote:  
> I would like to build boost libraries without documentation files. with only header files and .so libraries.  
> I have tried ./b2 --help or ./bootstrap --help but can't find any info there.  
> Since i am using checkinstall to install it and there are lots of docs, it is taking a lot time to finish installation.  
> So, kindly suggest how to exclude docs.  
>   
  
Running b2 in the root of the boost distribution does not  
build the docs.  In fact, you have to work extra hard to  
build the docs at all.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: SubrataM3  
> Created at: 2019-06-29 12:00:42 UTC  
> Updated at: 2019-06-29 12:01:22 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-506951887  

> AMDG  
  
Hi,  
  
You can see the screenshot below  
![shot_190629_171728](https://user-images.githubusercontent.com/15718377/60383703-16353800-9a92-11e9-9e01-adf7bc347926.png)  
I got those output with ./bootstrap.sh followed by ./b2 and checkinstall ./b2 install   
I wanted to just avoid copying those files, not sure if you meant something else (Those docs are already in html, i think you meant building pdf from those html files?)

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-06-29 16:34:44 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-506970251  

AMDG  
  
On 6/29/19 6:00 AM, SubrataM3 wrote:  
>  
> You can see the screenshot below  
> ![shot_190629_171728](https://user-images.githubusercontent.com/15718377/60383703-16353800-9a92-11e9-9e01-adf7bc347926.png)  
> I got those output with ./bootstrap.sh followed by ./b2 and checkinstall ./b2 install   
> I wanted to avoid copying those files, not sure if you meant something else (Those docs are already in html, i think you meant html to pdf?)  
>   
  
I'm not familiar with checkinstall, but the output  
you're showing is definitely not from `b2 install`  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: catalinalb  
> Created at: 2019-12-01 12:23:18 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-560101268  

Is it so hard to write some normal installation procedures? From where should we know that after runnung ./b2 we should also type " ./b2 install" .... ?  
  
I was checking for standard make... cmake... etc   
  
Not even sure if now is it properly installed or not since I am trying to install gnuradio with pybombs and it tells me that the Detected version of Boost is too old.  Requested version was 1.67...  
  
So apparently is still not installed. What about the installation path? I did the installation in home directory.   
  
  
Please clarify the installation procedure!!!

---

## Comment 5

> Username: anshu-khare-design  
> Created at: 2020-01-13 13:12:52 UTC  
> Updated at: 2020-01-13 13:13:39 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-573656277  

Hi   
I have worked on this.  
You will have to generate a stage folder in the boost directory and link it to your IDE.  
If permission is granted I can pull a request on this issue.  
  
Thank You  
Anshu Khare

---

## Comment 6

> Username: arunchannaraju18  
> Created at: 2020-06-28 13:25:41 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-650757557  

@swatanabe @SubrataM3 @catalinalb @anshu-khare-design @biochimia   
  
i have dowloaded the zip file of boost how to install or build it??  
pls share the commads

---

## Comment 7

> Username: sneurlax  
> Created at: 2022-12-20 16:43:59 UTC  
> Updated at: 2022-12-20 16:44:14 UTC  
> Url: https://github.com/boostorg/boost/issues/309#issuecomment-1359702634  

I'd also like to do the same.  I'm cross-compiling as in  
  
```  
./bootstrap.sh \  
	--prefix="x86_64" \  
	--with-toolset=gcc  
  
./b2 \  
	cxxflags=-fPIC \  
	cflags=-fPIC \  
	variant=release \  
	--layout=tagged \  
	--verbose \  
	--build-type=minimal \  
	--user-config=user-config.jam \  
	link=static \  
	runtime-link=static \  
	--with-chrono \  
	--with-date_time \  
	--with-filesystem \  
	--with-program_options \  
	--with-regex \  
	--with-serialization \  
	--with-system \  
	--with-thread \  
	--with-locale \  
	--build-dir=windows \  
	--stagedir=windows \  
	threading=multi \  
	threadapi=win32 \  
	binary-format=pe \  
	target-os=windows \  
	toolset=gcc-mingw \  
	-sICONV_PATH=${PREFIX} \  
	define=BOOST_USE_WINDOWS_H \  
	install  
```
