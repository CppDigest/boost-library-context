# #384 - Crash in try_join_for on mac [Open]

> Username: moneromooo-monero  
> Created at: 2023-04-06 15:38:23 UTC  
> Updated at: 2023-04-06 15:38:23 UTC  
> Url: https://github.com/boostorg/thread/issues/384  

Works fine on Linux and Windows. I'm on Linux, where it's valgrind and ASAN clean. Both boost 1.81 and master.  
This reproducer  
```  
#include "boost/thread.hpp"  
  
int main()  
{  
  boost::chrono::milliseconds ms(2000);  
  auto b = boost::thread([](){ sleep(2); });  
  b.try_join_for(ms);  
  return 0;  
}  
```  
crashes on mac for someone else.  
After various blind attempts at debugging, the crashes goes if adding "cxxflags=-std=c++14" to the ./b2 command line.  
  
The command line causing a crash in the sample above is:  
  
```  
./bootstrap.sh clang  
./b2 install --prefix=/Users/X/dev/prefix/ --build-dir=/Users/X/dev/boost/somewhere/ --without-mpi --without-python --without-iostreams --without-graph --without-graph_parallel --without-coroutine --without-fiber --without-log --without-math --without-random --without-stacktrace --without-test --without-type_erasure --without-wave --without-context --without-contract --no-cmake-config --layout=system address-model=64 variant=release link=static  
```  
This b2 command line instead fixes the crash:  
```  
./b2 install --prefix=/Users/X/dev/prefix/ --build-dir=/Users/X/dev/boost/somewhere/ --without-mpi --without-python --without-iostreams --without-graph --without-graph_parallel --without-coroutine --without-fiber --without-log --without-math --without-random --without-stacktrace --without-test --without-type_erasure --without-wave --without-context --without-contract --no-cmake-config --layout=system address-model=64 variant=release link=static cxxflags=-std=c++14  
```  
The stack trace for the crash:  
```  
Process:               a.out [X]  
Path:                  /Users/USER/*/a.out  
Identifier:            a.out  
Version:               0  
Code Type:             X86-64 (Native)  
Parent Process:        X  
Responsible:           X  
User ID:               X  
  
Date/Time:             X  
OS Version:            macOS 11.6.8 (20G730)  
Report Version:        12  
Anonymous UUID:        X  
  
Sleep/Wake UUID:       X  
  
Time Awake Since Boot: X  
Time Since Wake:       X  
  
System Integrity Protection: enabled  
  
Crashed Thread:        0  Dispatch queue: com.apple.main-thread  
  
Exception Type:        EXC_BAD_ACCESS (SIGSEGV)  
Exception Codes:       KERN_INVALID_ADDRESS at 0x00002af7713088dc  
Exception Note:        EXC_CORPSE_NOTIFY  
  
Termination Signal:    Segmentation fault: 11  
Termination Reason:    Namespace SIGNAL, Code 0xb  
Terminating Process:   exc handler [46102]  
  
VM Regions Near 0x2af7713088dc:  
    __LINKEDIT                  10f174000-10f178000    [   16K] r--/r-- SM=NUL  /usr/lib/dyld  
-->   
    STACK GUARD              7000061f1000-7000061f2000 [    4K] ---/rwx SM=NUL  stack guard for thread 1  
  
Application Specific Information:  
dyld2 mode  
  
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread  
0   libsystem_pthread.dylib       	0x00007fff2080bd86 pthread_mutex_lock + 4  
1   a.out                         	0x0000000109f0ac08 boost::mutex::lock() + 24  
2   a.out                         	0x0000000109f0865d boost::thread::native_handle() + 45  
3   a.out                         	0x0000000109f06615 boost::thread::get_id() const + 21  
4   a.out                         	0x0000000109f064aa boost::thread::do_try_join_until(boost::detail::real_platform_timepoint const&) + 42  
5   a.out                         	0x0000000109f060c2 bool boost::thread::try_join_until<boost::chrono::duration<long long, boost::ratio<1l, 1000000000l> > >(boost::chrono::time_point<boost::chrono::system_clock, boost::chrono::duration<long long, boost::ratio<1l, 1000000000l> > > const&) + 50  
6   a.out                         	0x0000000109f05daf bool boost::thread::try_join_until<boost::chrono::steady_clock, boost::chrono::duration<long long, boost::ratio<1l, 1000000000l> > >(boost::chrono::time_point<boost::chrono::steady_clock, boost::chrono::duration<long long, boost::ratio<1l, 1000000000l> > > const&) + 415  
7   a.out                         	0x0000000109eff8bf bool boost::thread::try_join_for<long long, boost::ratio<1l, 1000l> >(boost::chrono::duration<long long, boost::ratio<1l, 1000l> > const&) + 63  
8   a.out                         	0x0000000109eff808 main + 56  
9   libdyld.dylib                 	0x00007fff2082bf3d start + 1  
  
Thread 1:  
0   dyld                          	0x000000010f07ced4 ImageLoaderMachO::libPath(unsigned int) const + 34  
1   dyld                          	0x000000010f07ca6d ImageLoaderMachO::findExportedSymbol(char const*, bool, char const*, ImageLoader const**) const + 147  
2   dyld                          	0x000000010f0750bb ImageLoader::findExportedSymbolAddress(ImageLoader::LinkContext const&, char const*, ImageLoader const*, int, bool, ImageLoader const**, unsigned long*) const + 47  
3   dyld                          	0x000000010f083ad9 ImageLoaderMachOCompressed::resolveTwolevel(ImageLoader::LinkContext const&, char const*, ImageLoader const*, ImageLoader const*, unsigned int, bool, bool, ImageLoader const**) + 89  
4   dyld                          	0x000000010f083d6b ImageLoaderMachOCompressed::resolve(ImageLoader::LinkContext const&, char const*, unsigned char, long, ImageLoader const**, DyldSharedCache::DataConstLazyScopedWriter&, ImageLoaderMachOCompressed::LastLookup*, bool) + 299  
5   dyld                          	0x000000010f0873c1 ImageLoaderMachOCompressed::doBindFastLazySymbol(unsigned int, ImageLoader::LinkContext const&, void (*)(), void (*)()) + 321  
6   dyld                          	0x000000010f06554b dyld::fastBindLazySymbol(ImageLoader**, unsigned long) + 86  
7   libdyld.dylib                 	0x00007fff2082ace8 _dyld_fast_stub_entry(void*, long) + 65  
8   libdyld.dylib                 	0x00007fff2082ac26 dyld_stub_binder + 282  
9   ???                           	0x0000000109f15550 0 + 4461778256  
10  a.out                         	0x0000000109f073d9 boost::(anonymous namespace)::thread_proxy(void*) + 121  
11  libsystem_pthread.dylib       	0x00007fff208108fc _pthread_start + 224  
12  libsystem_pthread.dylib       	0x00007fff2080c443 thread_start + 15  
  
Thread 0 crashed with X86 Thread State (64-bit):  
  rax: 0x000000010f137e00  rbx: 0x00002af7713088dc  rcx: 0x000000010f137e00  rdx: 0x00000000222a9598  
  rdi: 0x00002af7713088dc  rsi: 0x000000010f137e00  rbp: 0x00007ffee5d026b0  rsp: 0x00007ffee5d026b0  
   r8: 0x00002af771309dd9   r9: 0x000000000d413abc  r10: 0x0000000000000020  r11: 0x00007fff2080bd82  
  r12: 0x0000000000000000  r13: 0x0000000000000000  r14: 0x00002af7713088dc  r15: 0x00007faa29c05b30  
  rip: 0x00007fff2080bd86  rfl: 0x0000000000010206  cr2: 0x00002af7713088dc  
    
Logical CPU:     6  
Error Code:      0x00000004 (no mapping for user data read)  
Trap Number:     14  
  
Thread 0 instruction stream:  
  48 89 f3 49 89 fe 48 8d-3d d1 ed 2f 60 be 00 00  H..I..H.=../`...  
  05 00 e8 3d 7e 00 00 b8-00 01 00 00 48 8d 0d c7  ...=~.......H...  
  ed 2f 60 48 83 3c c1 00-74 12 48 ff c0 48 3d 00  ./`H.<..t.H..H=.  
  03 00 00 75 ee bb 23 00-00 00 eb 0c 48 f7 d3 48  ...u..#.....H..H  
  89 1c c1 49 89 06 31 db-48 8d 3d 8f ed 2f 60 e8  ...I..1.H.=../`.  
  06 7e 00 00 89 d8 5b 41-5e 5d c3 90 55 48 89 e5  .~....[A^]..UH..  
 [48]81 3f 5a 54 55 4d 75-73 8b 47 0c 89 c1 81 e1  H.?ZTUMus.G.....	<==  
  c0 01 00 00 83 f9 40 74-6b 0f ba e0 0e 72 6d 48  ......@tk....rmH  
  8d 57 27 48 83 e2 f8 48-8b 0a f6 c1 02 75 45 4c  .W'H...H.....uEL  
  8d 57 1f 49 83 e2 f8 65-4c 8b 04 25 f8 ff ff ff  .W.I...eL..%....  
  49 b9 00 00 00 00 ff ff-ff ff 48 89 ce f6 c1 02  I.........H.....  
  75 22 4c 21 ce 89 c8 48-09 f0 83 c9 02 48 09 f1  u"L!...H.....H..  
    
Thread 0 last branch register state not available.  
  
  
Binary Images:  
       0x109efd000 -        0x109f10fff +a.out (0) <96252B97-E2CF-3A1A-AD77-5B71070B45B0> /Users/USER/*/a.out  
       0x10f060000 -        0x10f0fbfff  dyld (852.2) <E20C43E3-CEB9-397A-9056-11A6A8D3F86E> /usr/lib/dyld  
    0x7fff20548000 -     0x7fff20549fff  libsystem_blocks.dylib (79) <4DF6D8F5-D9C2-3A96-8DE4-5E99D6B73DC8> /usr/lib/system/libsystem_blocks.dylib  
    0x7fff2054a000 -     0x7fff2057ffff  libxpc.dylib (2038.120.1) <0397FC9F-BD11-31FC-882E-9EDAA1E5CA65> /usr/lib/system/libxpc.dylib  
    0x7fff20580000 -     0x7fff20597fff  libsystem_trace.dylib (1277.120.1) <0A24EB90-5396-36B0-A7E6-E9288702856D> /usr/lib/system/libsystem_trace.dylib  
    0x7fff20598000 -     0x7fff20635fff  libcorecrypto.dylib (1000.140.4) <57E7471E-3960-3398-8918-20DF446EA99B> /usr/lib/system/libcorecrypto.dylib  
    0x7fff20636000 -     0x7fff20662fff  libsystem_malloc.dylib (317.140.5) <10C22FD0-FC7B-3325-852E-FEC4E88D2BC5> /usr/lib/system/libsystem_malloc.dylib  
    0x7fff20663000 -     0x7fff206a7fff  libdispatch.dylib (1271.120.2) <BA7AD614-F2C2-3E89-9043-43DD548AE5B1> /usr/lib/system/libdispatch.dylib  
    0x7fff206a8000 -     0x7fff206e1fff  libobjc.A.dylib (824.1) <A598DA89-FF71-37BF-B954-26277541D859> /usr/lib/libobjc.A.dylib  
    0x7fff206e2000 -     0x7fff206e4fff  libsystem_featureflags.dylib (28.60.1) <6EB33926-8E22-3000-B2F1-C6182B8CBD8F> /usr/lib/system/libsystem_featureflags.dylib  
    0x7fff206e5000 -     0x7fff2076dfff  libsystem_c.dylib (1439.141.1) <8447A4B8-0751-3EF1-AA9B-042E40EFA07D> /usr/lib/system/libsystem_c.dylib  
    0x7fff2076e000 -     0x7fff207c3fff  libc++.1.dylib (905.6) <81674ADB-670F-3B19-AF5D-F3F66CB93D44> /usr/lib/libc++.1.dylib  
    0x7fff207c4000 -     0x7fff207d9fff  libc++abi.dylib (905.6) <78CE7863-E224-3D0B-98F1-E5E3E382322D> /usr/lib/libc++abi.dylib  
    0x7fff207da000 -     0x7fff20809fff  libsystem_kernel.dylib (7195.141.32) <70F8035C-BC80-38BA-82CC-A8856CEA0133> /usr/lib/system/libsystem_kernel.dylib  
    0x7fff2080a000 -     0x7fff20815fff  libsystem_pthread.dylib (454.120.2) <49670AEC-4D5D-3383-906C-23F568351FCB> /usr/lib/system/libsystem_pthread.dylib  
    0x7fff20816000 -     0x7fff20851fff  libdyld.dylib (852.2) <5FBD0E1A-ACCE-36DB-B11C-622F26C85132> /usr/lib/system/libdyld.dylib  
    0x7fff20852000 -     0x7fff2085bfff  libsystem_platform.dylib (254.80.2) <A85D12B6-6213-34EE-84D8-8E993C19E330> /usr/lib/system/libsystem_platform.dylib  
    0x7fff2085c000 -     0x7fff20887fff  libsystem_info.dylib (542.40.4) <E757523E-5882-3CED-A3EF-E5A574FBD1C2> /usr/lib/system/libsystem_info.dylib  
    0x7fff22c58000 -     0x7fff22c61fff  libsystem_darwin.dylib (1439.141.1) <29F82ABE-E1A0-3BC2-B91E-ADC010CF23FA> /usr/lib/system/libsystem_darwin.dylib  
    0x7fff23077000 -     0x7fff23082fff  libsystem_notify.dylib (279.40.4) <608B5A07-D31A-3BEC-86BF-C2E498C085AF> /usr/lib/system/libsystem_notify.dylib  
    0x7fff2500f000 -     0x7fff2501dfff  libsystem_networkextension.dylib (1295.140.3) <022BE26C-C058-3CC7-8E0B-348B3D3B639C> /usr/lib/system/libsystem_networkextension.dylib  
    0x7fff2507c000 -     0x7fff25092fff  libsystem_asl.dylib (385.0.2) <A5147017-3488-3B18-A11B-C19697C110FC> /usr/lib/system/libsystem_asl.dylib  
    0x7fff2675a000 -     0x7fff26761fff  libsystem_symptoms.dylib (1431.140.1) <EA6435E7-8F85-315B-8AED-C20A07DE7F96> /usr/lib/system/libsystem_symptoms.dylib  
    0x7fff28769000 -     0x7fff28779fff  libsystem_containermanager.dylib (318.100.4) <E0A89571-6D3E-3184-9F39-C6094C87B92B> /usr/lib/system/libsystem_containermanager.dylib  
    0x7fff2947d000 -     0x7fff29480fff  libsystem_configuration.dylib (1109.140.1) <89E70992-616F-3DD3-9430-67025F759A1B> /usr/lib/system/libsystem_configuration.dylib  
    0x7fff29481000 -     0x7fff29485fff  libsystem_sandbox.dylib (1441.141.7) <6C99B070-E74F-3065-8DA0-D7214DA85E23> /usr/lib/system/libsystem_sandbox.dylib  
    0x7fff2a177000 -     0x7fff2a179fff  libquarantine.dylib (119.40.4) <1263FFEA-9722-39FA-A097-D28B4F7FDE04> /usr/lib/system/libquarantine.dylib  
    0x7fff2a722000 -     0x7fff2a726fff  libsystem_coreservices.dylib (127.1) <7AE405FC-6A44-34A0-86AD-6BD80B0050BB> /usr/lib/system/libsystem_coreservices.dylib  
    0x7fff2a936000 -     0x7fff2a97dfff  libsystem_m.dylib (3186.100.3) <EA0354A3-8618-3D76-A760-E550AC60CE95> /usr/lib/system/libsystem_m.dylib  
    0x7fff2a97f000 -     0x7fff2a984fff  libmacho.dylib (980) <1FCE2BE3-4F6F-3EAA-9BC5-A9892A45CF0D> /usr/lib/system/libmacho.dylib  
    0x7fff2a9a1000 -     0x7fff2a9acfff  libcommonCrypto.dylib (60178.120.3) <7E242F29-1CB6-30EF-8C9A-C768A90FDBA0> /usr/lib/system/libcommonCrypto.dylib  
    0x7fff2a9ad000 -     0x7fff2a9b7fff  libunwind.dylib (201) <A5B040A8-847F-36EE-B13D-5DD1F5CD5BED> /usr/lib/system/libunwind.dylib  
    0x7fff2a9b8000 -     0x7fff2a9bffff  liboah.dylib (203.58) <26D08622-69F5-32DB-80D2-9B4651A9F0CC> /usr/lib/liboah.dylib  
    0x7fff2a9c0000 -     0x7fff2a9cafff  libcopyfile.dylib (173.40.2) <8C783785-0F5F-3DC5-B815-B29CEBA75737> /usr/lib/system/libcopyfile.dylib  
    0x7fff2a9cb000 -     0x7fff2a9d2fff  libcompiler_rt.dylib (102.2) <1FDC92D1-8A17-30AF-8E72-4F0517AEA157> /usr/lib/system/libcompiler_rt.dylib  
    0x7fff2a9d3000 -     0x7fff2a9d5fff  libsystem_collections.dylib (1439.141.1) <11D5775A-AD4C-35ED-BC05-616AB67ACBBE> /usr/lib/system/libsystem_collections.dylib  
    0x7fff2a9d6000 -     0x7fff2a9d8fff  libsystem_secinit.dylib (87.60.1) <27982311-637E-3308-9F55-4871762736F4> /usr/lib/system/libsystem_secinit.dylib  
    0x7fff2a9d9000 -     0x7fff2a9dbfff  libremovefile.dylib (49.120.1) <5973CED7-797B-3288-9589-C1856752F91A> /usr/lib/system/libremovefile.dylib  
    0x7fff2a9dc000 -     0x7fff2a9dcfff  libkeymgr.dylib (31) <AAA929A0-45E5-3186-8ABD-37EB25B2C939> /usr/lib/system/libkeymgr.dylib  
    0x7fff2a9dd000 -     0x7fff2a9e4fff  libsystem_dnssd.dylib (1310.140.1) <4A85F13B-E3AD-3C44-B327-091F56D35CC1> /usr/lib/system/libsystem_dnssd.dylib  
    0x7fff2a9e5000 -     0x7fff2a9eafff  libcache.dylib (83) <435668CB-666B-3379-AD65-F604A72099F4> /usr/lib/system/libcache.dylib  
    0x7fff2a9eb000 -     0x7fff2a9ecfff  libSystem.B.dylib (1292.120.1) <BF2EB42A-8928-3250-B344-00481468F0DB> /usr/lib/libSystem.B.dylib  
    0x7fff2ddf0000 -     0x7fff2ddf0fff  liblaunch.dylib (2038.120.1) <A3F46CC6-BD89-39DB-8732-C885B881A635> /usr/lib/system/liblaunch.dylib  
    0x7fff30287000 -     0x7fff30287fff  libsystem_product_info_filter.dylib (8.40.1) <6CA8DEA4-5BD4-375F-9AA7-3338135306C5> /usr/lib/system/libsystem_product_info_filter.dylib  
  
External Modification Summary:  
  Calls made by other processes targeting this process:  
    task_for_pid: 0  
    thread_create: 0  
    thread_set_state: 0  
  Calls made by this process:  
    task_for_pid: 0  
    thread_create: 0  
    thread_set_state: 0  
  Calls made by all processes on this machine:  
    task_for_pid: 0  
    thread_create: 0  
    thread_set_state: 0  
  
VM Region Summary:  
ReadOnly portion of Libraries: Total=505.2M resident=0K(0%) swapped_out_or_unallocated=505.2M(100%)  
Writable regions: Total=17.8M written=0K(0%) resident=0K(0%) swapped_out=0K(0%) unallocated=17.8M(100%)  
   
                                VIRTUAL   REGION   
REGION TYPE                        SIZE    COUNT (non-coalesced)   
===========                     =======  =======   
Kernel Alloc Once                    8K        1   
MALLOC                            9288K        7   
MALLOC guard page                   16K        4   
STACK GUARD                       56.0M        2   
Stack                             8712K        2   
VM_ALLOCATE                          8K        2   
__DATA                             404K       43   
__DATA_CONST                       258K       34   
__DATA_DIRTY                        58K       22   
__LINKEDIT                       500.3M        5   
__OBJC_RO                         70.3M        1   
__OBJC_RW                         2496K        2   
__TEXT                            5064K       44   
shared memory                        4K        1   
===========                     =======  =======   
TOTAL                            652.3M      170   
```
