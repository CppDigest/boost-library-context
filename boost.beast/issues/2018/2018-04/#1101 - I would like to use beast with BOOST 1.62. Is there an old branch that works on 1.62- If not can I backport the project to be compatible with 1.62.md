# #1101 - I would like to use beast with BOOST 1.62. Is there an old branch that works on 1.62? If not can I backport the project to be compatible with 1.62? [Closed]

> Username: imendel  
> Created at: 2018-04-22 13:26:35 UTC  
> Updated at: 2018-05-22 15:06:03 UTC  
> Closed at: 2018-05-22 15:06:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1101  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: imendel  
> Created at: 2018-04-22 13:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1101#issuecomment-383381679  

Hi Vinnie, I would like to use your library in my projects but currently I am using boost 1.62 version. Is there an old stable branch compatible with 1.62? Judging by the age of the project there should be.  
Thank you for your help.  
Igor

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-22 13:33:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1101#issuecomment-383381971  

You might have some luck with this version:  
https://github.com/boostorg/beast/tree/v124

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-22 13:41:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1101#issuecomment-390994395  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: imendel  
> Created at: 2018-05-22 15:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1101#issuecomment-391025482  

Thank you, we are using v124 and it works for us.  Apologies for not following up.

---

## Comment 5

> Username: imendel  
> Created at: 2018-05-22 15:06:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1101#issuecomment-391025662  

v124 works with Boost 1.62
