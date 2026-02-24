# #2604 - Is this fail function used or was it the start of an idea? [Closed]

> Username: 101glover  
> Created at: 2022-12-23 17:36:53 UTC  
> Updated at: 2023-01-24 01:55:07 UTC  
> Closed at: 2023-01-24 01:55:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2604  

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
> Created at: 2022-12-23 17:39:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2604#issuecomment-1364141373  

What exactly is the issue you are reporting?

---

## Comment 2

> Username: sehe  
> Created at: 2022-12-23 17:41:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2604#issuecomment-1364142429  

The fail function at https://github.com/boostorg/beast/blob/develop/example/http/client/awaitable/http_client_awaitable.cpp#L42  is a left-over that is a staple in many of the examples, but indeed not used in this particular example.

---

## Comment 3

> Username: 101glover  
> Created at: 2022-12-23 17:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2604#issuecomment-1364143234  

Apologize for fat fingering the title. I am having a typing issues. Thanks, understand it's just cruft. Was just wondering if I was missing something. Feel bad asking about something so trivial now.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-12-23 17:42:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2604#issuecomment-1364143301  

Whoever ported this example did so incorrectly, because it uses exceptions rather than error codes. The `fail` function is used to report the failure from an `error_code`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-12-23 17:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2604#issuecomment-1364143559  

> Feel bad asking about something so trivial now.  
  
First of all its not trivial and second of all, congrats - you found an actionable defect :)
