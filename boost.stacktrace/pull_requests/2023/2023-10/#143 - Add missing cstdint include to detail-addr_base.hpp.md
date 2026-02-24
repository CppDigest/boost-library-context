# #143 Add missing cstdint include to detail/addr_base.hpp [Merged]

> Username: francesco-ballarin  
> Created at: 2023-10-01 08:18:30 UTC  
> Updated at: 2023-10-20 16:02:54 UTC  
> Merged at: 2023-10-20 16:02:54 UTC  
> Closed at: 2023-10-20 16:02:54 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/143  

Without `cstdint` in `detail/addr_base.hpp` I get a compilation error like  
<details><summary>the following</summary>  
  
```  
2023-10-01T02:32:50.7345885Z In file included from ./boost/stacktrace/detail/addr2line_impls.hpp:15,  
2023-10-01T02:32:50.7346298Z                  from ./boost/stacktrace/detail/frame_unwind.ipp:27,  
2023-10-01T02:32:50.7346680Z                  from libs/stacktrace/build/../src/addr2line.cpp:15:  
2023-10-01T02:32:50.7347250Z ./boost/stacktrace/detail/addr_base.hpp:22:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7347651Z    22 |     uintptr_t start = 0;  
2023-10-01T02:32:50.7347904Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7348462Z ./boost/stacktrace/detail/addr_base.hpp:18:1: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7348895Z    17 | #include <cstdlib>  
2023-10-01T02:32:50.7349173Z   +++ |+#include <cstdint>  
2023-10-01T02:32:50.7349397Z    18 |   
2023-10-01T02:32:50.7349819Z ./boost/stacktrace/detail/addr_base.hpp:23:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7350179Z    23 |     uintptr_t end = 0;  
2023-10-01T02:32:50.7350407Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7350957Z ./boost/stacktrace/detail/addr_base.hpp:23:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7351572Z ./boost/stacktrace/detail/addr_base.hpp:24:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7351954Z    24 |     uintptr_t offset_from_base = 0;  
2023-10-01T02:32:50.7352515Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7353082Z ./boost/stacktrace/detail/addr_base.hpp:24:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7353980Z ./boost/stacktrace/detail/addr_base.hpp: In member function ‘bool boost::stacktrace::detail::mapping_entry_t::contains_addr(const void*) const’:  
2023-10-01T02:32:50.7354655Z ./boost/stacktrace/detail/addr_base.hpp:27:9: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:32:50.7355088Z    27 |         uintptr_t addr_uint = reinterpret_cast<uintptr_t>(addr);  
2023-10-01T02:32:50.7355404Z       |         ^~~~~~~~~  
2023-10-01T02:32:50.7355963Z ./boost/stacktrace/detail/addr_base.hpp:27:9: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7356614Z ./boost/stacktrace/detail/addr_base.hpp:28:16: error: ‘addr_uint’ was not declared in this scope  
2023-10-01T02:32:50.7357045Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:32:50.7357355Z       |                ^~~~~~~~~  
2023-10-01T02:32:50.7357864Z ./boost/stacktrace/detail/addr_base.hpp:28:29: error: ‘start’ was not declared in this scope; did you mean ‘stat’?  
2023-10-01T02:32:50.7358313Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:32:50.7358625Z       |                             ^~~~~  
2023-10-01T02:32:50.7358876Z       |                             stat  
2023-10-01T02:32:50.7359470Z ./boost/stacktrace/detail/addr_base.hpp:28:50: error: ‘end’ was not declared in this scope; did you mean ‘std::end’?  
2023-10-01T02:32:50.7359922Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:32:50.7360237Z       |                                                  ^~~  
2023-10-01T02:32:50.7360515Z       |                                                  std::end  
2023-10-01T02:32:50.7360846Z In file included from /root/.local/include/c++/13/string:53,  
2023-10-01T02:32:50.7361192Z                  from ./boost/stacktrace/frame.hpp:16,  
2023-10-01T02:32:50.7361558Z                  from ./boost/stacktrace/detail/frame_unwind.ipp:15:  
2023-10-01T02:32:50.7362112Z /root/.local/include/c++/13/bits/range_access.h:116:37: note: ‘std::end’ declared here  
2023-10-01T02:32:50.7362551Z   116 |   template<typename _Tp> const _Tp* end(const valarray<_Tp>&) noexcept;  
2023-10-01T02:32:50.7363077Z       |                                     ^~~  
2023-10-01T02:32:50.7363399Z ./boost/stacktrace/detail/addr_base.hpp: At global scope:  
2023-10-01T02:32:50.7363918Z ./boost/stacktrace/detail/addr_base.hpp:32:8: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7364346Z    32 | inline uintptr_t hex_str_to_int(const std::string& str) {  
2023-10-01T02:32:50.7364651Z       |        ^~~~~~~~~  
2023-10-01T02:32:50.7365210Z ./boost/stacktrace/detail/addr_base.hpp:32:8: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7366058Z ./boost/stacktrace/detail/addr_base.hpp: In function ‘boost::stacktrace::detail::mapping_entry_t boost::stacktrace::detail::parse_proc_maps_line(const std::string&)’:  
2023-10-01T02:32:50.7366897Z ./boost/stacktrace/detail/addr_base.hpp:66:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘start’  
2023-10-01T02:32:50.7367370Z    66 |         mapping.start = hex_str_to_int(mapping_start_str);  
2023-10-01T02:32:50.7367688Z       |                 ^~~~~  
2023-10-01T02:32:50.7368169Z ./boost/stacktrace/detail/addr_base.hpp:66:25: error: ‘hex_str_to_int’ was not declared in this scope  
2023-10-01T02:32:50.7368613Z    66 |         mapping.start = hex_str_to_int(mapping_start_str);  
2023-10-01T02:32:50.7368935Z       |                         ^~~~~~~~~~~~~~  
2023-10-01T02:32:50.7376878Z ./boost/stacktrace/detail/addr_base.hpp:67:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘end’  
2023-10-01T02:32:50.7377387Z    67 |         mapping.end = hex_str_to_int(mapping_end_str);  
2023-10-01T02:32:50.7377922Z       |                 ^~~  
2023-10-01T02:32:50.7378548Z ./boost/stacktrace/detail/addr_base.hpp:68:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘offset_from_base’  
2023-10-01T02:32:50.7379077Z    68 |         mapping.offset_from_base = hex_str_to_int(offset_from_base_str);  
2023-10-01T02:32:50.7379424Z       |                 ^~~~~~~~~~~~~~~~  
2023-10-01T02:32:50.7379740Z ./boost/stacktrace/detail/addr_base.hpp: At global scope:  
2023-10-01T02:32:50.7380247Z ./boost/stacktrace/detail/addr_base.hpp:75:8: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7380674Z    75 | inline uintptr_t get_own_proc_addr_base(const void* addr) {  
2023-10-01T02:32:50.7380978Z       |        ^~~~~~~~~  
2023-10-01T02:32:50.7381521Z ./boost/stacktrace/detail/addr_base.hpp:75:8: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7382308Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::source_location(const void*, bool)’:  
2023-10-01T02:32:50.7382985Z ./boost/stacktrace/detail/addr2line_impls.hpp:160:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:32:50.7383383Z   160 |     uintptr_t addr_base = 0;  
2023-10-01T02:32:50.7383885Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7384485Z ./boost/stacktrace/detail/addr2line_impls.hpp:24:1: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7384919Z    23 | #include <sys/wait.h>  
2023-10-01T02:32:50.7385168Z   +++ |+#include <cstdint>  
2023-10-01T02:32:50.7385425Z    24 | #include <signal.h>  
2023-10-01T02:32:50.7385924Z ./boost/stacktrace/detail/addr2line_impls.hpp:162:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7386397Z   162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7386723Z       |         ^~~~~~~~~  
2023-10-01T02:32:50.7387339Z ./boost/stacktrace/detail/addr2line_impls.hpp:162:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:32:50.7387855Z   162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7388203Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:32:50.7388875Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7389483Z   164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7389891Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:32:50.7390505Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7391185Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7391801Z   164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7394778Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:32:50.7395428Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::name(const void*, bool)’:  
2023-10-01T02:32:50.7396093Z ./boost/stacktrace/detail/addr2line_impls.hpp:207:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:32:50.7396484Z   207 |     uintptr_t addr_base = 0;  
2023-10-01T02:32:50.7396738Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7397307Z ./boost/stacktrace/detail/addr2line_impls.hpp:207:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7397977Z ./boost/stacktrace/detail/addr2line_impls.hpp:209:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7398449Z   209 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7398924Z       |         ^~~~~~~~~  
2023-10-01T02:32:50.7399549Z ./boost/stacktrace/detail/addr2line_impls.hpp:209:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:32:50.7400054Z   209 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7400428Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:32:50.7400924Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7401525Z   211 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7401924Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:32:50.7402524Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7403209Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7403814Z   211 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7404223Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:32:50.7404978Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::source_file(const void*, bool)’:  
2023-10-01T02:32:50.7405634Z ./boost/stacktrace/detail/addr2line_impls.hpp:234:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:32:50.7406022Z   234 |     uintptr_t addr_base = 0;  
2023-10-01T02:32:50.7406276Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7406838Z ./boost/stacktrace/detail/addr2line_impls.hpp:234:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7407506Z ./boost/stacktrace/detail/addr2line_impls.hpp:236:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7407994Z   236 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7408317Z       |         ^~~~~~~~~  
2023-10-01T02:32:50.7408924Z ./boost/stacktrace/detail/addr2line_impls.hpp:236:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:32:50.7409438Z   236 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7409800Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:32:50.7410278Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7410874Z   238 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7411280Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:32:50.7412030Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7412706Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7413321Z   238 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7413750Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:32:50.7414382Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::size_t boost::stacktrace::detail::source_line(const void*, bool)’:  
2023-10-01T02:32:50.7415024Z ./boost/stacktrace/detail/addr2line_impls.hpp:250:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:32:50.7415418Z   250 |     uintptr_t addr_base = 0;  
2023-10-01T02:32:50.7415676Z       |     ^~~~~~~~~  
2023-10-01T02:32:50.7416245Z ./boost/stacktrace/detail/addr2line_impls.hpp:250:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7416925Z ./boost/stacktrace/detail/addr2line_impls.hpp:252:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7417394Z   252 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7417728Z       |         ^~~~~~~~~  
2023-10-01T02:32:50.7418323Z ./boost/stacktrace/detail/addr2line_impls.hpp:252:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:32:50.7418825Z   252 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:32:50.7419183Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:32:50.7419674Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:32:50.7420269Z   254 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7420732Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:32:50.7421348Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:32:50.7422145Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:32:50.7422748Z   254 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:32:50.7423164Z        
  
2023-10-01T02:38:21.4489401Z In file included from ./boost/stacktrace/detail/addr2line_impls.hpp:15,  
2023-10-01T02:38:21.4489807Z                  from ./boost/stacktrace/detail/frame_unwind.ipp:27,  
2023-10-01T02:38:21.4490184Z                  from libs/stacktrace/build/../src/addr2line.cpp:15:  
2023-10-01T02:38:21.4490755Z ./boost/stacktrace/detail/addr_base.hpp:22:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4491124Z    22 |     uintptr_t start = 0;  
2023-10-01T02:38:21.4491383Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4491956Z ./boost/stacktrace/detail/addr_base.hpp:18:1: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4492381Z    17 | #include <cstdlib>  
2023-10-01T02:38:21.4492639Z   +++ |+#include <cstdint>  
2023-10-01T02:38:21.4492846Z    18 |   
2023-10-01T02:38:21.4493268Z ./boost/stacktrace/detail/addr_base.hpp:23:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4493880Z    23 |     uintptr_t end = 0;  
2023-10-01T02:38:21.4494239Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4494800Z ./boost/stacktrace/detail/addr_base.hpp:23:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4495422Z ./boost/stacktrace/detail/addr_base.hpp:24:5: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4495789Z    24 |     uintptr_t offset_from_base = 0;  
2023-10-01T02:38:21.4496055Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4496611Z ./boost/stacktrace/detail/addr_base.hpp:24:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4497512Z ./boost/stacktrace/detail/addr_base.hpp: In member function ‘bool boost::stacktrace::detail::mapping_entry_t::contains_addr(const void*) const’:  
2023-10-01T02:38:21.4498174Z ./boost/stacktrace/detail/addr_base.hpp:27:9: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:38:21.4498625Z    27 |         uintptr_t addr_uint = reinterpret_cast<uintptr_t>(addr);  
2023-10-01T02:38:21.4498951Z       |         ^~~~~~~~~  
2023-10-01T02:38:21.4499490Z ./boost/stacktrace/detail/addr_base.hpp:27:9: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4500147Z ./boost/stacktrace/detail/addr_base.hpp:28:16: error: ‘addr_uint’ was not declared in this scope  
2023-10-01T02:38:21.4500569Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:38:21.4500861Z       |                ^~~~~~~~~  
2023-10-01T02:38:21.4501382Z ./boost/stacktrace/detail/addr_base.hpp:28:29: error: ‘start’ was not declared in this scope; did you mean ‘stat’?  
2023-10-01T02:38:21.4501826Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:38:21.4502123Z       |                             ^~~~~  
2023-10-01T02:38:21.4502360Z       |                             stat  
2023-10-01T02:38:21.4502954Z ./boost/stacktrace/detail/addr_base.hpp:28:50: error: ‘end’ was not declared in this scope; did you mean ‘std::end’?  
2023-10-01T02:38:21.4503424Z    28 |         return addr_uint >= start && addr_uint < end;  
2023-10-01T02:38:21.4516647Z       |                                                  ^~~  
2023-10-01T02:38:21.4517101Z       |                                                  std::end  
2023-10-01T02:38:21.4517680Z In file included from /root/.local/include/c++/13/string:53,  
2023-10-01T02:38:21.4518045Z                  from ./boost/stacktrace/frame.hpp:16,  
2023-10-01T02:38:21.4518405Z                  from ./boost/stacktrace/detail/frame_unwind.ipp:15:  
2023-10-01T02:38:21.4519217Z /root/.local/include/c++/13/bits/range_access.h:116:37: note: ‘std::end’ declared here  
2023-10-01T02:38:21.4519665Z   116 |   template<typename _Tp> const _Tp* end(const valarray<_Tp>&) noexcept;  
2023-10-01T02:38:21.4520018Z       |                                     ^~~  
2023-10-01T02:38:21.4520341Z ./boost/stacktrace/detail/addr_base.hpp: At global scope:  
2023-10-01T02:38:21.4520863Z ./boost/stacktrace/detail/addr_base.hpp:32:8: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4521298Z    32 | inline uintptr_t hex_str_to_int(const std::string& str) {  
2023-10-01T02:38:21.4521601Z       |        ^~~~~~~~~  
2023-10-01T02:38:21.4522151Z ./boost/stacktrace/detail/addr_base.hpp:32:8: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4523028Z ./boost/stacktrace/detail/addr_base.hpp: In function ‘boost::stacktrace::detail::mapping_entry_t boost::stacktrace::detail::parse_proc_maps_line(const std::string&)’:  
2023-10-01T02:38:21.4523864Z ./boost/stacktrace/detail/addr_base.hpp:66:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘start’  
2023-10-01T02:38:21.4524357Z    66 |         mapping.start = hex_str_to_int(mapping_start_str);  
2023-10-01T02:38:21.4524666Z       |                 ^~~~~  
2023-10-01T02:38:21.4525147Z ./boost/stacktrace/detail/addr_base.hpp:66:25: error: ‘hex_str_to_int’ was not declared in this scope  
2023-10-01T02:38:21.4525686Z    66 |         mapping.start = hex_str_to_int(mapping_start_str);  
2023-10-01T02:38:21.4526065Z       |                         ^~~~~~~~~~~~~~  
2023-10-01T02:38:21.4536612Z ./boost/stacktrace/detail/addr_base.hpp:67:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘end’  
2023-10-01T02:38:21.4537136Z    67 |         mapping.end = hex_str_to_int(mapping_end_str);  
2023-10-01T02:38:21.4537433Z       |                 ^~~  
2023-10-01T02:38:21.4538068Z ./boost/stacktrace/detail/addr_base.hpp:68:17: error: ‘struct boost::stacktrace::detail::mapping_entry_t’ has no member named ‘offset_from_base’  
2023-10-01T02:38:21.4538609Z    68 |         mapping.offset_from_base = hex_str_to_int(offset_from_base_str);  
2023-10-01T02:38:21.4538948Z       |                 ^~~~~~~~~~~~~~~~  
2023-10-01T02:38:21.4539250Z ./boost/stacktrace/detail/addr_base.hpp: At global scope:  
2023-10-01T02:38:21.4539765Z ./boost/stacktrace/detail/addr_base.hpp:75:8: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4540209Z    75 | inline uintptr_t get_own_proc_addr_base(const void* addr) {  
2023-10-01T02:38:21.4540519Z       |        ^~~~~~~~~  
2023-10-01T02:38:21.4541080Z ./boost/stacktrace/detail/addr_base.hpp:75:8: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4541868Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::source_location(const void*, bool)’:  
2023-10-01T02:38:21.4542544Z ./boost/stacktrace/detail/addr2line_impls.hpp:160:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:38:21.4543056Z   160 |     uintptr_t addr_base = 0;  
2023-10-01T02:38:21.4543314Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4544063Z ./boost/stacktrace/detail/addr2line_impls.hpp:24:1: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4544495Z    23 | #include <sys/wait.h>  
2023-10-01T02:38:21.4544763Z   +++ |+#include <cstdint>  
2023-10-01T02:38:21.4545017Z    24 | #include <signal.h>  
2023-10-01T02:38:21.4545509Z ./boost/stacktrace/detail/addr2line_impls.hpp:162:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4545984Z   162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4546424Z       |         ^~~~~~~~~  
2023-10-01T02:38:21.4547050Z ./boost/stacktrace/detail/addr2line_impls.hpp:162:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:38:21.4547567Z   162 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4547930Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:38:21.4548421Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4549017Z   164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4549416Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:38:21.4550041Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4550716Z ./boost/stacktrace/detail/addr2line_impls.hpp:164:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4551328Z   164 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4551751Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:38:21.4552363Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::name(const void*, bool)’:  
2023-10-01T02:38:21.4553006Z ./boost/stacktrace/detail/addr2line_impls.hpp:207:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:38:21.4553471Z   207 |     uintptr_t addr_base = 0;  
2023-10-01T02:38:21.4553724Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4554383Z ./boost/stacktrace/detail/addr2line_impls.hpp:207:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4555046Z ./boost/stacktrace/detail/addr2line_impls.hpp:209:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4555524Z   209 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4555848Z       |         ^~~~~~~~~  
2023-10-01T02:38:21.4556457Z ./boost/stacktrace/detail/addr2line_impls.hpp:209:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:38:21.4556959Z   209 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4557318Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:38:21.4557804Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4558401Z   211 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4558808Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:38:21.4559421Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4560092Z ./boost/stacktrace/detail/addr2line_impls.hpp:211:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4560763Z   211 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4561174Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:38:21.4561810Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::string boost::stacktrace::detail::source_file(const void*, bool)’:  
2023-10-01T02:38:21.4562472Z ./boost/stacktrace/detail/addr2line_impls.hpp:234:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:38:21.4562909Z   234 |     uintptr_t addr_base = 0;  
2023-10-01T02:38:21.4563165Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4563741Z ./boost/stacktrace/detail/addr2line_impls.hpp:234:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4564489Z ./boost/stacktrace/detail/addr2line_impls.hpp:236:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4564951Z   236 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4565274Z       |         ^~~~~~~~~  
2023-10-01T02:38:21.4565888Z ./boost/stacktrace/detail/addr2line_impls.hpp:236:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:38:21.4566390Z   236 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4566757Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:38:21.4567724Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4568326Z   238 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4568720Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:38:21.4569338Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4570020Z ./boost/stacktrace/detail/addr2line_impls.hpp:238:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4570637Z   238 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4571047Z       |                                                                                      ^~~~~~~~~  
2023-10-01T02:38:21.4571782Z ./boost/stacktrace/detail/addr2line_impls.hpp: In function ‘std::size_t boost::stacktrace::detail::source_line(const void*, bool)’:  
2023-10-01T02:38:21.4572541Z ./boost/stacktrace/detail/addr2line_impls.hpp:250:5: error: ‘uintptr_t’ was not declared in this scope  
2023-10-01T02:38:21.4572938Z   250 |     uintptr_t addr_base = 0;  
2023-10-01T02:38:21.4573179Z       |     ^~~~~~~~~  
2023-10-01T02:38:21.4573752Z ./boost/stacktrace/detail/addr2line_impls.hpp:250:5: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4574422Z ./boost/stacktrace/detail/addr2line_impls.hpp:252:9: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4574893Z   252 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4575215Z       |         ^~~~~~~~~  
2023-10-01T02:38:21.4575832Z ./boost/stacktrace/detail/addr2line_impls.hpp:252:48: error: ‘get_own_proc_addr_base’ is not a member of ‘boost::stacktrace::detail’  
2023-10-01T02:38:21.4576334Z   252 |         addr_base = boost::stacktrace::detail::get_own_proc_addr_base(addr);  
2023-10-01T02:38:21.4576689Z       |                                                ^~~~~~~~~~~~~~~~~~~~~~  
2023-10-01T02:38:21.4577177Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:67: error: ‘uintptr_t’ does not name a type  
2023-10-01T02:38:21.4577781Z   254 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4578177Z       |                                                                   ^~~~~~~~~  
2023-10-01T02:38:21.4578849Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:67: note: ‘uintptr_t’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
2023-10-01T02:38:21.4579520Z ./boost/stacktrace/detail/addr2line_impls.hpp:254:86: error: ‘addr_base’ was not declared in this scope  
2023-10-01T02:38:21.4580130Z   254 |     const void* offset = reinterpret_cast<void*>(reinterpret_cast<uintptr_t>(addr) - addr_base);  
2023-10-01T02:38:21.4580542Z        
```  
</details>  
  
when building with `gcc 13.1.0`. Earlier versions of `gcc`, and in particular `12.3.0`, do not seem to be affected.

---

## Comment 1

> Username: coveralls  
> Created_at: 2023-10-20 15:12:03 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/143#issuecomment-1772925186  

[![Coverage Status](https://coveralls.io/builds/63437013/badge)](https://coveralls.io/builds/63437013)  
  
coverage: 92.573%. remained the same when pulling **8b1699c9d3ad564607030f98a1f9599bc0d6256a on francesco-ballarin:develop** into **dc5cd9d1f393299e5210d0acb92e97bf455e5e9a on boostorg:develop**.

---
