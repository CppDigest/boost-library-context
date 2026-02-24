# #204 - broken EOL in doc/concepts.qbk [Closed]

> Username: sfindeisen  
> Created at: 2019-07-27 18:48:05 UTC  
> Updated at: 2019-08-20 08:52:37 UTC  
> Closed at: 2019-08-20 08:52:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/204  

I am trying to install modularized Boost on my GNU/Linux by following [these instructions](https://github.com/boostorg/boost/wiki/Getting-Started). I do `git clone`, but then git reports modifications to `doc/concepts.qbk` file in this lib due to automatic EOL conversions.  
  
This is especially annoying when trying to check out Boost as a git submodule (because of uncommited changes propagating upwards).

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-07-29 22:10:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/204#issuecomment-516181850  

Thank you for reporting this. If I understand correctly, you want me to change the EOL characters in this file so git doesn't do automatic conversion, right? If you already know what needs to be done, feel free to submit a PR. Otherwise I will have to look into this.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-07-29 22:12:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/204#issuecomment-516182333  

Just to check, you did this: `git config --global core.autocrlf true`, right?

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-08-19 22:14:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/204#issuecomment-522774328  

@mloskot Do you have an idea how I could fix this? OP is not responding.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-08-20 08:25:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/204#issuecomment-522910582  

@hdembinski I don't think the OP set autocrlf true on Linux. It would be unusual.  
  
Any chance this case applies to your file? https://stackoverflow.com/a/39078611  
  
I'm travelling this week, having patchy Internet access and mostly on phone, hard to dig it deeper.
