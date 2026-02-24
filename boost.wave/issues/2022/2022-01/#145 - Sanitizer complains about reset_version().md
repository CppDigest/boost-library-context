# #145 - Sanitizer complains about reset_version() [Closed]

> Username: realazthat  
> Created at: 2022-01-26 22:21:41 UTC  
> Updated at: 2022-02-21 16:34:16 UTC  
> Closed at: 2022-02-05 23:46:37 UTC  
> Url: https://github.com/boostorg/wave/issues/145  

Constructing a simple context to process simple source code, with sanitizers on, results in the following report:  
  
```  
==295477==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffef510ce2c at pc 0x7fe257fd109f bp 0x7ffef510cc80 sp 0x7ffef510c410  
WRITE of size 13 at 0x7ffef510ce2c thread T0  
    #0 0x7fe257fd109e in __interceptor_vsprintf ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:1687  
    #1 0x7fe257fd12ce in __interceptor_sprintf ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:1730  
    #2 0x55f1a0c83119 in boost::wave::util::predefined_macros::reset_version() (/mnt/z/.ite-cache/3c45814383cd30ef7a050405df4dd30d/execroot/__main__/bazel-out/k8-dbg/bin/src/ite/main+0x2c6c119)  
    #3 0x55f1a0c83fff in boost::wave::util::predefined_macros::predefined_macros() (/mnt/z/.ite-cache/3c45814383cd30ef7a050405df4dd30d/execroot/__main__/bazel-out/k8-dbg/bin/src/ite/main+0x2c6cfff)  
    #4 0x55f1a0cc9ba1 in boost::wave::util::macromap<boost::wave::context<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::iteration_context_policies::load_file_to_string, ContextPolicy<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::this_type> >::macromap(boost::wave::context<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::iteration_context_policies::load_file_to_string, ContextPolicy<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::this_type>&) external/boost/boost/wave/util/cpp_macromap.hpp:92  
    #5 0x55f1a0ca21f7 in boost::wave::context<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::iteration_context_policies::load_file_to_string, ContextPolicy<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > >, boost::wave::this_type>::context(__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > const&, __gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > const&, char const*, ContextPolicy<boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> > > > > const&) (/mnt/z/.ite-cache/3c45814383cd30ef7a050405df4dd30d/execroot/__main__/bazel-out/k8-dbg/bin/src/ite/main+0x2c8b1f7)  
    #6 0x55f1a0c2fe01 in PreprocessShaderSource(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::filesystem::__cxx11::path const&, absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, std::optional<int> const&, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > const&, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, unsigned long, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, unsigned long> > >*, std::map<unsigned long, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<unsigned long>, std::allocator<std::pair<unsigned long const, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >*) src/gl-common.cxx:1492  
    #7 0x55f1a0c2deae in CompileShaderSource(unsigned int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::filesystem::__cxx11::path const&, absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, std::optional<int> const&, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > const&) src/gl-common.cxx:1123  
    #8 0x55f1a0ba926b in MakeProgram(absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, absl::lts_20211102::Span<std::filesystem::__cxx11::path const>, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::filesystem::__cxx11::path const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::filesystem::__cxx11::path const&, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > const&) src/MrsPaintCraft/common.cxx:173  
    #9 0x55f1a08d1bd5 in operator() src/ite/Rasterize.cxx:104  
    #10 0x55f1a08eae5e in __invoke_impl<util::StatusOr<gl::Program>, RasterizeWithGL::Create(GLFWwindow*, const uvec2&, const aiMesh&)::<lambda(const std::any&)>&, const std::any&> /usr/include/c++/11/bits/invoke.h:61  
    #11 0x55f1a08eaa8e in __invoke_r<util::StatusOr<gl::Program>, RasterizeWithGL::Create(GLFWwindow*, const uvec2&, const aiMesh&)::<lambda(const std::any&)>&, const std::any&> /usr/include/c++/11/bits/invoke.h:116  
    #12 0x55f1a08ea730 in _M_invoke /usr/include/c++/11/bits/std_function.h:292  
    #13 0x55f1a0bbedac in std::function<util::StatusOr<gl::Program> (std::any const&)>::operator()(std::any const&) const /usr/include/c++/11/bits/std_function.h:560  
    #14 0x55f1a0bac87c in CachedProgram::Update(std::any const&, std::optional<util::Status>*) src/MrsPaintCraft/common.cxx:226  
    #15 0x55f1a08d48fa in RasterizeWithGL::Create(GLFWwindow*, glm::vec<2, unsigned int, (glm::qualifier)0> const&, aiMesh const&) src/ite/Rasterize.cxx:126  
    #16 0x55f1a0791e71 in Main(std::filesystem::__cxx11::path const&, std::filesystem::__cxx11::path const&, std::filesystem::__cxx11::path const&) src/ite/main.cxx:138  
    #17 0x55f1a0796bce in main src/ite/main.cxx:241  
    #18 0x7fe256ee20b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
    #19 0x55f1a078290d in _start (/mnt/z/.ite-cache/3c45814383cd30ef7a050405df4dd30d/execroot/__main__/bazel-out/k8-dbg/bin/src/ite/main+0x276b90d)  
  
Address 0x7ffef510ce2c is located in stack of thread T0 at offset 140 in frame  
    #0 0x55f1a0c82ea7 in boost::wave::util::predefined_macros::reset_version() (/mnt/z/.ite-cache/3c45814383cd30ef7a050405df4dd30d/execroot/__main__/bazel-out/k8-dbg/bin/src/ite/main+0x2c6bea7)  
  
  This frame has 2 object(s):  
    [32, 88) 'first_day' (line 128)  
    [128, 140) 'buffer' (line 121) <== Memory access at offset 140 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: stack-buffer-overflow ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:1687 in __interceptor_vsprintf  
Shadow bytes around the buggy address:  
  0x10005ea19970: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005ea19980: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005ea19990: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005ea199a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005ea199b0: 00 00 00 00 f1 f1 f1 f1 00 00 00 00 00 00 00 f2  
=>0x10005ea199c0: f2 f2 f2 f2 00[04]f3 f3 00 00 00 00 00 00 00 00  
  0x10005ea199d0: 00 00 00 00 00 00 f1 f1 f1 f1 f1 f1 f8 f2 f8 f2  
  0x10005ea199e0: f8 f2 f8 f3 f3 f3 00 00 00 00 00 00 00 00 00 00  
  0x10005ea199f0: 00 00 f1 f1 f1 f1 f8 f2 f8 f2 f8 f3 f3 f3 00 00  
  0x10005ea19a00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x10005ea19a10: f1 f1 f1 f1 f8 f2 f8 f2 f8 f3 f3 f3 00 00 00 00  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==295477==ABORTING  
```  
  
I suspect there might be something wrong with the sprintf: https://github.com/boostorg/wave/blob/59610d6f79951fae841b9403fbdf5dfd63b45867/include/boost/wave/util/cpp_macromap_predef.hpp#L138

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-01-26 23:20:54 UTC  
> Url: https://github.com/boostorg/wave/issues/145#issuecomment-1022694066  

Oddly enough I can't reproduce this :( Can you supply a MWE?

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-01-26 23:29:40 UTC  
> Url: https://github.com/boostorg/wave/issues/145#issuecomment-1022698918  

And, in case it's important, compiler and version?

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-01-26 23:37:11 UTC  
> Url: https://github.com/boostorg/wave/issues/145#issuecomment-1022702502  

That said, this is indeed one of the cases flagged as a compiler warning:  
  
```  
../../boost/wave/util/cpp_macromap_predef.hpp:138:42: warning: ‘%04ld’ directive writing between 4 and 16 bytes into a region of size 6 [-Wformat-overflow=]  
  138 |             sprintf(buffer, "0x%02d%1d%1d%04ld", BOOST_WAVE_VERSION_MAJOR,  
      |                                          ^~~~~  
../../boost/wave/util/cpp_macromap_predef.hpp:138:29: note: directive argument in the range [-106751991167300, 106751991167300]  
  138 |             sprintf(buffer, "0x%02d%1d%1d%04ld", BOOST_WAVE_VERSION_MAJOR,  
```

---

## Comment 4

> Username: jefftrull  
> Created at: 2022-01-27 01:26:33 UTC  
> Url: https://github.com/boostorg/wave/issues/145#issuecomment-1022759091  

@realazthat if the above PR fixes your problem there is no need for a MWE. LMK.

---

## Comment 5

> Username: jefftrull  
> Created at: 2022-02-05 23:46:37 UTC  
> Url: https://github.com/boostorg/wave/issues/145#issuecomment-1030715375  

@realazthat it seems like this was resolved for you so I'm closing the ticket.
