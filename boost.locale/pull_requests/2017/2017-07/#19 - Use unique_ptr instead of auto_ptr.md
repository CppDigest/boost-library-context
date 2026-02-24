# #19 Use unique_ptr instead of auto_ptr [Closed]

> Username: 0-wiz-0  
> Created at: 2017-07-29 09:32:19 UTC  
> Updated at: 2017-08-07 13:44:13 UTC  
> Closed at: 2017-08-07 13:44:13 UTC  
> Url: https://github.com/boostorg/locale/pull/19  

I tried compiling gnucash-2.6.99 with gcc-5.4.0. This fails because the compiler complains about auto_ptr being deprecated. Replacing auto_ptr with unique_ptr makes the compilation of gnucash work.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-07-29 10:38:08 UTC  
> Url: https://github.com/boostorg/locale/pull/19#issuecomment-318819995  

This issue is important but simple replacement of `auto_ptr` by `unique_ptr` is wrong since it breaks C++2003 compatibility for all existing users.  
  
Correct fix would be providing dual interface of both auto and unique ptr so user can choose and replacement of the rest of the cases with scoped_ptr or something like that.  
  
Regarding gnucash - what exactly is the error you get?

---

## Comment 2

> Username: 0-wiz-0  
> Created_at: 2017-07-29 22:22:21 UTC  
> Url: https://github.com/boostorg/locale/pull/19#issuecomment-318863604  

Thanks for the feedback. Here's the output of a build of gnucash from git as of today:  
````  
In file included from .../include/boost/locale.hpp:17:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/format.hpp:124:22: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
                 std::auto_ptr<data> d;  
                      ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:19:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/generator.hpp:223:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<data> d;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
  
libtool: compile:  gcc -DHAVE_CONFIG_H -I. -I../../.. -I../../../src -I../../../src/core-utils -I../../../src/engine -I../../../src/gnc-module -I../../../src/app-utils -I../../../src/gnome -I../../../src/gnome-utils -I../../../src/import-export -I../../../src/libqof/qof -I../../../lib/libc -I../../../lib/goffice -I../../../lib -I.../include/guile/2.0 -I.../include -pthread -I.../include/glib/glib-2.0 -I.../lib/glib-2.0/include -pthread -I.../include/gtk-3.0 -I.../include/pango-1.0 -I.../include/cairo -I.../include/pixman-1 -I.../include/freetype2 -I.../include/libpng16 -I.../include/harfbuzz -I.../include/gdk-pixbuf-2.0 -D_REENTRANT -I.../include/glib/gio-unix-2.0/ -D_REENTRANT -I.../include/atk-1.0 -D_REENTRANT -pthread -I.../include/at-spi2-atk/2.0 -I.../include/at-spi-2.0 -I.../include/dbus-1.0 -I.../lib/dbus-1.0/include -pthread -DG_LOG_DOMAIN=\"gnc.import.csv\" -g -I.../include/libofx -I.../include/libdrm -Werror -Wdeclaration-after-statement -Wno-pointer-sign -D_FORTIFY_SOURCE=2 -Wall -Wunused -Wmissing-prototypes -Wmissing-declarations -Wno-unused -Wno-deprecated-declarations -O2 -g -fPIC -D_FORTIFY_SOURCE=2 -fstack-check -std=gnu11 -MT gnc-csv-gnumeric-popup.lo -MD -MP -MF .deps/gnc-csv-gnumeric-popup.Tpo -c gnc-csv-gnumeric-popup.c -o gnc-csv-gnumeric-popup.o >/dev/null 2>&1  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:107:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<localization_backend> get() const;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:114:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             void add_backend(std::string const &name,std::auto_ptr<localization_backend> backend);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:146:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<impl> pimpl_;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:179:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_utf8_converter();  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:187:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_simple_converter(std::string const &encoding);  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-csv-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:202:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     std::locale create_codecvt(std::locale const &in,std::auto_ptr<base_converter> cvt,character_facet_type type);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-csv-tokenizer.hpp:45,  
                 from gnc-csv-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
mv -f .deps/gnc-csv-gnumeric-popup.Tpo .deps/gnc-csv-gnumeric-popup.Plo  
/bin/sh ../../../libtool  --tag=CXX   --mode=compile c++ -DHAVE_CONFIG_H -I. -I../../..  -I../../../src -I../../../src/core-utils -I../../../src/engine -I../../../src/gnc-module -I../../../src/app-utils -I../../../src/gnome -I../../../src/gnome-utils -I../../../src/import-export -I../../../src/libqof/qof -I../../../lib/libc -I../../../lib/goffice -I../../../lib -I/usr/pkg/include/guile/2.0 -I/usr/pkg/include -pthread  -I/usr/pkg/include/glib/glib-2.0 -I/usr/pkg/lib/glib-2.0/include -I/usr/pkg/include -pthread  -I/usr/pkg/include/gtk-3.0 -I/usr/pkg/include/pango-1.0 -I/usr/pkg/include/glib/glib-2.0 -I/usr/pkg/lib/glib-2.0/include -I/usr/pkg/include -I/usr/pkg/include/cairo -I/usr/pkg/include/pixman-1 -I/usr/pkg/include/freetype2 -I/usr/pkg/include/libpng16 -I/usr/pkg/include/harfbuzz -I/usr/pkg/include/gdk-pixbuf-2.0 -D_REENTRANT -I/usr/pkg/include/glib/gio-unix-2.0/ -D_REENTRANT -I/usr/pkg/include/atk-1.0 -D_REENTRANT -pthread -I/usr/pkg/include/at-spi2-atk/2.0 -I/usr/pkg/include/at-spi-2.0 -I/usr/pkg/include/dbus-1.0 -I/usr/pkg/lib/dbus-1.0/include -pthread  -I/usr/pkg/include   -DG_LOG_DOMAIN=\"gnc.import.csv\" -g -I/usr/pkg/include/libofx -I/usr/pkg/include -I/usr/include -I/usr/pkg/include/freetype2 -I/usr/pkg/include/libdrm -Werror  -Wall -Wno-unused -Wno-deprecated-register -O2 -g -fPIC -D_FORTIFY_SOURCE=2 -fstack-check -I/usr/pkg/include/libofx -I/usr/pkg/include -I/usr/include -I/usr/pkg/include/freetype2 -I/usr/pkg/include/libdrm -std=gnu++11 -MT gnc-fw-tokenizer.lo -MD -MP -MF .deps/gnc-fw-tokenizer.Tpo -c -o gnc-fw-tokenizer.lo gnc-fw-tokenizer.cpp  
In file included from .../include/boost/locale.hpp:17:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/format.hpp:124:22: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
                 std::auto_ptr<data> d;  
                      ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:19:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/generator.hpp:223:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<data> d;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
libtool: compile:  c++ -DHAVE_CONFIG_H -I. -I../../.. -I../../../src -I../../../src/core-utils -I../../../src/engine -I../../../src/gnc-module -I../../../src/app-utils -I../../../src/gnome -I../../../src/gnome-utils -I../../../src/import-export -I../../../src/libqof/qof -I../../../lib/libc -I../../../lib/goffice -I../../../lib -I.../include/guile/2.0 -I.../include -pthread -I.../include/glib/glib-2.0 -I.../lib/glib-2.0/include -pthread -I.../include/gtk-3.0 -I.../include/pango-1.0 -I.../include/cairo -I.../include/pixman-1 -I.../include/freetype2 -I.../include/libpng16 -I.../include/harfbuzz -I.../include/gdk-pixbuf-2.0 -D_REENTRANT -I.../include/glib/gio-unix-2.0/ -D_REENTRANT -I.../include/atk-1.0 -D_REENTRANT -pthread -I.../include/at-spi2-atk/2.0 -I.../include/at-spi-2.0 -I.../include/dbus-1.0 -I.../lib/dbus-1.0/include -pthread -DG_LOG_DOMAIN=\"gnc.import.csv\" -g -I.../include/libofx -I.../include/libdrm -Werror -Wall -Wno-unused -Wno-deprecated-register -O2 -g -fPIC -D_FORTIFY_SOURCE=2 -fstack-check -std=gnu++11 -MT gnc-fw-tokenizer.lo -MD -MP -MF .deps/gnc-fw-tokenizer.Tpo -c gnc-fw-tokenizer.cpp  -fPIC -DPIC -o .libs/gnc-fw-tokenizer.o  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/localization_backend.hpp:107:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<localization_backend> get() const;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/localization_backend.hpp:114:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             void add_backend(std::string const &name,std::auto_ptr<localization_backend> backend);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/localization_backend.hpp:146:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<impl> pimpl_;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/util.hpp:179:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_utf8_converter();  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/util.hpp:187:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_simple_converter(std::string const &encoding);  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-dummy-tokenizer.cpp:10:  
.../include/boost/locale/util.hpp:202:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     std::locale create_codecvt(std::locale const &in,std::auto_ptr<base_converter> cvt,character_facet_type type);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-dummy-tokenizer.hpp:47,  
                 from gnc-dummy-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
cc1plus: error: unrecognized command line option '-Wno-deprecated-register' [-Werror]  
cc1plus: all warnings being treated as errors  
Makefile:778: recipe for target 'gnc-dummy-tokenizer.lo' failed  
gmake[5]: *** [gnc-dummy-tokenizer.lo] Error 1  
gmake[5]: *** Waiting for unfinished jobs....  
cc1plus: error: unrecognized command line option '-Wno-deprecated-register' [-Werror]  
cc1plus: all warnings being treated as errors  
Makefile:778: recipe for target 'gnc-csv-tokenizer.lo' failed  
gmake[5]: *** [gnc-csv-tokenizer.lo] Error 1  
In file included from .../include/boost/locale.hpp:17:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/format.hpp:124:22: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
                 std::auto_ptr<data> d;  
                      ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:19:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/generator.hpp:223:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<data> d;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:107:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<localization_backend> get() const;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:114:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             void add_backend(std::string const &name,std::auto_ptr<localization_backend> backend);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:22:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/localization_backend.hpp:146:18: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
             std::auto_ptr<impl> pimpl_;  
                  ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:179:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_utf8_converter();  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:187:28: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     BOOST_LOCALE_DECL std::auto_ptr<base_converter> create_simple_converter(std::string const &encoding);  
                            ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
In file included from .../include/boost/locale.hpp:24:0,  
                 from gnc-fw-tokenizer.cpp:11:  
.../include/boost/locale/util.hpp:202:59: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
     std::locale create_codecvt(std::locale const &in,std::auto_ptr<base_converter> cvt,character_facet_type type);  
                                                           ^  
In file included from /usr/include/g++/memory:81:0,  
                 from gnc-tokenizer.hpp:46,  
                 from gnc-fw-tokenizer.hpp:48,  
                 from gnc-fw-tokenizer.cpp:1:  
/usr/include/g++/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^  
mv -f .deps/assistant-csv-trans-import.Tpo .deps/assistant-csv-trans-import.Plo  
cc1plus: error: unrecognized command line option '-Wno-deprecated-register' [-Werror]  
cc1plus: all warnings being treated as errors  
````

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2017-07-30 04:09:01 UTC  
> Url: https://github.com/boostorg/locale/pull/19#issuecomment-318876833  

This isn't an error, it is warning.  
  
You can either add -Wno-depricated to suppress the warning or remove -Werror

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2017-08-07 10:39:50 UTC  
> Url: https://github.com/boostorg/locale/pull/19#issuecomment-320630554  

I updated the interface to support dual auto_ptr/unique_ptr interface.  
  
https://github.com/boostorg/locale/commit/322437a485c63d9fce4dc620597b6c75b6396daf  
  
You can try to build the project with `-DBOOST_LOCALE_HIDE_AUTO_PTR` to prevent warnings.  
It is in develop branch.  
  
Artyom

---
