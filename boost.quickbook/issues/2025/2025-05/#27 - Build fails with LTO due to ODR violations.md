# #27 - Build fails with LTO due to ODR violations [Open]

> Username: eli-schwartz  
> Created at: 2025-05-06 18:22:13 UTC  
> Updated at: 2025-05-06 19:32:55 UTC  
> Url: https://github.com/boostorg/quickbook/issues/27  

I tried to build with the following *FLAGS to optimize the build: `-flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing`  
  
Link-Time Optimization is a massively global compiler optimization pass which is pretty handy for producing faster executables. It also has the interesting property that because the compiler does whole-program analysis using bytecode, it can save type information and perform error checks that it normally doesn’t have enough insight for. In particular, checking for ODR issues and checking function type signature mismatches.  
  
Note that *all* the -Werror=* flags are used to help detect cases where the compiler tries to optimize by assuming UB cannot exist in the source code -- if it does exist, ordinarily the code would be miscompiled, and this says to make the miscompilation a fatal error.  
  
I got this error:  
```  
    "x86_64-pc-linux-gnu-g++"    -o "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/quickbook" -Wl,--start-group "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/quickbook.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/actions.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/doc_info_actions.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/state.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/dependency_tracker.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/utils.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/files.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/native_text.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/stream.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/glob.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/path.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/include_paths.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/values.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/document_state.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/id_generation.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/id_xml.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/post_process.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/bb2html.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/boostbook_chunker.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/xml_parse.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/html_printer.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/tree.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/collector.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/template_stack.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/code_snippet.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/markups.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/syntax_highlight.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/grammar.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/main_grammar.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/block_element_grammar.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/phrase_element_grammar.o" "../bin.v2/tools/quickbook/src/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/doc_info_grammar.o" "../bin.v2/libs/program_options/build/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/visibility-hidden/libboost_program_options.a" "../bin.v2/libs/filesystem/build/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/visibility-hidden/libboost_filesystem.a" "../bin.v2/libs/atomic/build/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/visibility-hidden/libboost_atomic.a" "../bin.v2/libs/system/build/gcc-14.2/release/x86_64/cxxstd-0x-iso/link-static/pch-off/threading-multi/visibility-hidden/libboost_system.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -m64 -pthread -std=c++0x -Wl,-O1 -Wl,--as-needed -Wl,-z,pack-relative-relocs -flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing -Wl,--defsym=__gentoo_check_ldflags__=0  
  
/var/tmp/portage/dev-libs/boost-1.88.0/work/boost_1_88_0-abi_x86_64.amd64/tools/quickbook/src/parsers.hpp:181:52: error: ‘operator()’ violates the C++ One Definition Rule [-Werror=odr]  
  181 |         scoped_parser_gen<Impl, phoenix::tuple<> > operator()() const  
      |                                                    ^  
/var/tmp/portage/dev-libs/boost-1.88.0/work/boost_1_88_0-abi_x86_64.amd64/tools/quickbook/src/parsers.hpp:181:52: note: return value type mismatch  
  181 |         scoped_parser_gen<Impl, phoenix::tuple<> > operator()() const  
      |                                                    ^  
/var/tmp/portage/dev-libs/boost-1.88.0/work/boost_1_88_0-abi_x86_64.amd64/tools/quickbook/src/parsers.hpp:152:57: note: type name ‘quickbook::scoped_parser_gen<quickbook::to_value_scoped_action, phoenix::tuple<phoenix::nil_t, phoenix::nil_t, phoenix::nil_t, phoenix::nil_t, phoenix::nil_t, phoenix::nil_t, phoenix::nil_t> >’ should match type name ‘quickbook::scoped_parser_gen<quickbook::to_value_scoped_action, phoenix::tuple<phoenix::nil_t, phoenix::nil_t, phoenix::nil_t, phoenix::nil_t> >’  
  152 |     template <typename Impl, typename Arguments> struct scoped_parser_gen  
      |                                                         ^  
/var/tmp/portage/dev-libs/boost-1.88.0/work/boost_1_88_0-abi_x86_64.amd64/tools/quickbook/src/parsers.hpp:181:52: note: ‘operator()’ was previously declared here  
  181 |         scoped_parser_gen<Impl, phoenix::tuple<> > operator()() const  
      |                                                    ^  
/var/tmp/portage/dev-libs/boost-1.88.0/work/boost_1_88_0-abi_x86_64.amd64/tools/quickbook/src/parsers.hpp:181:52: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
lto1: some warnings being treated as errors  
lto-wrapper: fatal error: x86_64-pc-linux-gnu-g++ returned 1 exit status  
compilation terminated.  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: error: lto-wrapper failed  
collect2: error: ld returned 1 exit status  
```  
  
Originally reported downstream: https://bugs.gentoo.org/943975  
Full build log: [build.log](https://github.com/user-attachments/files/19952269/build.log)

---

## Comment 1

> Username: pdimov  
> Created at: 2025-05-06 19:26:59 UTC  
> Url: https://github.com/boostorg/quickbook/issues/27#issuecomment-2855692196  

Reported to Spirit: https://github.com/boostorg/spirit/issues/800

---

## Comment 2

> Username: pdimov  
> Created at: 2025-05-06 19:29:00 UTC  
> Url: https://github.com/boostorg/quickbook/issues/27#issuecomment-2855697101  

If you want to patch this locally, the fix is to change 3 to 6 here:  
  
https://github.com/boostorg/spirit/blob/dcaded05c84db5ecea6485178f77e825df5352f1/include/boost/spirit/home/classic/phoenix/tuples.hpp#L21

---

## Comment 3

> Username: eli-schwartz  
> Created at: 2025-05-06 19:32:53 UTC  
> Url: https://github.com/boostorg/quickbook/issues/27#issuecomment-2855717169  

Thanks for root cause'ing this. Looks like it's been around since 2010... https://github.com/boostorg/spirit/commit/c59544076edc54138d3560980d0290c085fd26f9
