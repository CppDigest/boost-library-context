# #664 - boost-1.61.0 failure to bootstrap with xcode-12 due to implicit function declarations [Closed]

> Username: rlei-weta  
> Created at: 2020-10-22 22:05:12 UTC  
> Updated at: 2024-10-31 13:35:51 UTC  
> Closed at: 2020-10-23 03:17:23 UTC  
> Url: https://github.com/boostorg/build/issues/664  

Environment:  
- macOs Catalina 10.15.7  
- Apple clang version 12.0.0  
  
Repro steps:  
1. Download and extract boost-1.61.0 (https://sourceforge.net/projects/boost/files/boost/1.61.0/boost_1_61_0.tar.bz2/download)  
2. Execute ./bootstrap.sh  
  
Error output:  
```  
Building Boost.Build engine with toolset darwin...   
Failed to build Boost.Build build engine  
Consult 'bootstrap.log' for more details  
```  
  
`bootstrap.log`:  
```  
###  
### Using 'darwin' toolset.  
###  
rm -rf bootstrap  
mkdir bootstrap  
cc -o bootstrap/jam0 command.c compile.c constants.c debug.c execcmd.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c class.c cwd.c native.c md5.c w32_getreg.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c execunix.c fileunix.c pathunix.c  
execcmd.c:120:5: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    out_printf( "...interrupted\n" );  
    ^  
1 error generated.  
make.c:132:13: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
            out_printf( "...found %d target%s...\n", counts->targets,  
            ^  
make.c:184:17: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
                out_printf( "fate change  %s from %s to %s (as dependant of %s)\n",  
                ^  
make.c:215:17: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
                out_printf( "fate change  %s from %s to %s (by rebuild)\n",  
                ^  
make.c:300:9: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
        ^  
make.c:300:41: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
                                        ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:300:41: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:307:41: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
                                        ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:307:41: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:380:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "bind\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:380:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:388:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "time\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:388:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:393:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "time\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:393:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:735:13: error: implicit declaration of function 'out_flush' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
            out_flush();  
            ^  
make.c:758:13: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            spaces( depth ), object_str( t->name ) );  
            ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:758:13: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
6 warnings and 5 errors generated.  
filesys.c:360:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( archive->members ) )  
         ^  
filesys.c:496:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) )  
         ^  
filesys.c:534:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) )  
         ^  
filesys.c:570:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) ) return list;  
         ^  
filesys.c:593:11: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( !filelist_empty( list ) ) result = list->size;  
          ^  
filesys.c:602:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) ) return;  
         ^  
6 errors generated.  
modules/path.c:16:12: error: implicit declaration of function 'file_query' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    return file_query( list_front( lol_get( frame->args, 0 ) ) ) ?  
           ^  
1 error generated.  
fileunix.c:233:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( archive->members ) )  
         ^  
fileunix.c:235:14: error: implicit declaration of function 'file_collect_archive_content_' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        if ( file_collect_archive_content_( archive ) < 0 )  
             ^  
fileunix.c:235:14: note: did you mean 'file_collect_dir_content_'?  
fileunix.c:110:5: note: 'file_collect_dir_content_' declared here  
int file_collect_dir_content_( file_info_t * const d )  
    ^  
fileunix.c:294:12: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( ! filelist_empty( archive->members ) ) filelist_free( archive->members );  
           ^  
3 errors generated.  
```  
  
Looks like it is caused by clang bundled with xcode-12 now reporting an error for implicit function declarations:  
https://developer.apple.com/documentation/xcode-release-notes/xcode-12-release-notes  
> Clang now reports an error when you use a function without an explicit declaration when building C or Objective-C code for macOS (-Werror=implicit-function-declaration flag is on). This additional error detection unifies Clang’s behavior for iOS/tvOS and macOS 64-bit targets for this diagnostic. (49917738)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-10-23 03:17:23 UTC  
> Url: https://github.com/boostorg/build/issues/664#issuecomment-714882384  

This was fixed 3 years ago.. https://github.com/boostorg/build/commit/48e9017139dd94446633480661e5447c7e0d8b1b

---

## Comment 2

> Username: sv6375261073  
> Created at: 2022-11-30 10:15:04 UTC  
> Updated at: 2022-11-30 12:23:27 UTC  
> Url: https://github.com/boostorg/build/issues/664#issuecomment-1331922738  

Hi everyone,  
  
I'm also facing the same above mentioned issue.  
In mac 10.15.7, Xcode: 12.4 & 12.3 with c and cpp 12 version.  
  
I am trying to update boost version to 1.70.0 but get below error.  
  
HUNTER CONFIGURATION:   
  
hunter_config(Boost VERSION 1.70.0)  
hunter_config(OpenSSL VERSION 1.0.2n)  
hunter_config(PocoCpp VERSION 1.9.0x CMAKE_ARGS ENABLE_NETSSL=ON ENABLE_CRYPTO=ON)  
~                                                                                      
  
[hunter ** FATAL ERROR **] Version not found: 1.70.0. Please check 'hunter_config' command.  
[hunter ** FATAL ERROR **]   
  
please suggest sometthing to resolve this issue.

---

## Comment 3

> Username: mloskot  
> Created at: 2022-11-30 11:58:00 UTC  
> Updated at: 2022-11-30 11:58:53 UTC  
> Url: https://github.com/boostorg/build/issues/664#issuecomment-1332038387  

> please suggest sometthing to resolve this issue.  
  
@sv6375261073 Get in touch with Hunter community  
  
p.s. Hijacking unrelated threads is a bad idea!

---

## Comment 4

> Username: t3sting3  
> Created at: 2024-10-29 20:28:57 UTC  
> Url: https://github.com/boostorg/build/issues/664#issuecomment-2445268173  

@rlei-weta  did you ever get this to work or did you have to use a more current version? even with the fix that @grafikrobot shared, the error   
  
```  
fileunix.c:235:14: error: implicit declaration of function 'file_collect_archive_content_' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        if ( file_collect_archive_content_( archive ) < 0 )  
```  
  
still persists.

---

## Comment 5

> Username: t3sting3  
> Created at: 2024-10-31 13:34:58 UTC  
> Updated at: 2024-10-31 13:35:51 UTC  
> Url: https://github.com/boostorg/build/issues/664#issuecomment-2449859153  

trying to fix an app for a client, but i absolutely have to get any boost 1.61 thru 1.65.1 compiled on clang 13 before i can move to a current version and having the same issue as you @rlei-weta and the commit that @grafikrobot linked is mostly unrelated to these issues.   
  
using boost 1.65.1 (after using the commit posted above) :   
  
```  
###  
### Using 'darwin' toolset.  
###  
rm -rf bootstrap  
mkdir bootstrap  
cc -o bootstrap/jam0 command.c compile.c constants.c debug.c execcmd.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c class.c cwd.c native.c md5.c w32_getreg.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c execunix.c fileunix.c pathunix.c  
execcmd.c:120:5: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    out_printf( "...interrupted\n" );  
    ^  
1 error generated.  
function.c:33:1: warning: '/*' within block comment [-Wcomment]  
/* */  
^  
1 warning generated.  
hash.c:29:1: warning: '/*' within block comment [-Wcomment]  
/* */  
^  
1 warning generated.  
jam.c:669:5: error: implicit declaration of function 'exec_done' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    exec_done();  
    ^  
jam.c:669:5: note: did you mean 'regex_done'?  
jam.c:219:6: note: 'regex_done' declared here  
void regex_done();  
     ^  
1 error generated.  
make.c:105:5: error: implicit declaration of function 'exec_init' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    exec_init();  
    ^  
make.c:136:13: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
            out_printf( "...found %d target%s...\n", counts->targets,  
            ^  
make.c:188:17: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
                out_printf( "fate change  %s from %s to %s (as dependant of %s)\n",  
                ^  
make.c:219:17: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
                out_printf( "fate change  %s from %s to %s (by rebuild)\n",  
                ^  
make.c:304:9: error: implicit declaration of function 'out_printf' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
        ^  
make.c:304:41: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
                                        ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:304:41: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:311:41: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
        out_printf( "make\t--\t%s%s\n", spaces( depth ), object_str( t->name ) );  
                                        ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:311:41: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:384:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "bind\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:384:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:392:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "time\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:392:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:397:49: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            out_printf( "time\t--\t%s%s: %s\n", spaces( depth ),  
                                                ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:397:49: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
make.c:739:13: error: implicit declaration of function 'out_flush' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
            out_flush();  
            ^  
make.c:762:13: warning: adding 'int' to a string does not append to the string [-Wstring-plus-int]  
            spaces( depth ), object_str( t->name ) );  
            ^~~~~~~~~~~~~~~  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                    ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~  
make.c:762:13: note: use array indexing to silence this warning  
make.c:85:44: note: expanded from macro 'spaces'  
#define spaces(x) ( "                    " + ( x > 20 ? 0 : 20-x ) )  
                                           ^  
6 warnings and 6 errors generated.  
strings.c:196:11: warning: expression result unused [-Wunused-value]  
    for ( p; p >= self->value && ( *p == '\0' || isspace( *p ) ); *p-- = 0 );  
          ^  
1 warning generated.  
filesys.c:360:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( archive->members ) )  
         ^  
filesys.c:496:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) )  
         ^  
filesys.c:534:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) )  
         ^  
filesys.c:570:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) ) return list;  
         ^  
filesys.c:593:11: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( !filelist_empty( list ) ) result = list->size;  
          ^  
filesys.c:602:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( list ) ) return;  
         ^  
6 errors generated.  
modules/path.c:16:12: error: implicit declaration of function 'file_query' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    return file_query( list_front( lol_get( frame->args, 0 ) ) ) ?  
           ^  
1 error generated.  
fileunix.c:236:10: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( filelist_empty( archive->members ) )  
         ^  
fileunix.c:238:14: error: implicit declaration of function 'file_collect_archive_content_' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        if ( file_collect_archive_content_( archive ) < 0 )  
             ^  
fileunix.c:238:14: note: did you mean 'file_collect_dir_content_'?  
fileunix.c:110:5: note: 'file_collect_dir_content_' declared here  
int file_collect_dir_content_( file_info_t * const d )  
    ^  
fileunix.c:297:12: error: implicit declaration of function 'filelist_empty' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
    if ( ! filelist_empty( archive->members ) ) filelist_free( archive->members );  
           ^  
3 errors generated.  
```
