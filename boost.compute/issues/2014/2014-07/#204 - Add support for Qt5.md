# #204 - Add support for Qt5 [Closed]

> Username: kylelutz  
> Created at: 2014-07-30 01:37:26 UTC  
> Updated at: 2014-08-05 15:23:07 UTC  
> Closed at: 2014-08-05 15:23:07 UTC  
> Url: https://github.com/boostorg/compute/issues/204  

Add support for Qt5 in the Boost.Compute/Qt interop code as well as the examples which use Qt.  
  
We can check for the Qt version at compile time using the [`QT_VERSION`](http://qt-project.org/doc/qt-5/qtglobal.html#QT_VERSION) macro (after including the `<QtGlobal>` header).

---

## Comment 1

> Username: f-koehler  
> Created at: 2014-07-30 08:58:35 UTC  
> Url: https://github.com/boostorg/compute/issues/204#issuecomment-50590223  

I think PR [#205](https://github.com/kylelutz/compute/pull/205) can do this. Maybe there is a more elegant and less hacky way.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-07-31 04:33:30 UTC  
> Url: https://github.com/boostorg/compute/issues/204#issuecomment-50711641  

Yeah, it should support both Qt4 and Qt5 (and probably default to Qt5 if both are installed).  
  
I thinking you should be able to do something like this:  
  
```  
# first try to find qt5  
find_package(Qt5Widgets QUIET)  
if(${Qt5Widgets_FOUND})  
  # use qt5  
else()  
  # no qt5, try to find qt4  
  find_package(Qt4 QUIET)  
  if(${Qt4_FOUND})  
    # use qt4  
  else()  
    message("Error: Could not find Qt")  
  endif()  
endif()  
```  
  
This is just one idea. There may be better ways.

---

## Comment 3

> Username: f-koehler  
> Created at: 2014-07-31 12:35:39 UTC  
> Url: https://github.com/boostorg/compute/issues/204#issuecomment-50752037  

I found it more sufficient to look for Qt4 first: [PR 207](https://github.com/kylelutz/compute/pull/207)

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-08-05 15:23:07 UTC  
> Url: https://github.com/boostorg/compute/issues/204#issuecomment-51213035  

Implemented in PR #211.
