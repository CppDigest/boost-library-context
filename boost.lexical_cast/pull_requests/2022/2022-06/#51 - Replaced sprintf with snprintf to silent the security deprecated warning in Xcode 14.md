# #51 Replaced sprintf with snprintf to silent the security deprecated warning in Xcode 14 [Merged]

> Username: biodranik  
> Created at: 2022-06-10 22:00:25 UTC  
> Updated at: 2022-09-13 15:50:00 UTC  
> Merged at: 2022-08-31 18:27:53 UTC  
> Closed at: 2022-08-31 18:27:53 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51  

<img width="959" alt="image" src="https://user-images.githubusercontent.com/170263/173156445-e104d051-9e41-408a-9801-1f9726e9877f.png">

---

## Comment 1

> Username: apolukhin  
> Created_at: 2022-08-31 18:28:03 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1233275018  

Many thanks for the PR!

---

## Comment 2

> Username: apolukhin  
> Created_at: 2022-09-03 05:00:06 UTC  
> Updated_at: 2022-09-03 05:00:56 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1236049443  

Reverted the commit as it broke compilation on some platforms (at least MinGW)/  
  
Just suppress warning of your IDE as it is recommended here: https://stackoverflow.com/questions/6921884/in-xcode-how-to-suppress-all-warnings-in-specific-source-files

---

## Comment 3

> Username: biodranik  
> Created_at: 2022-09-03 05:20:34 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1236051637  

Wow, what was the compilation error? Mingw sources definitely have snprintf. Could it be some other issue? Also, it could be `#ifdef'ed`:  
  
```  
➜  mingw-w64-v10.0.0 rg -w snprintf  
mingw-w64-crt/Makefile.in  
873:	stdio/mingw_wvfscanf.c stdio/snprintf.c stdio/snwprintf.c \  
1210:	stdio/lib32_libmingwex_a-snprintf.$(OBJEXT) \  
2260:	stdio/mingw_wvfscanf.c stdio/snprintf.c stdio/snwprintf.c \  
2597:	stdio/lib64_libmingwex_a-snprintf.$(OBJEXT) \  
3514:	stdio/mingw_wvfscanf.c stdio/snprintf.c stdio/snwprintf.c \  
3857:	stdio/libarm32_libmingwex_a-snprintf.$(OBJEXT) \  
4816:	stdio/mingw_wvfscanf.c stdio/snprintf.c stdio/snwprintf.c \  
5124:	stdio/libarm64_libmingwex_a-snprintf.$(OBJEXT) \  
8902:	stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po \  
9025:	stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po \  
9132:	stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po \  
9241:	stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po \  
10826:  stdio/mingw_wvfscanf.c   stdio/snprintf.c          stdio/snwprintf.c        stdio/strtof.c            stdio/truncate.c        \  
14434:stdio/lib32_libmingwex_a-snprintf.$(OBJEXT): stdio/$(am__dirstamp) \  
16360:stdio/lib64_libmingwex_a-snprintf.$(OBJEXT): stdio/$(am__dirstamp) \  
18084:stdio/libarm32_libmingwex_a-snprintf.$(OBJEXT): stdio/$(am__dirstamp) \  
19976:stdio/libarm64_libmingwex_a-snprintf.$(OBJEXT): stdio/$(am__dirstamp) \  
24254:@AMDEP_TRUE@@am__include@ @am__quote@stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po@am__quote@ # am--include-marker  
24377:@AMDEP_TRUE@@am__include@ @am__quote@stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po@am__quote@ # am--include-marker  
24484:@AMDEP_TRUE@@am__include@ @am__quote@stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po@am__quote@ # am--include-marker  
24593:@AMDEP_TRUE@@am__include@ @am__quote@stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po@am__quote@ # am--include-marker  
32970:stdio/lib32_libmingwex_a-snprintf.o: stdio/snprintf.c  
32971:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/lib32_libmingwex_a-snprintf.o -MD -MP -MF stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Tpo -c -o stdio/lib32_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
32972:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po  
32973:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/lib32_libmingwex_a-snprintf.o' libtool=no @AMDEPBACKSLASH@  
32975:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/lib32_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
32977:stdio/lib32_libmingwex_a-snprintf.obj: stdio/snprintf.c  
32978:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/lib32_libmingwex_a-snprintf.obj -MD -MP -MF stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Tpo -c -o stdio/lib32_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
32979:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po  
32980:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/lib32_libmingwex_a-snprintf.obj' libtool=no @AMDEPBACKSLASH@  
32982:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/lib32_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
43610:stdio/lib64_libmingwex_a-snprintf.o: stdio/snprintf.c  
43611:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/lib64_libmingwex_a-snprintf.o -MD -MP -MF stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Tpo -c -o stdio/lib64_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
43612:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po  
43613:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/lib64_libmingwex_a-snprintf.o' libtool=no @AMDEPBACKSLASH@  
43615:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/lib64_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
43617:stdio/lib64_libmingwex_a-snprintf.obj: stdio/snprintf.c  
43618:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/lib64_libmingwex_a-snprintf.obj -MD -MP -MF stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Tpo -c -o stdio/lib64_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
43619:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po  
43620:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/lib64_libmingwex_a-snprintf.obj' libtool=no @AMDEPBACKSLASH@  
43622:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(lib64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/lib64_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
53312:stdio/libarm32_libmingwex_a-snprintf.o: stdio/snprintf.c  
53313:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/libarm32_libmingwex_a-snprintf.o -MD -MP -MF stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Tpo -c -o stdio/libarm32_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
53314:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po  
53315:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/libarm32_libmingwex_a-snprintf.o' libtool=no @AMDEPBACKSLASH@  
53317:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/libarm32_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
53319:stdio/libarm32_libmingwex_a-snprintf.obj: stdio/snprintf.c  
53320:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/libarm32_libmingwex_a-snprintf.obj -MD -MP -MF stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Tpo -c -o stdio/libarm32_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
53321:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po  
53322:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/libarm32_libmingwex_a-snprintf.obj' libtool=no @AMDEPBACKSLASH@  
53324:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm32_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/libarm32_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
63826:stdio/libarm64_libmingwex_a-snprintf.o: stdio/snprintf.c  
63827:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/libarm64_libmingwex_a-snprintf.o -MD -MP -MF stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Tpo -c -o stdio/libarm64_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
63828:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po  
63829:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/libarm64_libmingwex_a-snprintf.o' libtool=no @AMDEPBACKSLASH@  
63831:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/libarm64_libmingwex_a-snprintf.o `test -f 'stdio/snprintf.c' || echo '$(srcdir)/'`stdio/snprintf.c  
63833:stdio/libarm64_libmingwex_a-snprintf.obj: stdio/snprintf.c  
63834:@am__fastdepCC_TRUE@	$(AM_V_CC)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -MT stdio/libarm64_libmingwex_a-snprintf.obj -MD -MP -MF stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Tpo -c -o stdio/libarm64_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
63835:@am__fastdepCC_TRUE@	$(AM_V_at)$(am__mv) stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Tpo stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po  
63836:@AMDEP_TRUE@@am__fastdepCC_FALSE@	$(AM_V_CC)source='stdio/snprintf.c' object='stdio/libarm64_libmingwex_a-snprintf.obj' libtool=no @AMDEPBACKSLASH@  
63838:@am__fastdepCC_FALSE@	$(AM_V_CC@am__nodep@)$(CC) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(libarm64_libmingwex_a_CPPFLAGS) $(CPPFLAGS) $(AM_CFLAGS) $(CFLAGS) -c -o stdio/libarm64_libmingwex_a-snprintf.obj `if test -f 'stdio/snprintf.c'; then $(CYGPATH_W) 'stdio/snprintf.c'; else $(CYGPATH_W) '$(srcdir)/stdio/snprintf.c'; fi`  
73887:	-rm -f stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po  
74010:	-rm -f stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po  
74117:	-rm -f stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po  
74226:	-rm -f stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po  
77187:	-rm -f stdio/$(DEPDIR)/lib32_libmingwex_a-snprintf.Po  
77310:	-rm -f stdio/$(DEPDIR)/lib64_libmingwex_a-snprintf.Po  
77417:	-rm -f stdio/$(DEPDIR)/libarm32_libmingwex_a-snprintf.Po  
77526:	-rm -f stdio/$(DEPDIR)/libarm64_libmingwex_a-snprintf.Po  
  
mingw-w64-crt/stdio/snprintf_alias.c  
11: * snprintf inline, and it can't be renamed with "#define snprintf othername"  
12: * either, as stdio.h contains "#undef snprintf". */  
16:int __cdecl snprintf(char *buffer, size_t n, const char *format, ...);  
17:int __cdecl snprintf(char *buffer, size_t n, const char *format, ...)  
  
mingw-w64-crt/stdio/ucrt_snprintf.c  
11:int __cdecl snprintf (char * __restrict__ __stream, size_t __n, const char * __restrict__ __format, ...)  
20:int __cdecl (*__MINGW_IMP_SYMBOL(snprintf))(char *__restrict__, size_t, const char *__restrict__, ...) = snprintf;  
  
mingw-w64-crt/stdio/mingw_snprintf.c  
1:/* snprintf.c  
3: * $Id: snprintf.c,v 1.3 2008/07/28 23:24:20 keithmarshall Exp $  
5: * Provides an implementation of the "snprintf" function, conforming  
  
mingw-w64-crt/Makefile.am  
577:  stdio/mingw_wvfscanf.c   stdio/snprintf.c          stdio/snwprintf.c        stdio/strtof.c            stdio/truncate.c        \  
  
mingw-w64-crt/ChangeLog.2012  
247:	* lib64/msvcrt.def: Add forwarders to wide-(v)snprintf  
248:	and provide snprintf.  
  
mingw-w64-headers/crt/ChangeLog.2011  
73:	* stdio.h: Fix broken handling for snprintf/vsnprintf  
  
mingw-w64-headers/crt/ChangeLog.2010  
308:	* stdio.h: push/pop possible [v]snprintf and [v]snwprintf macros.  
  
mingw-w64-headers/crt/stdio.h  
13:#pragma push_macro("snprintf")  
14:#undef snprintf  
441:int snprintf (char *__stream, size_t __n, const char *__format, ...)  
451:int snprintf (char *__stream, size_t __n, const char *__format, ...)  
831:  int snprintf (char * __restrict__ __stream, size_t __n, const char * __restrict__ __format, ...);  
869:int snprintf (char * __restrict__ __stream, size_t __n, const char * __restrict__ __format, ...)  
881:int snprintf (char * __restrict__ __stream, size_t __n, const char * __restrict__ __format, ...)  
1565:#pragma pop_macro("snprintf")  
➜  mingw-w64-v10.0.0  
```

---

## Comment 4

> Username: biodranik  
> Created_at: 2022-09-13 15:48:31 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1245604824  

@apolukhin Here is the proper fix how is it done in other boost libraries:  
https://github.com/boostorg/assert/commit/601ee4ba7ae9d9b532dab5f7e757f4187dce6300

---

## Comment 5

> Username: biodranik  
> Created_at: 2022-09-13 15:50:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/51#issuecomment-1245606751  

> Just suppress warning of your IDE as it is recommended here: https://stackoverflow.com/questions/6921884/in-xcode-how-to-suppress-all-warnings-in-specific-source-files  
  
Disabling ALL warnings is definitely not an option. It would be great to properly fix it while still working on MinGW. Where are failed build logs?

---
