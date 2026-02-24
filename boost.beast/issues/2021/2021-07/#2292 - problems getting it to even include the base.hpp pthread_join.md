# #2292 - problems getting it to even include the base.hpp pthread_join [Closed]

> Username: tbandtg  
> Created at: 2021-07-27 13:47:49 UTC  
> Updated at: 2021-08-03 09:59:56 UTC  
> Closed at: 2021-08-03 09:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2292  

YOCTO Zeus  
Boost Beast.  
I wanted to use the websockets library included with this, and I noticed that Zeus already had it installed. But when I try to use the library I get linker errors for pthread.   
  
I can use pthread_t in my own program and not get them, but when I add in boost/beast I still get them. I feel like this should be super easy, but it is not.   
  
I am using autotools so I downloaded a ax_pthread.m4 and boost.m4 into the m4 directory but I do not know what I need to put into the configure.ac to force it to add the linker commands to build.  
  
This is all being cross compiled for arm the toolchain is setup properly, I can build and remote debug when I am not including boosts/beast  
  
AC_CONFIG_MACRO_DIR([m4])   
  
is the only thing I have added to the configure.ac  
  
  
Does anyone have a clue on how to force it to link in the pthread_join pthread_create etc etc that boost/beast seems to require?  
  
I searched and searched and found absolutely no exact way to handle this.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-27 18:42:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-887744545  

I must admit that I'm no expert in autotools. It _sounds_ as if there might be a link order problem? I suspect you'd want the `-lpthreads` to appear somewhere near the end of the linker command line.  
  
It is actually not beast that needs pthreads, but boost.asio which is an implicit dependency.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-27 19:14:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-887767182  

How much experience do you have building and linking programs which use Boost.ASIO?

---

## Comment 3

> Username: tbandtg  
> Created at: 2021-07-27 19:30:05 UTC  
> Updated at: 2021-07-27 19:30:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-887776962  

I have none, none whatsoever. All of my experience has been on small arm core projects. I worked on one linux project 12 years ago. I am just looking for a way to implement a simple websockets library.   
  
I have been searching stack overflow and reddit for a solution, but honestly everyone points me at   
https://stackoverflow.com/questions/17055279/autotools-for-pthreads-not-setting-correct-linker-flags  
  
which I tried to follow, but I do not know enough about auto tools to know what I am doing wrong.   
  
Someone else suggested I use CMAKE, but the built in builder for eclipse does not work with CMAKE and yocto.   
  
So I am back to square one.   
  
I know that this library is installed, as I can find all of the headers for it in the toolchain.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-07-27 19:32:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-887778184  

I’ll ask around some grey-haired friends tomorrow morning for you. I think most people who know and use auto tools are retired or dead.

---

## Comment 5

> Username: tbandtg  
> Created at: 2021-07-28 13:53:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-888328259  

TYVM, I feel like I resemble that remark, I just do not do much linux work. I am working on a multiyear linux project now though so I guess that will change. And I guarantee in three years I will look at cortex work and not remember a single thing.

---

## Comment 6

> Username: mzimbres  
> Created at: 2021-07-29 06:05:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-888829285  

I believe the proper way to link to pthread in autotools is to use LDADD. For example  
https://github.com/mzimbres/smms/blob/e01032fb9b9bafe967621703a554f2f60572e6ed/Makefile.am#L53.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-08-03 09:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2292#issuecomment-891710624  

Can I go ahread and close this issue?
