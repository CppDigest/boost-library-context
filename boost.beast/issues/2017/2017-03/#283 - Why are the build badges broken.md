# #283 - Why are the build badges broken? [Closed]

> Username: vinniefalco  
> Created at: 2017-03-21 19:01:44 UTC  
> Updated at: 2017-03-21 19:52:55 UTC  
> Closed at: 2017-03-21 19:52:55 UTC  
> Url: https://github.com/boostorg/beast/issues/283  

Can someone please tell me what happened? There were no changes to README.md but now the badges are broken...  
  
See the top of this page  
https://github.com/vinniefalco/Beast/blob/master/README.md

---

## Comment 1

> Username: HowardHinnant  
> Created at: 2017-03-21 19:29:29 UTC  
> Updated at: 2017-03-21 19:31:25 UTC  
> Url: https://github.com/boostorg/beast/issues/283#issuecomment-288192171  

I experienced a similar failure this morning.  I'm not sure of the cause.  I fixed it by trial and error with this:  https://github.com/HowardHinnant/date/commit/5eff31acdd1e1b67577d486155088951e63a70eb  
  
I suspect the newline around the link is probably the active magic.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-03-21 19:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/283#issuecomment-288194857  

So now you're also better than me at fixing Markdown...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-03-21 19:45:15 UTC  
> Updated at: 2017-03-21 19:51:36 UTC  
> Url: https://github.com/boostorg/beast/issues/283#issuecomment-288196256  

Testing:  
  
[![Join the chat at https://gitter.im/vinniefalco/Beast](https://badges.gitter.im/vinniefalco/Beast.svg)](https://gitter.im/vinniefalco/Beast?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![Build Status](https://travis-ci.org/vinniefalco/Beast.svg?branch=master)](https://travis-ci.org/vinniefalco/Beast) [![codecov](https://codecov.io/gh/vinniefalco/Beast/branch/master/graph/badge.svg)](https://codecov.io/gh/vinniefalco/Beast) [![coveralls](https://coveralls.io/repos/github/vinniefalco/Beast/badge.svg?branch=master)](https://coveralls.io/github/vinniefalco/Beast?branch=master) [![Documentation](https://img.shields.io/badge/documentation-master-brightgreen.svg)](http://vinniefalco.github.io/beast/) [![License](https://img.shields.io/badge/license-boost-brightgreen.svg)](LICENSE_1_0.txt)  
  
This is promising :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-03-21 19:51:18 UTC  
> Url: https://github.com/boostorg/beast/issues/283#issuecomment-288197852  

WEW LAD Removing the newlines fixed it! Unfortunate, because if you have a whole row of badges they have to all go on the same line of markdown.
