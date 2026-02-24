# #414 - Use swap for reading messages [Closed]

> Username: vinniefalco  
> Created at: 2017-06-03 16:12:48 UTC  
> Updated at: 2017-06-12 03:19:22 UTC  
> Closed at: 2017-06-12 03:19:22 UTC  
> Url: https://github.com/boostorg/beast/issues/414  

it might be a little cleaner to swap the message instead of assigning it at the end of a read.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 03:19:22 UTC  
> Url: https://github.com/boostorg/beast/issues/414#issuecomment-307681570  

@pdimov and issue https://github.com/vinniefalco/Beast/issues/469 have changed my mind, read now move constructs the parser from the callers message and then move assigns into it when done.
