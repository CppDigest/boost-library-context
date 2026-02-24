# #547 - Boost.Process V2 (1.87) broken on MacOS 26 [Closed]

> Username: o01eg  
> Created at: 2026-02-20 09:34:23 UTC  
> Updated at: 2026-02-20 13:32:57 UTC  
> Closed at: 2026-02-20 13:32:21 UTC  
> Url: https://github.com/boostorg/process/issues/547  

When trying to launch program built with Boost.Process V2 https://github.com/freeorion/freeorion/pull/5156 it fails with segfault  
  
https://github.com/freeorion/freeorion/actions/runs/22211965932/job/64247992167?pr=5156  
  
LLDB shows it as:  
  
```  
(lldb) bt  
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x0)  
  * frame #0: 0x0000000000000000  
    frame #1: 0x0000000101b26108 libfreeorioncommon.dylib`__cxx_global_var_init.4 at error.hpp:23:46 [opt] [inlined]  
    frame #2: 0x0000000101b26104 libfreeorioncommon.dylib`_GLOBAL__sub_I_Process.cpp at Process.cpp:0 [opt]  
    frame #3: 0x0000000197c3ec24 dyld`invocation function for block in dyld4::Loader::findAndRunAllInitializers(dyld4::RuntimeState&) const + 444  
    frame #4: 0x0000000197c7cc24 dyld`invocation function for block in dyld3::MachOAnalyzer::forEachInitializer(Diagnostics&, dyld3::MachOAnalyzer::VMAddrConverter const&, void (unsigned int) block_pointer, void const*) const + 172  
    frame #5: 0x0000000197c9bc44 dyld`invocation function for block in mach_o::Header::forEachSection(void (mach_o::Header::SectionInfo const&, bool&) block_pointer) const + 312  
    frame #6: 0x0000000197c98868 dyld`mach_o::Header::forEachLoadCommand(void (load_command const*, bool&) block_pointer) const + 208  
    frame #7: 0x0000000197c9a100 dyld`mach_o::Header::forEachSection(void (mach_o::Header::SectionInfo const&, bool&) block_pointer) const + 124  
    frame #8: 0x0000000197c71fdc dyld`dyld3::MachOFile::forEachInitializerPointerSection(Diagnostics&, void (unsigned int, unsigned int, bool&) block_pointer) const + 160  
    frame #9: 0x0000000197c7c8e4 dyld`dyld3::MachOAnalyzer::forEachInitializer(Diagnostics&, dyld3::MachOAnalyzer::VMAddrConverter const&, void (unsigned int) block_pointer, void const*) const + 432  
    frame #10: 0x0000000197c3e9dc dyld`dyld4::Loader::findAndRunAllInitializers(dyld4::RuntimeState&) const + 172  
    frame #11: 0x0000000197c467f4 dyld`dyld4::JustInTimeLoader::runInitializers(dyld4::RuntimeState&) const + 36  
    frame #12: 0x0000000197c3f1d0 dyld`dyld4::Loader::runInitializersBottomUp(dyld4::RuntimeState&, lsl::Vector<dyld4::Loader const*>&, lsl::Vector<dyld4::Loader const*>&) const + 380  
    frame #13: 0x0000000197c3f14c dyld`dyld4::Loader::runInitializersBottomUp(dyld4::RuntimeState&, lsl::Vector<dyld4::Loader const*>&, lsl::Vector<dyld4::Loader const*>&) const + 248  
    frame #14: 0x0000000197c43d9c dyld`dyld4::Loader::runInitializersBottomUpPlusUpwardLinks(dyld4::RuntimeState&) const::$_0::operator()() const + 68  
    frame #15: 0x0000000197c3f4b8 dyld`dyld4::Loader::runInitializersBottomUpPlusUpwardLinks(dyld4::RuntimeState&) const + 716  
    frame #16: 0x0000000197c60f8c dyld`dyld4::APIs::runAllInitializersForMain() + 400  
    frame #17: 0x0000000197c23158 dyld`dyld4::prepare(dyld4::APIs&, mach_o::Header const*) + 3112  
    frame #18: 0x0000000197c21d04 dyld`start + 7104  
(lldb) f 1  
warning: libfreeorioncommon.dylib was compiled with optimization - stepping may behave oddly; variables may not be available.  
frame #1: 0x0000000101b26108 libfreeorioncommon.dylib`__cxx_global_var_init.4 at error.hpp:23:46 [opt] [inlined]  
   20  	};  
   21  
   22  	extern BOOST_PROCESS_V2_DECL const error_category& get_utf8_category();  
-> 23  	static const error_category& utf8_category = get_utf8_category();  
   24  
   25  	extern BOOST_PROCESS_V2_DECL const error_category& get_exit_code_category();  
   26  
```

---

## Comment 1

> Username: o01eg  
> Created at: 2026-02-20 13:32:57 UTC  
> Url: https://github.com/boostorg/process/issues/547#issuecomment-3934658782  

Looks like this bug was caused with missing linking.
