# #163 - It no longer seems possible to build against both python2 and python3 [Closed]

> Username: jwakely  
> Created at: 2017-02-16 12:48:26 UTC  
> Updated at: 2018-05-24 08:58:06 UTC  
> Closed at: 2017-03-16 02:43:27 UTC  
> Url: https://github.com/boostorg/build/issues/163  

Initially reported at https://svn.boost.org/trac/boost/ticket/12515 and independently rediscovered while packaging Boost for Fedora 26. The Trac ticket says:  
  
> In Boost 1.62, specifying "python=3.5" is not working anymore: Always the first python entry in user-config.jam is selected. It seems to be related to a change in tools/build/src/tools/python.jam (line 905 ff), which appeared between 1.61.0 and 1.62.0.   
  
> It also means that it makes it impossible (or at least hard for me) to build libboost_python and libboost_python3 at one run. While both files are present, in fact they are both python2 bindings.   
  
The trac ticket says it's caused by https://github.com/boostorg/build/commit/78ffbe094400d277627b2c19ceb182d637b8baca  
  
In the Fedora package we have this in user-config.jam:  
  
````  
using python : 2.7 : /usr/bin/python2 : /usr/include/python2.7 : : : : ;  
using python : 3.6 : /usr/bin/python3 : /usr/include/python3.6m : : : : m ;  
````  
  
And then we invoke `b2` with `python=2.7` in the arguments, with this comment:  
  
````  
# The "python=2.*" bit tells jam that we want to _also_ build 2.*, not  
# just 3.*.  When omitted, it just builds for python 3 twice, once  
# calling the library libboost_python and once libboost_python3.  I  
# assume this is for backward compatibility for apps that are used to  
# linking against -lboost_python, for when 2->3 transition is  
# eventually done.  
````  
  
  
  
In previous versions this would cause `libboost_python.so` to link to `libpython2.7.so` and `libboost_python3.so` to link to `libpython3.6m.so` but now both libs link to `libpython2.7.so`  
  
Was this an intended consequence of 78ffbe094400d277627b2c19ceb182d637b8baca or accidental?  
  
Is there some other way to build against both Python2 and Python3, as linux distro packagers require?

---

## Comment 1

> Username: jwakely  
> Created at: 2017-02-16 19:17:04 UTC  
> Url: https://github.com/boostorg/build/issues/163#issuecomment-280429841  

Also for some reason the extra lines added by 78ffbe0 appear twice on the `master` branch, this merge added a second copy of them: https://github.com/boostorg/build/commit/df6712e674558eb3fd0c6d8dd26b35ed79b60f0b#diff-1421f920ac337b75f899a426084177d0L905  
  
That made my first attempt to revert the 78ffbe0 change fail, because I only removed one occurrence of those lines.  
  
Commenting out both occurrences restores the previous behaviour, which  is what I'll use for the Fedora packages.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2017-03-16 02:43:27 UTC  
> Url: https://github.com/boostorg/build/issues/163#issuecomment-286941820  

Many commits later.. It looks like this is fixed in develop/master.

---

## Comment 3

> Username: jwakely  
> Created at: 2017-07-01 16:10:44 UTC  
> Url: https://github.com/boostorg/build/issues/163#issuecomment-312440643  

I'm still seeing this with 1.64, are you sure it's fixed on master?

---

## Comment 4

> Username: Cdriko  
> Created at: 2018-05-24 08:58:06 UTC  
> Url: https://github.com/boostorg/build/issues/163#issuecomment-391641385  

it seem the same under ubuntu 18.04 :   
#https://github.com/mkeeter/antimony/issues/191
