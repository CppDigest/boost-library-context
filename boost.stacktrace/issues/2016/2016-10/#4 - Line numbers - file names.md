# #4 - Line numbers / file names [Closed]

> Username: bluescarni  
> Created at: 2016-10-15 23:58:44 UTC  
> Updated at: 2016-12-10 09:51:45 UTC  
> Closed at: 2016-12-10 09:51:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4  

Would it be possible to add information regarding the line numbers and the filenames related to the functions appearing in the stacktrace?

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-10-16 06:11:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4#issuecomment-254029743  

It is possible, but very hard to do for all the backends. I'll try to do that for Windows platform soon.

---

## Comment 2

> Username: bluescarni  
> Created at: 2016-10-16 08:50:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4#issuecomment-254035153  

Ok, thanks for the quick reply!  
  
Out of curiosity, what would be the options on linux/unix platforms? Would stacktrace need to depend on other 3rd party libraries like libdwarf/bfd?

---

## Comment 3

> Username: apolukhin  
> Created at: 2016-10-16 17:28:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4#issuecomment-254060700  

Do not know exactly, but in case of libdwarf/bfd it will be a separate backend (because of their license).  
  
libdwarf/bfd are GPL which may be unacceptable for some of the library users.

---

## Comment 4

> Username: eyalroz  
> Created at: 2016-11-21 09:44:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4#issuecomment-261890561  

@apolukhin : You could #ifdef -check their availability, so that's not of a problem I would think...

---

## Comment 5

> Username: apolukhin  
> Created at: 2016-12-10 09:51:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/4#issuecomment-266194346  

Done. There's still room for improvement, however the current solution works out-of-the-box
