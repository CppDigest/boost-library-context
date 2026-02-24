# #1820 - could not shutdown the connection: application data after close notify [Closed]

> Username: Xeverous  
> Created at: 2020-01-23 18:28:41 UTC  
> Updated at: 2020-03-06 23:45:02 UTC  
> Closed at: 2020-03-06 23:45:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1820  

### Version of Beast  
  
266  
  
### Steps necessary to reproduce the problem  
  
Basically [http_client_async_ssl](https://www.boost.org/doc/libs/1_72_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp) example with minor changes to accomodate `std::future` and https://stackoverflow.com/questions/50348516/boost-beast-message-with-body-limit solution  
  
Actual code: https://github.com/Xeverous/filter_spirit/blob/body-limit-bugfix/src/lib/fs/network/http.cpp  
  
### All relevant compiler information  
  
GCC 9.2.1 (2019.11.26)  
  
### description  
  
After applying changes from linked SO question to fix the issue of body limit error I'm now gettting `could not shutdown the connection: application data after close notify`. I know one of the targets I query against has the SSL stream truncation problem but this error is already ignored.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-24 11:49:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-578099678  

@Xeverous Thanks for reporting this issue. I am investigating now.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-01-27 08:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-578646876  

Hi @Xeverous Could you let me know which target in the filter_spirit project I should build and whether I need to supply any command line arguments?  
  
I have set up a branch for testing on a forket repo here:  
https://github.com/test-scenarios/filter_spirit/tree/body-limit-bugfix  
  
You will see that I have added a small commit. Basically all this does is ensure that cmake finds and builds all dependencies locally. It means I don't have to install the dependencies on my system.  
  
When building the executable `filter_spirit_test` I am getting a lot of compiler errors:  
  
```  
====================[ Build | filter_spirit_test | Debug ]======================  
/home/rhodges/.local/share/JetBrains/Toolbox/apps/CLion/ch-0/193.6015.37/bin/cmake/linux/bin/cmake --build /home/rhodges/github/test-scenarios/filter_spirit/cmake-build-debug --target filter_spirit_test -- -j 6  
[  2%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/parser/parser.cpp.o  
[  4%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/parser/print_error.cpp.o  
[  6%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/parser/detail/grammar.cpp.o  
[  8%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/build_filter_blocks.cpp.o  
[ 11%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/resolve_symbols.cpp.o  
[ 13%] Building CXX object src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/print_error.cpp.o  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:2,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:60:27: error: declaration of ‘std::optional<fs::lang::font_size> fs::lang::action_set::font_size’ changes meaning of ‘font_size’ [-fpermissive]  
   60 |  std::optional<font_size> font_size;  
      |                           ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:2,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:55:8: note: ‘font_size’ declared here as ‘struct fs::lang::font_size’  
   55 | struct font_size  
      |        ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:61:29: error: declaration of ‘std::optional<fs::lang::alert_sound> fs::lang::action_set::alert_sound’ changes meaning of ‘alert_sound’ [-fpermissive]  
   61 |  std::optional<alert_sound> alert_sound;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:2,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:240:8: note: ‘alert_sound’ declared here as ‘struct fs::lang::alert_sound’  
  240 | struct alert_sound  
      |        ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:63:30: error: declaration of ‘std::optional<fs::lang::minimap_icon> fs::lang::action_set::minimap_icon’ changes meaning of ‘minimap_icon’ [-fpermissive]  
   63 |  std::optional<minimap_icon> minimap_icon;  
      |                              ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:2,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:139:8: note: ‘minimap_icon’ declared here as ‘struct fs::lang::minimap_icon’  
  139 | struct minimap_icon  
      |        ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:64:29: error: declaration of ‘std::optional<fs::lang::beam_effect> fs::lang::action_set::beam_effect’ changes meaning of ‘beam_effect’ [-fpermissive]  
   64 |  std::optional<beam_effect> beam_effect;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.hpp:2,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/build_filter_blocks.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:158:8: note: ‘beam_effect’ declared here as ‘struct fs::lang::beam_effect’  
  158 | struct beam_effect  
      |        ^~~~~~~~~~~  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:102: src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/build_filter_blocks.cpp.o] Error 1  
gmake[3]: *** Waiting for unfinished jobs....  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:65:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_success_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   65 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:70:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_failure_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   70 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.cpp:3:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger.hpp:40:10: error: declaration of ‘fs::log::logger& fs::log::logger_wrapper::logger’ changes meaning of ‘logger’ [-fpermissive]  
   40 |  logger& logger;  
      |          ^~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger_fwd.hpp:6:7: note: ‘logger’ declared here as ‘class fs::log::logger’  
    6 | class logger;  
      |       ^~~~~~  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:76: src/lib/CMakeFiles/filter_spirit.dir/fs/parser/print_error.cpp.o] Error 1  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:60:27: error: declaration of ‘std::optional<fs::lang::font_size> fs::lang::action_set::font_size’ changes meaning of ‘font_size’ [-fpermissive]  
   60 |  std::optional<font_size> font_size;  
      |                           ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:55:8: note: ‘font_size’ declared here as ‘struct fs::lang::font_size’  
   55 | struct font_size  
      |        ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:61:29: error: declaration of ‘std::optional<fs::lang::alert_sound> fs::lang::action_set::alert_sound’ changes meaning of ‘alert_sound’ [-fpermissive]  
   61 |  std::optional<alert_sound> alert_sound;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:240:8: note: ‘alert_sound’ declared here as ‘struct fs::lang::alert_sound’  
  240 | struct alert_sound  
      |        ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:63:30: error: declaration of ‘std::optional<fs::lang::minimap_icon> fs::lang::action_set::minimap_icon’ changes meaning of ‘minimap_icon’ [-fpermissive]  
   63 |  std::optional<minimap_icon> minimap_icon;  
      |                              ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:139:8: note: ‘minimap_icon’ declared here as ‘struct fs::lang::minimap_icon’  
  139 | struct minimap_icon  
      |        ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:64:29: error: declaration of ‘std::optional<fs::lang::beam_effect> fs::lang::action_set::beam_effect’ changes meaning of ‘beam_effect’ [-fpermissive]  
   64 |  std::optional<beam_effect> beam_effect;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/resolve_symbols.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:158:8: note: ‘beam_effect’ declared here as ‘struct fs::lang::beam_effect’  
  158 | struct beam_effect  
      |        ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:65:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_success_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   65 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:70:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_failure_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   70 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.cpp:4:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger.hpp:40:10: error: declaration of ‘fs::log::logger& fs::log::logger_wrapper::logger’ changes meaning of ‘logger’ [-fpermissive]  
   40 |  logger& logger;  
      |          ^~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger_fwd.hpp:6:7: note: ‘logger’ declared here as ‘class fs::log::logger’  
    6 | class logger;  
      |       ^~~~~~  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:115: src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/resolve_symbols.cpp.o] Error 1  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:63: src/lib/CMakeFiles/filter_spirit.dir/fs/parser/parser.cpp.o] Error 1  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:65:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_success_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   65 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:70:14: error: declaration of ‘fs::parser::lookup_data fs::parser::parse_failure_data::lookup_data’ changes meaning of ‘lookup_data’ [-fpermissive]  
   70 |  lookup_data lookup_data;  
      |              ^~~~~~~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:16:7: note: ‘lookup_data’ declared here as ‘class fs::parser::lookup_data’  
   16 | class lookup_data  
      |       ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:60:27: error: declaration of ‘std::optional<fs::lang::font_size> fs::lang::action_set::font_size’ changes meaning of ‘font_size’ [-fpermissive]  
   60 |  std::optional<font_size> font_size;  
      |                           ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:55:8: note: ‘font_size’ declared here as ‘struct fs::lang::font_size’  
   55 | struct font_size  
      |        ^~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:61:29: error: declaration of ‘std::optional<fs::lang::alert_sound> fs::lang::action_set::alert_sound’ changes meaning of ‘alert_sound’ [-fpermissive]  
   61 |  std::optional<alert_sound> alert_sound;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:240:8: note: ‘alert_sound’ declared here as ‘struct fs::lang::alert_sound’  
  240 | struct alert_sound  
      |        ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:63:30: error: declaration of ‘std::optional<fs::lang::minimap_icon> fs::lang::action_set::minimap_icon’ changes meaning of ‘minimap_icon’ [-fpermissive]  
   63 |  std::optional<minimap_icon> minimap_icon;  
      |                              ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:139:8: note: ‘minimap_icon’ declared here as ‘struct fs::lang::minimap_icon’  
  139 | struct minimap_icon  
      |        ^~~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/object.hpp:7,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:4,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/action_set.hpp:64:29: error: declaration of ‘std::optional<fs::lang::beam_effect> fs::lang::action_set::beam_effect’ changes meaning of ‘beam_effect’ [-fpermissive]  
   64 |  std::optional<beam_effect> beam_effect;  
      |                             ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:158:8: note: ‘beam_effect’ declared here as ‘struct fs::lang::beam_effect’  
  158 | struct beam_effect  
      |        ^~~~~~~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:3:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger.hpp:40:10: error: declaration of ‘fs::log::logger& fs::log::logger_wrapper::logger’ changes meaning of ‘logger’ [-fpermissive]  
   40 |  logger& logger;  
      |          ^~~~~~  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/parser.hpp:6,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.hpp:3,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/compiler/print_error.cpp:1:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/log/logger_fwd.hpp:6:7: note: ‘logger’ declared here as ‘class fs::log::logger’  
    6 | class logger;  
      |       ^~~~~~  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:128: src/lib/CMakeFiles/filter_spirit.dir/fs/compiler/print_error.cpp.o] Error 1  
In file included from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast.hpp:8,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/ast_adapted.hpp:15,  
                 from /home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/parser/detail/grammar.cpp:7:  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:149:8: error: declaration of ‘fs::lang::shape fs::lang::minimap_icon::shape’ changes meaning of ‘shape’ [-fpermissive]  
  149 |  shape shape;  
      |        ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:110:12: note: ‘shape’ declared here as ‘enum class fs::lang::shape’  
  110 | enum class shape { circle, diamond, hexagon, square, star, triangle };  
      |            ^~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:216:24: error: declaration of ‘std::optional<fs::lang::volume> fs::lang::built_in_alert_sound::volume’ changes meaning of ‘volume’ [-fpermissive]  
  216 |  std::optional<volume> volume;  
      |                        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:77:8: note: ‘volume’ declared here as ‘struct fs::lang::volume’  
   77 | struct volume  
      |        ^~~~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:234:7: error: declaration of ‘fs::lang::path fs::lang::custom_alert_sound::path’ changes meaning of ‘path’ [-fpermissive]  
  234 |  path path;  
      |       ^~~~  
/home/rhodges/github/test-scenarios/filter_spirit/src/lib/fs/lang/primitive_types.hpp:184:8: note: ‘path’ declared here as ‘struct fs::lang::path’  
  184 | struct path  
      |        ^~~~  
gmake[3]: *** [src/lib/CMakeFiles/filter_spirit.dir/build.make:89: src/lib/CMakeFiles/filter_spirit.dir/fs/parser/detail/grammar.cpp.o] Error 1  
gmake[2]: *** [CMakeFiles/Makefile2:902: src/lib/CMakeFiles/filter_spirit.dir/all] Error 2  
gmake[1]: *** [CMakeFiles/Makefile2:999: src/test/CMakeFiles/filter_spirit_test.dir/rule] Error 2  
gmake: *** [Makefile:508: filter_spirit_test] Error 2  
```

---

## Comment 3

> Username: Xeverous  
> Created at: 2020-01-27 10:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-578693069  

> Could you let me know which target in the filter_spirit project I should build and whether I need to supply any command line arguments?  
  
There are 3 targets currently:  
  
- main library  
- command line executable  
- command line test runner  
  
Since the tests are only for the parser+genenator functionality (no networking tests currently) and both executables depend on the library, you should build the library and the command line executable. My CMake has no switch for the core library, because it is being used by all targets:  
  
https://github.com/Xeverous/filter_spirit/blob/b13b4d12f80c76c00dc637fedb357984f0cbd4a9/CMakeLists.txt#L26-L31  
  
So you can disable tests at most.  
  
There are no command-line flags required. Everything is exposed in CMake as target properties. The only significant flags are include paths and C++17 switch.  
  
I haven't thought about preparing the project for a debug like this but fortunately one of the program functionalities is to just download some data from external APIs and save it to a specified path (`./filter_spirit_cli -n Standard -s some/dir/path`)  
  
___  
  
Regarding the errors you get - what compiler and version are you using? It builds for me using GCC 9.2.1  
  
Not sure whether these errors are correct, but I should probably change them because there is a lot of shadowing and some objects have the same identifiers as types.

---

## Comment 4

> Username: Xeverous  
> Created at: 2020-01-28 23:14:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-579517986  

I have extracted some code from my own project. This single-source program always reproduces the problem for me. Tested on Windows, Beast version 266, GCC 9.2.1  
  
```cpp  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/error.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/system/system_error.hpp>  
  
#include <limits>  
#include <memory>  
#include <future>  
#include <stdexcept>  
#include <string_view>  
#include <iostream>  
  
namespace  
{  
  
// based on:  
// https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/examples.html#beast.examples.clients  
  
using tcp = boost::asio::ip::tcp; // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl; // from <boost/asio/ssl.hpp>  
namespace http = boost::beast::http; // from <boost/beast/http.hpp>  
  
using response_type = http::response_parser<http::string_body>;  
using result_type = std::vector<std::string>;  
  
class session: public std::enable_shared_from_this<session>  
{  
public:  
	explicit  
	session(boost::asio::io_context& ioc, ssl::context& ctx)  
	: resolver(ioc), stream(ioc, ctx)  
	{  
	}  
  
	[[nodiscard]] std::future<result_type>  
	async_http_get(  
		const char* host,  
		std::vector<std::string> targets);  
  
	void on_resolve(  
		boost::system::error_code ec,  
		tcp::resolver::results_type results);  
  
	void on_connect(boost::system::error_code ec);  
  
	void on_handshake(boost::system::error_code ec);  
  
	void on_write(  
		boost::system::error_code ec,  
		std::size_t bytes_transferred);  
  
	void on_read(  
		boost::system::error_code ec,  
		std::size_t bytes_transferred);  
  
	void on_shutdown(boost::system::error_code ec);  
  
private:  
	void next_request();  
  
	tcp::resolver resolver;  
	ssl::stream<tcp::socket> stream;  
	boost::beast::flat_buffer buffer; // (Must persist between reads)  
	http::request<http::empty_body> request; // HTTP GET request  
	response_type response;                  // returned HTTP content  
	std::vector<std::string> targets;        // array of links  
	result_type results;        // array of downloaded data  
	std::promise<result_type> promise;  
};  
  
  
std::future<result_type> session::async_http_get(  
	const char* host,  
	std::vector<std::string> targets)  
{  
	// save targets that will be used in each request+response pair  
	results.reserve(targets.size());  
	this->targets = std::move(targets);  
  
	// Set SNI Hostname (many hosts need this to handshake successfully)  
	if (!SSL_set_tlsext_host_name(stream.native_handle(), host)) {  
		boost::system::error_code ec{static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category()};  
		throw boost::system::system_error(ec);  
	}  
  
	// Set up an HTTP GET request message  
	request.version(11); // HTTP 1.1  
	request.method(http::verb::get);  
	request.set(http::field::host, host);  
	request.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
	// request.keep_alive(true); // redundant in HTTP 1.1  
  
	// Look up the domain name  
	resolver.async_resolve(  
		host,  
		"443", // default HTTP port  
		[self = shared_from_this()]  
		(boost::system::error_code ec, tcp::resolver::results_type results) {  
			self->on_resolve(ec, results);  
		});  
  
	return promise.get_future();  
}  
  
void session::on_resolve(  
	boost::system::error_code ec,  
	tcp::resolver::results_type results)  
{  
	if (ec)  
		throw boost::system::system_error(ec, "could not resolve host name");  
  
	// Make the connection on the IP address we get from a lookup  
	boost::asio::async_connect(  
		stream.next_layer(),  
		results.begin(),  
		results.end(),  
		std::bind( // lambda does not compile here - beast bug probably (example changed between 1.69 and 1.70)  
			&session::on_connect,  
			shared_from_this(),  
			std::placeholders::_1));  
}  
  
void session::on_connect(boost::system::error_code ec)  
{  
	if (ec)  
		throw boost::system::system_error(ec, "could not connect to the host");  
  
	// Perform the SSL handshake  
	stream.async_handshake(  
		ssl::stream_base::client,  
		[self = shared_from_this()](boost::system::error_code ec) {  
			self->on_handshake(ec);  
		});  
}  
  
void session::on_handshake(boost::system::error_code ec)  
{  
	if (ec)  
		throw boost::system::system_error(ec, "could not handshake with the target");  
  
	next_request();  
}  
  
void session::next_request()  
{  
	if (results.size() == targets.size()) {  
		// we are done, we downloaded from all targets  
		promise.set_value(std::move(results));  
		results.clear();  
  
		// Gracefully close the stream  
		stream.async_shutdown(  
			[self = shared_from_this()](boost::system::error_code ec) {  
				self->on_shutdown(ec);  
			});  
		return;  
	}  
  
	const auto& current_target = targets[results.size()];  
	request.target(boost::beast::string_view(current_target.data(), current_target.size()));  
  
	std::cout << "REQUEST:\n" << request << "\n";  
  
	// Send the HTTP request to the remote host  
	http::async_write(  
		stream,  
		request,  
		[self = shared_from_this()]  
		(boost::system::error_code ec, std::size_t bytes_transferred) {  
			self->on_write(ec, bytes_transferred);  
		});  
}  
  
void session::on_write(  
	boost::system::error_code ec,  
	std::size_t /* bytes_transferred */)  
{  
	if (ec)  
		throw boost::system::system_error(ec, "could not write the request");  
  
	// We want unlimited body size  
	// Some of the data we get is too large for default buffer size (8 MB)  
	// See https://stackoverflow.com/questions/50348516/boost-beast-message-with-body-limit  
	response.body_limit(std::numeric_limits<std::uint64_t>::max());  
  
	// Receive the HTTP response  
	http::async_read(  
		stream,  
		buffer,  
		response,  
		[self = shared_from_this()]  
		(boost::system::error_code ec, std::size_t bytes_transferred) {  
			self->on_read(ec, bytes_transferred);  
		});  
}  
  
void session::on_read(  
	boost::system::error_code ec,  
	std::size_t /* bytes_transferred */)  
{  
	if (ec)  
		throw boost::system::system_error(ec, "could not read the request");  
  
	std::cout << "got response of size " << response.get().body().size() << "\n";  
  
	// move the data from the response to our results  
	results.resize(results.size() + 1);  
	results.back() = std::move(response.get().body());  
  
	next_request();  
}  
  
void session::on_shutdown(boost::system::error_code ec)  
{  
	if (  
		// Rationale:  
		// http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
		ec == boost::asio::error::eof  
		// Technically, we should not ignore this error but unfortunately some services (including poe.ninja's  
		// Cloudflare backend) shutdowns abruptly as a way to save bandwidth. This very shady optimization is  
		// seen from the client side as an SSL short read which could be a symptom of a man-in-the-middle attack  
		// trying to hijack the TCP connection. In HTTP 1.0, this could be used to exploit some implicit assumptions  
		// when the connection closes. As of HTTP 1.1, there are no known exploits because 1.1 has no on-close assumptions.  
		// See https://github.com/boostorg/beast/issues/38 for more details on the problem.  
		// If you use a different networking library and do not get this error, report this as a security issue anyway.  
		|| ec == boost::asio::ssl::error::stream_truncated)  
	{  
		ec.assign(0, ec.category());  
	}  
  
	if (ec) {  
		throw boost::system::system_error(ec, "could not shutdown the connection");  
	}  
  
	// If we get here then the connection is closed gracefully  
}  
  
} // namespace  
  
constexpr auto host = "poe.ninja";  
  
std::future<result_type>  
async_http_get(  
	boost::asio::io_context& ioc,  
	boost::asio::ssl::context& ctx,  
	const char* host,  
	std::vector<std::string> targets)  
{  
	return std::make_shared<session>(ioc, ctx)->async_http_get(host, std::move(targets));  
}  
  
template <typename F>  
auto async_download(  
	const char* host,  
	std::vector<std::string> targets,  
	F response_handler /* signature: (result_type) -> auto */)  
{  
	return std::async(std::launch::async, [host, targets = std::move(targets), f = std::move(response_handler)]()  
	{  
		boost::asio::io_context ioc;  
		boost::asio::ssl::context ctx{boost::asio::ssl::context::tls_client};  
		// No SSL certificate checking as of now (not so trivial to implement).  
		// This potentially puts us into being able to be fooled while reacing  
		// some other target falsely claiming to be our desired one but since  
		// we do not send any sensitive data we only risk getting bad responses  
		ctx.set_verify_mode(boost::asio::ssl::verify_none);  
		auto request = async_http_get(ioc, ctx, host, std::move(targets));  
		ioc.run();  
  
		return f(request.get());  
	});  
}  
  
std::future<result_type> async_download_item_price_data(std::string league_name)  
{  
	// std::string league_encoded = url_encode(league_name);  
	std::string league_encoded = league_name;  
  
	#define CURRENCY_OVERVIEW_LINK(type) "/api/data/currencyoverview" "?type=" #type "&league="  
	#define ITEM_OVERVIEW_LINK(type)     "/api/data/itemoverview"     "?type=" #type "&league="  
  
	std::vector<std::string> targets = {  
		CURRENCY_OVERVIEW_LINK(Currency)    + league_encoded,  
		CURRENCY_OVERVIEW_LINK(Fragment)    + league_encoded,  
		ITEM_OVERVIEW_LINK(Oil)             + league_encoded,  
		ITEM_OVERVIEW_LINK(Incubator)       + league_encoded,  
		ITEM_OVERVIEW_LINK(Scarab)          + league_encoded,  
		ITEM_OVERVIEW_LINK(Fossil)          + league_encoded,  
		ITEM_OVERVIEW_LINK(Resonator)       + league_encoded,  
		ITEM_OVERVIEW_LINK(Essence)         + league_encoded,  
		ITEM_OVERVIEW_LINK(DivinationCard)  + league_encoded,  
		ITEM_OVERVIEW_LINK(Prophecy)        + league_encoded,  
		ITEM_OVERVIEW_LINK(SkillGem)        + league_encoded,  
		ITEM_OVERVIEW_LINK(BaseType)        + league_encoded,  
		ITEM_OVERVIEW_LINK(HelmetEnchant)   + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueMap)       + league_encoded,  
		ITEM_OVERVIEW_LINK(Map)             + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueJewel)     + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueFlask)     + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueWeapon)    + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueArmour)    + league_encoded,  
		ITEM_OVERVIEW_LINK(UniqueAccessory) + league_encoded,  
		ITEM_OVERVIEW_LINK(Beast)           + league_encoded,  
	};  
  
	#undef CURRENCY_OVERVIEW_LINK  
	#undef ITEM_OVERVIEW_LINK  
  
	auto response_handler = [league = std::move(league_name)](result_type responses) {  
		if (responses.size() != 21u) {  
			throw std::logic_error("logic error: downloaded a different "  
				"number of files from poe.ninja: expected 21 but got " + std::to_string(responses.size()));  
		}  
  
		return responses;  
	};  
  
	return async_download(host, std::move(targets), response_handler);  
}  
  
int main()  
{  
	auto result = async_download_item_price_data("Standard");  
	auto responses = result.get();  
	for (auto const& response : responses) {  
		std::cout << "response size: " << response.size() << "\n";  
	}  
}  
```  
  
The 1st download happens correctly, but all further report a 0-size response. The same problem happens in my project.  
  
output from this source when run:  
  
```  
$ ./beast_http_client_async_ssl.exe  
REQUEST:  
GET /api/data/currencyoverview?type=Currency&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 68521  
REQUEST:  
GET /api/data/currencyoverview?type=Fragment&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Oil&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Incubator&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Scarab&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Fossil&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Resonator&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Essence&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=DivinationCard&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Prophecy&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=SkillGem&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=BaseType&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=HelmetEnchant&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueMap&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Map&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueJewel&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueFlask&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueWeapon&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueArmour&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=UniqueAccessory&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
REQUEST:  
GET /api/data/itemoverview?type=Beast&league=Standard HTTP/1.1  
Host: poe.ninja  
User-Agent: Boost.Beast/266  
  
  
got response of size 0  
terminate called after throwing an instance of 'boost::system::system_error'  
  what():  could not shutdown the connection: application data after close notify  
```  
  
compiler flags: `-std=c++17 -O3 -pedantic -Wall -Wextra -c -fmessage-length=0 -ffast-math -MMD -MP -MF"src/main.d" -MT"src/main.o" -o "src/main.o" "../src/main.cpp"`  
  
linker flags: `-lboost_system-mgw82-mt-x64-1_68 -lssl -lcrypto -lwsock32 -lws2_32`

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-01-28 23:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-579523849  

Nothing is jumping out at me as obviously incorrect

---

## Comment 6

> Username: Xeverous  
> Created at: 2020-01-29 20:24:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-579942743  

Found this while browsing headers.  
  
https://github.com/boostorg/beast/blob/c9d5049307925bccaf2f5e57c127db1a1c78e178/include/boost/beast/http/parser.hpp#L339-L343  
  
The above comment is nowhere in the official documentation. It actually states the opposite: *The [message] container offers value semantics including move and copy if supported by Body and Fields.* ([on this page](https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_http/message_containers.html)). I would expect that the library-provided fields are at least moveable, but in this case it's `response_parser` that has explicitly deleted copy and move operations.  
  
https://github.com/boostorg/beast/blob/c9d5049307925bccaf2f5e57c127db1a1c78e178/include/boost/beast/http/parser.hpp#L84-L91  
  
IMO this is a very unwanted design from user's perspective. The only things I can do with a parser are to construct, use once and destroy it. I need to use the tricks like `std::optional` or placement-new to reuse the object. This is very unintuitive and the documentation does not mention this.  
  
Explicit destruction as if by `obj.~T()` solves the issue but it's quite an ugly workaround. Is there any strong reason why the parser type is non-moveable?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-01-29 21:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-579971531  

> Explicit destruction as if by obj.~T() solves the issue but it's quite an ugly workaround.   
  
Yes this is a design flaw. `obj.~T()` is of course undefined behavior since it leaves behind a "live" object. What I usually do is put the parser in an `optional`.  
  
> Is there any strong reason why the parser type is non-moveable?  
  
Yes, this is a consequence of the design of _Body_. I am aware of these deficiencies and at some point will unveil a fully redesigned HTTP interface.

---

## Comment 8

> Username: Xeverous  
> Created at: 2020-01-29 22:00:27 UTC  
> Updated at: 2020-01-29 22:00:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-579980351  

`optional` does the same thing (`obj.~T()` and placement-new) underneath. Just more safe interface. I mean I did both obviously.  
  
Since I was already surprised with `std::vector<response_type>` errors (type not moveable) I think you should really add it somewhere into documentation. The current limitations of the type and (rather unintuitive) behavior after being used may cause some user trouble and/or more issues like this.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2020-02-28 22:29:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-592758758  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: stale[bot]  
> Created at: 2020-03-06 23:45:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1820#issuecomment-596010955  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
