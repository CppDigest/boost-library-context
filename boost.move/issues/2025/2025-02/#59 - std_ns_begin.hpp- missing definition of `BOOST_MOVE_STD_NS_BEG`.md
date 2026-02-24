# #59 - std_ns_begin.hpp: missing definition of `BOOST_MOVE_STD_NS_BEG` [Closed]

> Username: 0-wiz-0  
> Created at: 2025-02-18 09:18:47 UTC  
> Updated at: 2025-02-28 19:39:55 UTC  
> Closed at: 2025-02-28 16:22:52 UTC  
> Url: https://github.com/boostorg/move/issues/59  

In https://github.com/boostorg/move/commit/18b0f7e1813182da53690c697c0d4762efbf62ad due to https://github.com/boostorg/container/issues/292 the `std_ns_begin.hpp` header was changed.  
Now there are cases where `BOOST_MOVE_STD_NS_BEG` and `BOOST_MOVE_STD_NS_END` are not defined at all.  
  
I noticed because this [broke flightgear-2020.3 in pkgsrc](http://shadow.netbsd.org/pub/pkgsrc/packages/reports/HEAD/NetBSD-10.0-x86_64/20250216.2008/flightgear-2020.3.11nb54/build.log) with  
```  
In file included from /usr/pkg/qt5/include/QtCore/qglobal.h:1307,  
                 from /usr/pkg/qt5/include/QtGui/qtguiglobal.h:43,  
                 from /usr/pkg/qt5/include/QtGui/qwindow.h:43,  
                 from /usr/pkg/qt5/include/QtGui/QWindow:1,  
                 from /pbulk/work/games/flightgear/work/flightgear-2020.3.11/src/Viewer/GraphicsWindowQt5.hxx:21,  
                 from /pbulk/work/games/flightgear/work/flightgear-2020.3.11/src/Viewer/GraphicsWindowQt5.cpp:20:  
/usr/pkg/qt5/include/QtCore/qflags.h:123:80: note: declared here  
  123 |     QT_DEPRECATED_X("Use default constructor instead") Q_DECL_CONSTEXPR inline QFlags(Zero) noexcept : i(0) {}  
      |                                                                                ^~~~~~  
[ 96%] Building CXX object src/Main/CMakeFiles/fgfs.dir/__/Viewer/renderingpipeline.cxx.o  
[ 98%] Building CXX object src/Main/CMakeFiles/fgfs.dir/fg_commands.cxx.o  
[ 98%] Building CXX object src/Main/CMakeFiles/fgfs.dir/fg_init.cxx.o  
[ 98%] Building CXX object src/Main/CMakeFiles/fgfs.dir/fg_io.cxx.o  
[ 98%] Building CXX object src/Main/CMakeFiles/fgfs.dir/fg_os_common.cxx.o  
In file included from /usr/pkg/include/boost/container/container_fwd.hpp:65,  
                 from /usr/pkg/include/boost/lexical_cast/detail/converter_lexical.hpp:50,  
                 from /usr/pkg/include/boost/lexical_cast/try_lexical_convert.hpp:32,  
                 from /usr/pkg/include/boost/lexical_cast.hpp:33,  
                 from /pbulk/work/games/flightgear/work/flightgear-2020.3.11/src/Viewer/renderingpipeline.cxx:30:  
/usr/pkg/include/boost/container/detail/std_fwd.hpp:27:1: error: 'BOOST_MOVE_STD_NS_BEG' does not name a type  
   27 | BOOST_MOVE_STD_NS_BEG  
      | ^~~~~~~~~~~~~~~~~~~~~  
/usr/pkg/include/boost/container/detail/std_fwd.hpp:59:1: error: 'BOOST_MOVE_STD_NS_END' does not name a type; did you mean 'BOOST_MOVE_CATCH_END'?  
   59 | BOOST_MOVE_STD_NS_END  
      | ^~~~~~~~~~~~~~~~~~~~~  
      | BOOST_MOVE_CATCH_END  
```  
  
I've added `#error` statements to my local copy of the file to verify it is loaded, but none of the `if` cases trigger.  
The log is from NetBSD 10/x86_64 with gcc 10.5.0.  
I see the same problem on NetBSD 10.99.12/x86_64 with gcc 12.4.0.  
  
The commandline on the latter system is:  
```  
c++ -DHAVE_CONFIG_H -DQT_CORE_LIB -DQT_GUI_LIB -DQT_NETWORK_LIB -DQT_NO_DEBUG -DQT_QMLMODELS_LIB -DQT_QML_LIB -DQT_QUICK_LIB -DQT_SVG_LIB -DQT_WIDGETS_LIB -I/scratch/games/flightgear/work/flightgear-2020.3.11/fgbuild/src/Main/fgfs_autogen/include -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/iaxclient/lib -I/usr -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/sqlite3 -I/scratch/games/flightgear/work/flightgear-2020.3.11 -I/scratch/games/flightgear/work/flightgear-2020.3.11/src -I/scratch/games/flightgear/work/flightgear-2020.3.11/fgbuild/src -I/scratch/games/flightgear/work/flightgear-2020.3.11/fgbuild/src/Include -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/hts_engine_API/include -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/flite_hts_engine/include -I/scratch/games/flightgear/work/flightgear-2020.3.11/src/FDM/JSBSim -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/joystick -I/scratch/games/flightgear/work/flightgear-2020.3.11/3rdparty/fonts -isystem /usr/pkg/include -isystem /usr/pkg/qt5/include -isystem /usr/pkg/qt5/include/QtCore -isystem /usr/pkg/qt5/./mkspecs/netbsd-g++ -isystem /usr/pkg/qt5/include/QtWidgets -isystem /usr/pkg/qt5/include/QtGui -isystem /usr/pkg/qt5/include/QtNetwork -isystem /usr/pkg/qt5/include/QtQml -isystem /usr/pkg/qt5/include/QtQuick -isystem /usr/pkg/qt5/include/QtQmlModels -isystem /usr/pkg/qt5/include/QtSvg -isystem /usr/pkg/qt5/include/QtGui/5.15.16 -isystem /usr/pkg/qt5/include/QtGui/5.15.16/QtGui -isystem /usr/pkg/qt5/include/QtCore/5.15.16 -isystem /usr/pkg/qt5/include/QtCore/5.15.16/QtCore -O2 -g -fstack-clash-protection -pthread -I/usr/pkg/include -I/usr/include -I/usr/pkg/include/libdrm -I/usr/pkg/include/freetype2 -I/usr/pkg/include/lua-5.2 -I/usr/include/readline -I/usr/pkg/include/ffmpeg4 -I/usr/pkg/qt5/include -I/usr/pkg/include/glib-2.0 -I/usr/pkg/include/gio-unix-2.0 -I/usr/pkg/lib/glib-2.0/include -I/usr/pkg/include/harfbuzz -msse2 -mfpmath=sse -ftree-vectorize -ftree-slp-vectorize -Wall -D_REENTRANT -DBOOST_BIMAP_DISABLE_SERIALIZATION -DBOOST_NO_STDLIB_CONFIG -DNDEBUG -msse2 -mfpmath=sse -ftree-vectorize -ftree-slp-vectorize -std=gnu++11 -fPIC -MD -MT src/Main/CMakeFiles/fgfs.dir/__/Viewer/renderingpipeline.cxx.o -MF CMakeFiles/fgfs.dir/__/Viewer/renderingpipeline.cxx.o.d -o /tmp/renderingpipeline.E -E /scratch/games/flightgear/work/flightgear-2020.3.11/src/Viewer/renderingpipeline.cxx  
```  
  
  
(there's a second error in the same log which I haven't analyzed yet)  
```  
In file included from /usr/pkg/include/boost/lexical_cast/detail/converter_lexical.hpp:50,  
                 from /usr/pkg/include/boost/lexical_cast/try_lexical_convert.hpp:32,  
                 from /usr/pkg/include/boost/lexical_cast.hpp:33,  
                 from /pbulk/work/games/flightgear/work/flightgear-2020.3.11/src/Viewer/renderingpipeline.cxx:30:  
/usr/pkg/include/boost/container/container_fwd.hpp:75:26: error: 'intrusive' is not a namespace-name  
   75 |    namespace bi = boost::intrusive;  
      |                          ^~~~~~~~~  
/usr/pkg/include/boost/container/container_fwd.hpp:76:27: error: 'boost::intrusive' has not been declared  
   76 |    namespace bid = boost::intrusive::detail;  
      |                           ^~~~~~~~~  
/usr/pkg/include/boost/container/container_fwd.hpp:76:38: error: 'detail' is not a namespace-name  
   76 |    namespace bid = boost::intrusive::detail;  
      |                                      ^~~~~~  
/usr/pkg/include/boost/container/container_fwd.hpp:80:26: error: 'intrusive' is not a namespace-name  
   80 |    namespace bi = boost::intrusive;  
      |                          ^~~~~~~~~  
/usr/pkg/include/boost/container/container_fwd.hpp:81:27: error: 'boost::intrusive' has not been declared  
   81 |    namespace bid = boost::intrusive::detail;  
      |                           ^~~~~~~~~  
/usr/pkg/include/boost/container/container_fwd.hpp:81:38: error: 'detail' is not a namespace-name  
   81 |    namespace bid = boost::intrusive::detail;  
      |                                      ^~~~~~  
```

---

## Comment 1

> Username: amalon  
> Created at: 2025-02-24 17:01:44 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2679101286  

I'm also seeing this (again with simgear) after updating boost to 1.87 on archlinux. Reverting to 1.86 made it work again.

---

## Comment 2

> Username: Dwokfur  
> Created at: 2025-02-27 17:15:04 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2688604568  

https://bugs.gentoo.org/948204  
really annoying

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-02-28 16:23:18 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2691059345  

I don't have access to NetBSD, please let me know if the commit fixes the problem.

---

## Comment 4

> Username: 0-wiz-0  
> Created at: 2025-02-28 18:31:14 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2691300086  

Thank you for the patch - simgear builds again for me when I apply it to boost 1.87.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2025-02-28 18:34:03 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2691304764  

Sorry, seems like the old fix was not correct (BOOST_MOVE_STD_NS_END was missing the closing brace, fixed in the last commit). Maybe the problem was that boost/config.hpp was not being included in some circumstances.

---

## Comment 6

> Username: 0-wiz-0  
> Created at: 2025-02-28 18:45:14 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2691323233  

No, I had just misremembered and tested the wrong program - simgear instead of flightgear. Sorry for that.  
  
I didn't retest the first version of the patch, but the second version of the patch does fix the flightgear build for me.

---

## Comment 7

> Username: Dwokfur  
> Created at: 2025-02-28 19:39:54 UTC  
> Url: https://github.com/boostorg/move/issues/59#issuecomment-2691416040  

Updated fix works for me.
