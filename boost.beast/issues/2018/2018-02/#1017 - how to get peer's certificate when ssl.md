# #1017 - how to get peer's certificate when ssl [Closed]

> Username: mozeat-sun  
> Created at: 2018-02-11 00:50:56 UTC  
> Updated at: 2018-02-27 16:39:04 UTC  
> Closed at: 2018-02-27 16:39:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1017  

hi vinnie  
    i develop a https server which needs to store peer's cert and trustchain info when ssl, where i did not find any method to get them from ssl::context ,is there any way to achieve ?  
  
  
  
  
  
  
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

> Username: vinniefalco  
> Created at: 2018-02-11 02:04:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1017#issuecomment-364712797  

If the `ssl::context` interface does not have the functionality you need, there is always the ability to get the native SSL handle and interact directly with OpenSSL.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-21 22:35:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1017#issuecomment-367500700  

Has this issue been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-27 16:39:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1017#issuecomment-368941654  

Validation of SSL certificates is the responsibility of Asio not Beast, so I'll go ahead and close this issue. Feel free to open a new one if you have questions or problems!
