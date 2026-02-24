# #182 - cpp_re.hpp has a memmove that passes 0/null as arguments => Sanitizer complains. [Closed]

> Username: realazthat  
> Created at: 2022-11-17 18:13:20 UTC  
> Updated at: 2023-03-29 23:35:51 UTC  
> Closed at: 2023-03-29 23:35:50 UTC  
> Url: https://github.com/boostorg/wave/issues/182  

### Happens here: https://github.com/boostorg/wave/blob/99ccc6e3cbd3fa347dc23f10518aa7d33d31573e/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L205  
  
My guess is that sometimes memmove is a noop (length is zero) but nulls are passed in, and sanitizer doesn't like this. If so the fix would be to wrap this in a condition.  
  
### Address sanitizer complains:  
  
![image](https://user-images.githubusercontent.com/141522/202524644-4b1f62bf-ea04-4488-a957-05ca879ae0a5.png)  
  
### Values shown in debugger:  
  
![image](https://user-images.githubusercontent.com/141522/202524737-cd6a45b5-1b3d-4ad4-8299-660513243149.png)

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-11-17 19:35:30 UTC  
> Url: https://github.com/boostorg/wave/issues/182#issuecomment-1319109262  

This is UB and definitely requires fixing. I probably also need to make a testcase - is there anything special about the situation? If you have a small repro I'd love to have it.

---

## Comment 2

> Username: realazthat  
> Created at: 2022-11-18 11:23:17 UTC  
> Url: https://github.com/boostorg/wave/issues/182#issuecomment-1319873441  

## Minrep:  
  
```c++  
  
#include <filesystem>  
#include <iostream>  
#include <set>  
#include <sstream>  
#include <string>  
#include <string_view>  
#include <map>  
  
  
#include "boost/wave.hpp"  
#include "boost/wave/cpp_context.hpp"  
#include "boost/wave/cpp_exceptions.hpp"  
#include "boost/wave/cpplexer/cpp_lex_iterator.hpp"  
#include "boost/wave/language_support.hpp"  
  
  
  
template <typename Token>  
struct ContextPolicy_  
    : boost::wave::context_policies::default_preprocessing_hooks {  
  typedef boost::wave::context_policies::default_preprocessing_hooks base_type;  
  
  template <typename ContextT, typename TokenT>  
  bool found_directive(ContextT const& ctx, TokenT const& directive) {  
    return this->base_type::found_directive(ctx, directive);  
  }  
  
  template <typename ContextT, typename ContainerT>  
  bool found_unknown_directive(ContextT const& ctx, ContainerT const& line,  
                               ContainerT& pending) {  
    for (const auto& token : line) {  
      if (token.is_valid()) {  
      }  
    }  
    auto it = line.begin();  
    assert(it != line.end());  
    ++it;  
    assert(it != line.end());  
    auto directive_flex_str = it->get_value();  
    std::string_view directive(/*s=*/directive_flex_str.data(),  
                               directive_flex_str.size());  
  
    std::set<std::string_view> glsl_directives = {"version", "extension"};  
    if (glsl_directives.contains(directive)) {  
      pending = line;  
      return true;  
    }  
    return this->base_type::found_unknown_directive(ctx, line, pending);  
  }  
  
  template <typename ContextT, typename TokenT>  
  bool may_skip_whitespace(ContextT const& ctx, TokenT& token,  
                           bool& skipped_newline) {  
    skipped_newline = false;  
    return false;  
  }  
  
  template <typename ContextT, typename TokenT>  
  TokenT const& generated_token(ContextT const& ctx, TokenT const& token) {  
    return this->base_type::generated_token(ctx, token);  
  }  
};  
  
int main() {  
  
  
  using lex_iterator_type =  
      boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<>>;  
  using context_type = boost::wave::context<  
      std::string::const_iterator, lex_iterator_type,  
      boost::wave::iteration_context_policies::load_file_to_string,  
      ContextPolicy_<typename lex_iterator_type::token_type>>;  
  try {  
    const std::string shader_source = R"str()str";  
    std::filesystem::path shader_path = "shader.glsl";  
    std::vector<std::filesystem::path> local_dirs = {shader_path.parent_path()};  
    std::vector<std::filesystem::path> system_dirs = {};  
    std::map<std::string, std::string> defines = {};  
  
  
  
  
  
  
  
  
  
    std::string shader_source_ = shader_source;  
    if (!shader_source_.ends_with("\n")) {  
      shader_source_.push_back('\n');  
    }  
  
    context_type ctx(/*first_=*/shader_source_.begin(),  
                     /*last_=*/shader_source_.end(),  
                     /*fname=*/shader_path.string().c_str());  
    ctx.set_language(/*language=*/boost::wave::language_support::support_c99);  
    ctx.set_language(  
        /*language=*/boost::wave::enable_preserve_comments(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_variadics(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_long_long(ctx.get_language()));  
    // ctx.set_language(/*language=*/boost::wave::enable_va_opt(ctx.get_language()));  
    // ctx.set_language(/*language=*/boost::wave::enable_no_newline_at_end_of_file(ctx.get_language()));  
    // ctx.set_language(/*language=*/boost::wave::enable_has_include(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_insert_whitespace(ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_single_line(ctx.get_language()));  
    ctx.set_language(/*language=*/boost::wave::enable_emit_line_directives(  
        ctx.get_language()));  
    ctx.set_language(/*language=*/boost::wave::enable_emit_pragma_directives(  
        ctx.get_language()));  
    ctx.set_language(/*language=*/boost::wave::enable_include_guard_detection(  
        ctx.get_language()));  
    ctx.set_language(  
        /*language=*/boost::wave::enable_prefer_pp_numbers(ctx.get_language()));  
    for (const auto& local_dir : local_dirs) {  
      ctx.add_include_path(local_dir.string().c_str());  
    }  
  
    for (const auto& system_dir : system_dirs) {  
      ctx.add_sysinclude_path(system_dir.string().c_str());  
    }  
    for (const auto& [key, value] : defines) {  
      ctx.add_macro_definition(  
          /*macrostring=*/key + "=" + value);  
    }  
  
    assert(!ctx.is_defined_macro(std::string("__cplusplus")));  
  
    context_type::iterator_type first = ctx.begin();  
    context_type::iterator_type last = ctx.end();  
  
  
    std::ostringstream sstr;  
    while (first != last) {  
      sstr << (*first).get_value();  
      ++first;  
    }  
  
    std::string preprocessedGLSL = sstr.str();  
      
  } catch (const boost::wave::cpp_exception& e) {  
    std::string severity = boost::wave::util::get_severity(e.get_severity());  
  
    std::cerr  
           << "Error during preprocessing"  
           << "\n e.what(): " << e.what()  
           << "\n e.description(): " << e.description()  
           << "\n e.get_errorcode(): " << e.get_errorcode()  
           << "\n e.get_severity(): " << e.get_severity()  
           << "\n e.get_severity(): " << severity  
           << "\n e.is_recoverable(): " << e.is_recoverable()  
           << "\n e.file_name(): " << e.file_name()  
           << "\n e.file_name(): " << e.file_name() << ":" << e.line_no()  
           << "\n e.line_no(): " << e.line_no()  
           << "\n e.column_no(): " << e.column_no()  
           << "\n";  
    return 1;  
  } catch (const boost::wave::cpplexer::cpplexer_exception& e) {  
    std::string severity = boost::wave::util::get_severity(e.get_severity());  
  
    std::cerr  
           << "Error during preprocessing"  
           << "\n e.what(): " << e.what()  
           << "\n e.description(): " << e.description()  
           << "\n e.get_errorcode(): " << e.get_errorcode()  
           << "\n e.get_severity(): " << e.get_severity()  
           << "\n e.get_severity(): " << severity  
           << "\n e.is_recoverable(): " << e.is_recoverable()  
           << "\n e.file_name(): " << e.file_name()  
           << "\n e.file_name(): " << e.file_name() << ":" << e.line_no()  
           << "\n e.line_no(): " << e.line_no()  
           << "\n e.column_no(): " << e.column_no()  
           << "\n";  
    return 1;  
  }  
  return 0;  
}  
  
```  
  
The command is a bit insane, you should be able to pare it down easily, it is difficult for me to pare it down based on how I have installed boost, I need to run it via bazel. Apologies for the mess. I assume mostly the sanitizers are important.  
  
```bash  
  
  /usr/bin/gcc-11 -U_FORTIFY_SOURCE -fstack-protector -Wall -Wunused-but-set-parameter -Wno-free-nonheap-object -fno-omit-frame-pointer -g '-std=c++0x' -MD -MF bazel-out/k8-dbg/bin/minreps/_objs/wave_182/wave_182.pic.d '-frandom-seed=bazel-out/k8-dbg/bin/minreps/_objs/wave_182/wave_182.pic.o' -fPIC -DBOOST_NO_CXX98_FUNCTION_BASE -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_HAS_POSIX_AT_APIS -iquote . -iquote bazel-out/k8-dbg/bin -iquote external/boost -iquote bazel-out/k8-dbg/bin/external/boost -iquote external/com_github_facebook_zstd -iquote bazel-out/k8-dbg/bin/external/com_github_facebook_zstd -iquote external/net_zlib_zlib -iquote bazel-out/k8-dbg/bin/external/net_zlib_zlib -iquote external/org_bzip_bzip2 -iquote bazel-out/k8-dbg/bin/external/org_bzip_bzip2 -iquote external/org_lzma_lzma -iquote bazel-out/k8-dbg/bin/external/org_lzma_lzma -iquote external/bazel_tools -iquote bazel-out/k8-dbg/bin/external/bazel_tools -isystem external/boost -isystem bazel-out/k8-dbg/bin/external/boost -isystem external/boost/libs/math/include_private -isystem bazel-out/k8-dbg/bin/external/boost/libs/math/include_private -isystem external/com_github_facebook_zstd/lib -isystem bazel-out/k8-dbg/bin/external/com_github_facebook_zstd/lib -isystem external/net_zlib_zlib -isystem bazel-out/k8-dbg/bin/external/net_zlib_zlib -isystem external/org_bzip_bzip2 -isystem bazel-out/k8-dbg/bin/external/org_bzip_bzip2 -isystem external/org_lzma_lzma/src/liblzma/api -isystem bazel-out/k8-dbg/bin/external/org_lzma_lzma/src/liblzma/api -D_GLFW_OSMESA -g3 -O0 -ggdb3 -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG '-fsanitize=address,undefined' -fno-omit-frame-pointer '-fsanitize=float-divide-by-zero' '-fsanitize=float-cast-overflow' '-std=c++20' -Werror -Wall -Wno-deprecated-volatile -fexceptions '-fsanitize=address,undefined' -fno-omit-frame-pointer -fno-canonical-system-headers -Wno-builtin-macro-redefined '-D__DATE__="redacted"' '-D__TIMESTAMP__="redacted"' '-D__TIME__="redacted"' -c minreps/wave_182.cxx -o bazel-out/k8-dbg/bin/minreps/_objs/wave_182/wave_182.pic.o)  
  ```

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-11-18 18:50:31 UTC  
> Url: https://github.com/boostorg/wave/issues/182#issuecomment-1320406699  

That's fantastic, actually! Thank you.

---

## Comment 4

> Username: jefftrull  
> Created at: 2023-03-29 23:35:47 UTC  
> Url: https://github.com/boostorg/wave/issues/182#issuecomment-1489473659  

This will be fixed in 1.82, to be released soon.
