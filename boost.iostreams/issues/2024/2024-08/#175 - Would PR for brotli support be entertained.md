# #175 - Would PR for brotli support be entertained? [Open]

> Username: justend29  
> Created at: 2024-08-19 20:33:13 UTC  
> Updated at: 2025-07-24 08:43:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/175  

Within the compression features of iostreams, I'm considering adding support for [brotli](https://github.com/google/brotli) compression. It's a very common compression format, particularly on the web. It supports streaming with various window sizes, making it an optimal candidate for iostreams.  
  
Before I invest any time into this support, I'd like to know if the format is of interest, of if there are known characteristics of brotli that have previously excluded it from considering (other than effort to support).

---

## Comment 1

> Username: SSE4  
> Created at: 2025-07-24 08:40:59 UTC  
> Updated at: 2025-07-24 08:43:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/175#issuecomment-3112580694  

I have some patch, mostly working  
  
```  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index 1d8352f..418ef1e 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -41,6 +41,18 @@ boost_iostreams_option(BOOST_IOSTREAMS_ENABLE_LZMA "Boost.Iostreams: Enable LZMA  
 boost_iostreams_option(BOOST_IOSTREAMS_ENABLE_ZSTD "Boost.Iostreams: Enable Zstd support" zstd "1.0" zstd_FOUND zstd::libzstd_shared src/zstd.cpp)  
   
 include(CheckCXXSourceCompiles)  
+include(FindPkgConfig)  
+  
+pkg_check_modules(BrotliDec IMPORTED_TARGET libbrotlidec)  
+pkg_check_modules(BrotliEnc IMPORTED_TARGET libbrotlienc)  
+  
+if (BrotliDec_FOUND AND BrotliEnc_FOUND AND TARGET PkgConfig::BrotliDec AND TARGET PkgConfig::BrotliEnc)  
+  option(BOOST_IOSTREAMS_ENABLE_BROTLI "Boost.Iostreams: Enable Brotli support" ON)  
+  target_sources(boost_iostreams PRIVATE src/brotli.cpp)  
+  target_link_libraries(boost_iostreams PRIVATE PkgConfig::BrotliDec PkgConfig::BrotliEnc)  
+else()  
+  option(BOOST_IOSTREAMS_ENABLE_BROTLI "Boost.Iostreams: Enable Brotli support" OFF)  
+endif()  
   
 function(iostreams_check var source incs libs defs)  
   
@@ -67,7 +79,7 @@ if(BOOST_IOSTREAMS_ENABLE_LZMA)  
   
 endif()  
   
-message(STATUS "Boost.Iostreams: ZLIB ${BOOST_IOSTREAMS_ENABLE_ZLIB}, BZip2 ${BOOST_IOSTREAMS_ENABLE_BZIP2}, LZMA ${BOOST_IOSTREAMS_ENABLE_LZMA}${_lzma_mt}, Zstd ${BOOST_IOSTREAMS_ENABLE_ZSTD}")  
+message(STATUS "Boost.Iostreams: ZLIB ${BOOST_IOSTREAMS_ENABLE_ZLIB}, BZip2 ${BOOST_IOSTREAMS_ENABLE_BZIP2}, LZMA ${BOOST_IOSTREAMS_ENABLE_LZMA}${_lzma_mt}, Zstd ${BOOST_IOSTREAMS_ENABLE_ZSTD}, Brotli ${BOOST_IOSTREAMS_ENABLE_BROTLI}")  
   
 unset(_lzma_mt)  
   
diff --git a/build/Jamfile.v2 b/build/Jamfile.v2  
index 7ea8a96..f85b22e 100644  
--- a/build/Jamfile.v2  
+++ b/build/Jamfile.v2  
@@ -30,6 +30,7 @@ for local v in NO_COMPRESSION  
                NO_BZIP2  
                NO_LZMA  
                NO_ZSTD  
+               NO_BROTLI  
 {  
     $(v) = [ modules.peek : $(v) ] ;  
 }  
@@ -112,6 +113,24 @@ else  
     }  
 }  
   
+if $(NO_COMPRESSION) != 1 && $(NO_BROTLI) != 1  
+{  
+    using brotli ;  
+    brotli-requirements =  
+        [ ac.check-library /brotli//brotlidec : <library>/brotli//brotlidec  
+          <source>brotli.cpp ] ;  
+              brotli-requirements +=  
+        [ ac.check-library /brotli//brotlienc : <library>/brotli//brotlienc  
+          <source>brotli.cpp ] ;  
+}  
+else  
+{  
+    if $(debug)  
+    {  
+        ECHO "notice: iostreams: not using brotli compression " ;  
+    }  
+}  
+  
 local sources = file_descriptor.cpp mapped_file.cpp ;  
   
 lib boost_iostreams   
@@ -122,6 +141,7 @@ lib boost_iostreams  
       $(bzip2-requirements)  
       $(lzma-requirements)  
       $(zstd-requirements)  
+      $(brotli-requirements)  
     :  
     : <link>shared:<define>BOOST_IOSTREAMS_DYN_LINK=1  
     ;  
diff --git a/include/boost/iostreams/filter/brotli.hpp b/include/boost/iostreams/filter/brotli.hpp  
new file mode 100644  
index 0000000..448c2d8  
--- /dev/null  
+++ b/include/boost/iostreams/filter/brotli.hpp  
@@ -0,0 +1,374 @@  
+// (C) Copyright Reimar Döffinger 2024.  
+// Distributed under the Boost Software License, Version 1.0. (See accompanying  
+// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt.)  
+  
+// See http://www.boost.org/libs/iostreams for documentation.  
+  
+#ifndef BOOST_IOSTREAMS_BROTLI_HPP_INCLUDED  
+#define BOOST_IOSTREAMS_BROTLI_HPP_INCLUDED  
+  
+#if defined(_MSC_VER)  
+# pragma once  
+#endif  
+  
+#include <cassert>  
+#include <iosfwd>            // streamsize.  
+#include <memory>            // allocator, bad_alloc.  
+#include <new>  
+#include <boost/config.hpp>  // MSVC, STATIC_CONSTANT, DEDUCED_TYPENAME, DINKUM.  
+#include <boost/detail/workaround.hpp>  
+#include <boost/iostreams/constants.hpp>   // buffer size.  
+#include <boost/iostreams/detail/config/auto_link.hpp>  
+#include <boost/iostreams/detail/config/dyn_link.hpp>  
+#include <boost/iostreams/detail/config/wide_streams.hpp>  
+#include <boost/iostreams/detail/ios.hpp>  // failure, streamsize.  
+#include <boost/iostreams/filter/symmetric.hpp>  
+#include <boost/iostreams/pipeline.hpp>  
+#include <boost/type_traits/is_same.hpp>  
+  
+// Must come last.  
+#ifdef BOOST_MSVC  
+# pragma warning(push)  
+# pragma warning(disable:4251 4231 4660)         // Dependencies not exported.  
+#endif  
+#include <boost/config/abi_prefix.hpp>  
+  
+namespace boost { namespace iostreams {  
+  
+namespace brotli {  
+  
+typedef void* (*alloc_func)(void*, size_t);  
+typedef void (*free_func)(void*, void*);  
+  
+                    // Compression levels  
+  
+BOOST_IOSTREAMS_DECL extern const uint32_t best_speed;  
+BOOST_IOSTREAMS_DECL extern const uint32_t best_compression;  
+BOOST_IOSTREAMS_DECL extern const uint32_t default_compression;  
+  
+                    // Status codes  
+  
+BOOST_IOSTREAMS_DECL extern const int okay;  
+BOOST_IOSTREAMS_DECL extern const int stream_end;  
+  
+                    // Flush codes  
+  
+BOOST_IOSTREAMS_DECL extern const int finish;  
+BOOST_IOSTREAMS_DECL extern const int flush;  
+BOOST_IOSTREAMS_DECL extern const int run;  
+  
+                    // Code for current OS  
+  
+                    // Null pointer constant.  
+  
+const int null                               = 0;  
+  
+                    // Default values  
+  
+} // End namespace brotli.  
+  
+//  
+// Class name: brotli_params.  
+// Description: Encapsulates the parameters passed to brotlidec_init  
+//      to customize compression and decompression.  
+//  
+struct brotli_params {  
+  
+    // Non-explicit constructor.  
+    brotli_params( uint32_t level = brotli::default_compression )  
+        : level(level)  
+        { }  
+    uint32_t level;  
+};  
+  
+//  
+// Class name: brotli_error.  
+// Description: Subclass of std::ios::failure thrown to indicate  
+//     brotli errors other than out-of-memory conditions.  
+//  
+class BOOST_IOSTREAMS_DECL brotli_error : public BOOST_IOSTREAMS_FAILURE {  
+public:  
+    explicit brotli_error(size_t error);  
+    int error() const { return error_; }  
+    static void check BOOST_PREVENT_MACRO_SUBSTITUTION(size_t error);  
+private:  
+    size_t error_;  
+};  
+  
+namespace detail {  
+  
+template<typename Alloc>  
+struct brotli_allocator_traits {  
+#ifndef BOOST_NO_STD_ALLOCATOR  
+#if defined(BOOST_NO_CXX11_ALLOCATOR)  
+    typedef typename Alloc::template rebind<char>::other type;  
+#else  
+    typedef typename std::allocator_traits<Alloc>::template rebind_alloc<char> type;  
+#endif  
+#else  
+    typedef std::allocator<char> type;  
+#endif  
+};  
+  
+template< typename Alloc,  
+          typename Base = // VC6 workaround (C2516)  
+              BOOST_DEDUCED_TYPENAME brotli_allocator_traits<Alloc>::type >  
+struct brotli_allocator : private Base {  
+private:  
+#if defined(BOOST_NO_CXX11_ALLOCATOR) || defined(BOOST_NO_STD_ALLOCATOR)  
+    typedef typename Base::size_type size_type;  
+#else  
+    typedef typename std::allocator_traits<Base>::size_type size_type;  
+#endif  
+public:  
+    BOOST_STATIC_CONSTANT(bool, custom =  
+        (!is_same<std::allocator<char>, Base>::value));  
+    typedef typename brotli_allocator_traits<Alloc>::type allocator_type;  
+    static void* allocate(void* self, size_t size);  
+    static void deallocate(void* self, void* address);  
+};  
+  
+class BOOST_IOSTREAMS_DECL brotli_base {  
+public:  
+    typedef char char_type;  
+protected:  
+    brotli_base();  
+    ~brotli_base();  
+    template<typename Alloc>  
+    void init( const brotli_params& p,  
+               bool compress,  
+               brotli_allocator<Alloc>& zalloc )  
+        {  
+            bool custom = brotli_allocator<Alloc>::custom;  
+            do_init( p, compress,  
+                     custom ? brotli_allocator<Alloc>::allocate : 0,  
+                     custom ? brotli_allocator<Alloc>::deallocate : 0,  
+                     &zalloc );  
+        }  
+    void before( const char*& src_begin, const char* src_end,  
+                 char*& dest_begin, char* dest_end );  
+    void after( const char*& src_begin, char*& dest_begin,  
+                bool compress );  
+    int deflate(int action);  
+    int inflate(int action);  
+    void reset(bool compress, bool realloc);  
+private:  
+    void do_init( const brotli_params& p, bool compress,  
+                  brotli::alloc_func,  
+                  brotli::free_func,  
+                  void* derived );  
+    void init_stream(bool compress,  
+                     brotli::alloc_func,  
+                     brotli::free_func,  
+                     void* derived);  
+    void*           encoder_;         // Actual type: BrotliEncoderState *  
+    void*           decoder_;         // Actual type: BrotliDecoderState *  
+    size_t          avail_in_;  
+    size_t          avail_out_;  
+    const uint8_t * next_in_;  
+    uint8_t *       next_out_;  
+    uint32_t        level_;  
+    bool            eof_;  
+    brotli::alloc_func alloc_;  
+    brotli::free_func free_;  
+    void *          opaque_;  
+};  
+  
+//  
+// Template name: brotli_compressor_impl  
+// Description: Model of C-Style Filter implementing compression by  
+//      delegating to the brotli function deflate.  
+//  
+template<typename Alloc = std::allocator<char> >  
+class brotli_compressor_impl : public brotli_base, public brotli_allocator<Alloc> {  
+public:  
+    brotli_compressor_impl(const brotli_params& = brotli::default_compression);  
+    ~brotli_compressor_impl();  
+    bool filter( const char*& src_begin, const char* src_end,  
+                 char*& dest_begin, char* dest_end, bool flush );  
+    void close();  
+};  
+  
+//  
+// Template name: brotli_decompressor_impl  
+// Description: Model of C-Style Filter implementing decompression by  
+//      delegating to the brotli function inflate.  
+//  
+template<typename Alloc = std::allocator<char> >  
+class brotli_decompressor_impl : public brotli_base, public brotli_allocator<Alloc> {  
+public:  
+    brotli_decompressor_impl(const brotli_params&);  
+    brotli_decompressor_impl();  
+    ~brotli_decompressor_impl();  
+    bool filter( const char*& begin_in, const char* end_in,  
+                 char*& begin_out, char* end_out, bool flush );  
+    void close();  
+private:  
+    bool eof_; // Guard to make sure filter() isn't called after it returns false.  
+};  
+  
+} // End namespace detail.  
+  
+//  
+// Template name: brotli_compressor  
+// Description: Model of InputFilter and OutputFilter implementing  
+//      compression using brotli.  
+//  
+template<typename Alloc = std::allocator<char> >  
+struct basic_brotli_compressor  
+    : symmetric_filter<detail::brotli_compressor_impl<Alloc>, Alloc>  
+{  
+private:  
+    typedef detail::brotli_compressor_impl<Alloc> impl_type;  
+    typedef symmetric_filter<impl_type, Alloc>  base_type;  
+public:  
+    typedef typename base_type::char_type               char_type;  
+    typedef typename base_type::category                category;  
+    basic_brotli_compressor( const brotli_params& = brotli::default_compression,  
+                           std::streamsize buffer_size = default_device_buffer_size );  
+};  
+BOOST_IOSTREAMS_PIPABLE(basic_brotli_compressor, 1)  
+  
+typedef basic_brotli_compressor<> brotli_compressor;  
+  
+//  
+// Template name: brotli_decompressor  
+// Description: Model of InputFilter and OutputFilter implementing  
+//      decompression using brotli.  
+//  
+template<typename Alloc = std::allocator<char> >  
+struct basic_brotli_decompressor  
+    : symmetric_filter<detail::brotli_decompressor_impl<Alloc>, Alloc>  
+{  
+private:  
+    typedef detail::brotli_decompressor_impl<Alloc> impl_type;  
+    typedef symmetric_filter<impl_type, Alloc>    base_type;  
+public:  
+    typedef typename base_type::char_type               char_type;  
+    typedef typename base_type::category                category;  
+    basic_brotli_decompressor( std::streamsize buffer_size = default_device_buffer_size );  
+    basic_brotli_decompressor( const brotli_params& p,  
+                             std::streamsize buffer_size = default_device_buffer_size );  
+};  
+BOOST_IOSTREAMS_PIPABLE(basic_brotli_decompressor, 1)  
+  
+typedef basic_brotli_decompressor<> brotli_decompressor;  
+  
+//----------------------------------------------------------------------------//  
+  
+//------------------Implementation of brotli_allocator--------------------------//  
+  
+namespace detail {  
+  
+template<typename Alloc, typename Base>  
+void* brotli_allocator<Alloc, Base>::allocate  
+    (void* self, size_t size)  
+{  
+    size_type len = size;  
+    char* ptr =  
+        static_cast<allocator_type*>(self)->allocate  
+            (len + sizeof(size_type)  
+            #if BOOST_WORKAROUND(BOOST_DINKUMWARE_STDLIB, == 1)  
+                , (char*)0  
+            #endif  
+            );  
+    *reinterpret_cast<size_type*>(ptr) = len;  
+    return ptr + sizeof(size_type);  
+}  
+  
+template<typename Alloc, typename Base>  
+void brotli_allocator<Alloc, Base>::deallocate(void* self, void* address)  
+{  
+    if (address) {  
+        char *ptr = reinterpret_cast<char *>(address) - sizeof(size_type);  
+        size_type len = *reinterpret_cast<size_type *>(ptr) + sizeof(size_type);  
+        static_cast<allocator_type *>(self)->deallocate(ptr, len);  
+    }  
+}  
+  
+//------------------Implementation of brotli_compressor_impl--------------------//  
+  
+template<typename Alloc>  
+brotli_compressor_impl<Alloc>::brotli_compressor_impl(const brotli_params& p)  
+{ init(p, true, static_cast<brotli_allocator<Alloc>&>(*this)); }  
+  
+template<typename Alloc>  
+brotli_compressor_impl<Alloc>::~brotli_compressor_impl()  
+{ reset(true, false); }  
+  
+template<typename Alloc>  
+bool brotli_compressor_impl<Alloc>::filter  
+    ( const char*& src_begin, const char* src_end,  
+      char*& dest_begin, char* dest_end, bool flush )  
+{  
+    before(src_begin, src_end, dest_begin, dest_end);  
+    int result = deflate(flush ? brotli::finish : brotli::run);  
+    after(src_begin, dest_begin, true);  
+    return result != brotli::stream_end;  
+}  
+  
+template<typename Alloc>  
+void brotli_compressor_impl<Alloc>::close() { reset(true, true); }  
+  
+//------------------Implementation of brotli_decompressor_impl------------------//  
+  
+template<typename Alloc>  
+brotli_decompressor_impl<Alloc>::brotli_decompressor_impl(const brotli_params& p)  
+{ init(p, false, static_cast<brotli_allocator<Alloc>&>(*this)); }  
+  
+template<typename Alloc>  
+brotli_decompressor_impl<Alloc>::~brotli_decompressor_impl()  
+{ reset(false, false); }  
+  
+template<typename Alloc>  
+brotli_decompressor_impl<Alloc>::brotli_decompressor_impl()  
+{  
+    brotli_params p;  
+    init(p, false, static_cast<brotli_allocator<Alloc>&>(*this));  
+}  
+  
+template<typename Alloc>  
+bool brotli_decompressor_impl<Alloc>::filter  
+    ( const char*& src_begin, const char* src_end,  
+      char*& dest_begin, char* dest_end, bool flush )  
+{  
+    before(src_begin, src_end, dest_begin, dest_end);  
+    int result = inflate(flush ? brotli::finish : brotli::run);  
+    after(src_begin, dest_begin, false);  
+    return result != brotli::stream_end;  
+}  
+  
+template<typename Alloc>  
+void brotli_decompressor_impl<Alloc>::close() { reset(false, true); }  
+  
+} // End namespace detail.  
+  
+//------------------Implementation of brotli_compressor-----------------------//  
+  
+template<typename Alloc>  
+basic_brotli_compressor<Alloc>::basic_brotli_compressor  
+    (const brotli_params& p, std::streamsize buffer_size)  
+    : base_type(buffer_size, p) { }  
+  
+//------------------Implementation of brotli_decompressor-----------------------//  
+  
+template<typename Alloc>  
+basic_brotli_decompressor<Alloc>::basic_brotli_decompressor  
+    (std::streamsize buffer_size)  
+    : base_type(buffer_size) { }  
+  
+template<typename Alloc>  
+basic_brotli_decompressor<Alloc>::basic_brotli_decompressor  
+    (const brotli_params& p, std::streamsize buffer_size)  
+    : base_type(buffer_size, p) { }  
+  
+//----------------------------------------------------------------------------//  
+  
+} } // End namespaces iostreams, boost.  
+  
+#include <boost/config/abi_suffix.hpp> // Pops abi_suffix.hpp pragmas.  
+#ifdef BOOST_MSVC  
+# pragma warning(pop)  
+#endif  
+  
+#endif // #ifndef BOOST_IOSTREAMS_BROTLI_HPP_INCLUDED  
diff --git a/src/brotli.cpp b/src/brotli.cpp  
new file mode 100644  
index 0000000..1049af9  
--- /dev/null  
+++ b/src/brotli.cpp  
@@ -0,0 +1,200 @@  
+// (C) Copyright Reimar Döffinger 2024.  
+// Distributed under the Boost Software License, Version 1.0. (See accompanying  
+// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt.)  
+  
+// See http://www.boost.org/libs/iostreams for documentation.  
+  
+// Define BOOST_IOSTREAMS_SOURCE so that <boost/iostreams/detail/config.hpp>  
+// knows that we are building the library (possibly exporting code), rather  
+// than using it (possibly importing code).  
+#define BOOST_IOSTREAMS_SOURCE  
+  
+#include <brotli/decode.h>  
+#include <brotli/encode.h>  
+  
+#include <boost/throw_exception.hpp>  
+#include <boost/iostreams/detail/config/dyn_link.hpp>  
+#include <boost/iostreams/filter/brotli.hpp>  
+  
+namespace boost { namespace iostreams {  
+  
+namespace brotli {  
+                    // Compression levels  
+  
+const uint32_t best_speed           = BROTLI_MIN_QUALITY;  
+const uint32_t best_compression     = BROTLI_MAX_QUALITY;  
+const uint32_t default_compression  = BROTLI_DEFAULT_QUALITY;  
+  
+                    // Status codes  
+  
+const int okay                 = 0;  
+const int stream_end           = 1;  
+  
+                    // Flush codes  
+  
+const int finish               = BROTLI_OPERATION_FINISH;  
+const int flush                = BROTLI_OPERATION_FLUSH;  
+const int run                  = BROTLI_OPERATION_PROCESS;  
+} // End namespace brotli.  
+  
+//------------------Implementation of brotli_error------------------------------//  
+  
+brotli_error::brotli_error(size_t error)  
+    : BOOST_IOSTREAMS_FAILURE(BrotliDecoderErrorString(static_cast<BrotliDecoderErrorCode>(error))), error_(error)  
+    { }  
+  
+void brotli_error::check BOOST_PREVENT_MACRO_SUBSTITUTION(size_t error)  
+{  
+    switch (static_cast<BrotliDecoderErrorCode>(error)) {  
+        case BROTLI_DECODER_ERROR_ALLOC_CONTEXT_MODES:  
+        case BROTLI_DECODER_ERROR_ALLOC_TREE_GROUPS:  
+        case BROTLI_DECODER_ERROR_ALLOC_CONTEXT_MAP:  
+        case BROTLI_DECODER_ERROR_ALLOC_RING_BUFFER_1:  
+        case BROTLI_DECODER_ERROR_ALLOC_RING_BUFFER_2:  
+        case BROTLI_DECODER_ERROR_ALLOC_BLOCK_TYPE_TREES:  
+            boost::throw_exception(std::bad_alloc());  
+        default:  
+            boost::throw_exception(brotli_error(error));  
+    }  
+}  
+  
+//------------------Implementation of brotli_base-------------------------------//  
+  
+namespace detail {  
+  
+brotli_base::brotli_base()  
+    : encoder_(nullptr), decoder_(nullptr), next_in_(nullptr), next_out_(nullptr), avail_in_(0), avail_out_(0), level_(brotli::default_compression), eof_(false), alloc_(  
+        nullptr), free_(nullptr), opaque_(nullptr)  
+    {  
+    }  
+  
+brotli_base::~brotli_base()  
+{  
+}  
+  
+void brotli_base::before( const char*& src_begin, const char* src_end,  
+                        char*& dest_begin, char* dest_end )  
+{  
+    next_in_ = reinterpret_cast<const uint8_t*>(src_begin);  
+    avail_in_ = static_cast<size_t>(src_end - src_begin);  
+    next_out_ = reinterpret_cast<uint8_t*>(dest_begin);  
+    avail_out_ = static_cast<size_t>(dest_end - dest_begin);  
+}  
+  
+void brotli_base::after(const char*& src_begin, char*& dest_begin, bool)  
+{  
+    src_begin = reinterpret_cast<const char*>(next_in_);  
+    dest_begin = reinterpret_cast<char*>(next_out_);  
+}  
+  
+int brotli_base::deflate(int action)  
+{  
+    do {  
+  
+    if (!BrotliEncoderCompressStream(static_cast<BrotliEncoderState*>(encoder_),  
+                                     static_cast<BrotliEncoderOperation>(action),  
+                                     &avail_in_,  
+                                     &next_in_,  
+                                     &avail_out_,  
+                                     &next_out_,  
+                                     nullptr))  
+        boost::throw_exception(std::bad_alloc());  
+  
+    } while (action == brotli::finish && (avail_in_ || BrotliEncoderHasMoreOutput(static_cast<BrotliEncoderState*>(encoder_))));  
+  
+    return BrotliEncoderIsFinished(static_cast<BrotliEncoderState*>(encoder_)) ? brotli::stream_end : brotli::okay;  
+}  
+  
+int brotli_base::inflate(int action)  
+{  
+    // Ignore spurious extra calls.  
+    // Note size > 0 will trigger an error in this case.  
+    if (eof_) {  
+        if (avail_in_ == 0) {  
+            return brotli::stream_end;  
+        } else {  
+            reset(false, true);  
+        }  
+    }  
+  
+    do {  
+  
+        BrotliDecoderResult result = BrotliDecoderDecompressStream(static_cast<BrotliDecoderState *>(decoder_),  
+                                                                   &avail_in_,  
+                                                                   &next_in_,  
+                                                                   &avail_out_,  
+                                                                   &next_out_,  
+                                                                   nullptr);  
+  
+        switch (result) {  
+            case BROTLI_DECODER_RESULT_ERROR:  
+                brotli_error::check BOOST_PREVENT_MACRO_SUBSTITUTION(  
+                        BrotliDecoderGetErrorCode(static_cast<BrotliDecoderState *>(decoder_)));  
+                break;  
+            case BROTLI_DECODER_RESULT_NEEDS_MORE_INPUT:  
+            case BROTLI_DECODER_RESULT_NEEDS_MORE_OUTPUT:  
+            case BROTLI_DECODER_RESULT_SUCCESS:  
+                break;  
+        }  
+    }  
+    while (BrotliDecoderHasMoreOutput(static_cast<BrotliDecoderState *>(decoder_)) && avail_out_);  
+  
+    if (action == brotli::finish) {  
+  
+        return brotli::stream_end;  
+    }  
+  
+    eof_ = BrotliDecoderIsFinished(static_cast<BrotliDecoderState *>(decoder_));  
+  
+    return eof_ ? brotli::stream_end : brotli::okay;  
+  
+}  
+  
+void brotli_base::reset(bool compress, bool realloc)  
+{  
+    compress ?  
+    BrotliEncoderDestroyInstance(static_cast<BrotliEncoderState *>(encoder_)) :  
+    BrotliDecoderDestroyInstance(static_cast<BrotliDecoderState*>(decoder_));  
+  
+    if (realloc) {  
+        init_stream(compress, alloc_, free_, opaque_);  
+    }  
+}  
+  
+void brotli_base::do_init  
+    ( const brotli_params& p, bool compress,  
+      brotli::alloc_func alloc, brotli::free_func free,  
+      void* opaque)  
+{  
+    level_ = p.level;  
+    init_stream(compress, alloc, free, opaque);  
+}  
+  
+void brotli_base::init_stream(bool compress,  
+                              brotli::alloc_func alloc, brotli::free_func free,  
+                              void* opaque)  
+{  
+    alloc_ = alloc;  
+    free_ = free;  
+    opaque_ = opaque;  
+    if (compress) {  
+        encoder_ = BrotliEncoderCreateInstance(alloc_, free_, opaque_);  
+        if (!encoder_)  
+            boost::throw_exception(std::bad_alloc());  
+  
+        if (!BrotliEncoderSetParameter(static_cast<BrotliEncoderState *>(encoder_), BROTLI_PARAM_QUALITY, level_))  
+            boost::throw_exception(std::bad_alloc());  
+    } else {  
+        decoder_ = BrotliDecoderCreateInstance(alloc_, free_, opaque_);  
+        if (!decoder_)  
+            boost::throw_exception(std::bad_alloc());  
+    }  
+    eof_ = false;  
+}  
+  
+  
+} // End namespace detail.  
+  
+//----------------------------------------------------------------------------//  
+  
+} } // End namespaces iostreams, boost.  
diff --git a/test/CMakeLists.txt b/test/CMakeLists.txt  
new file mode 100644  
index 0000000..1d071df  
--- /dev/null  
+++ b/test/CMakeLists.txt  
@@ -0,0 +1,3 @@  
+add_executable(brotli_test brotli_test.cpp)  
+  
+target_link_libraries(brotli_test PRIVATE Boost::iostreams Boost::unit_test_framework)  
\ No newline at end of file  
diff --git a/test/Jamfile.v2 b/test/Jamfile.v2  
index e13ff8f..7f3ab0a 100644  
--- a/test/Jamfile.v2  
+++ b/test/Jamfile.v2  
@@ -15,6 +15,7 @@ local NO_BZIP2 = [ modules.peek : NO_BZIP2 ] ;  
 local NO_ZLIB = [ modules.peek : NO_ZLIB ] ;  
 local NO_LZMA = [ modules.peek : NO_LZMA ] ;  
 local NO_ZSTD = [ modules.peek : NO_ZSTD ] ;  
+local NO_BROTLI = [ modules.peek : NO_BROTLI ] ;  
 local LARGE_FILE_TEMP = [ modules.peek : LARGE_FILE_TEMP ] ;  
 local LARGE_FILE_KEEP = [ modules.peek : LARGE_FILE_KEEP ] ;  
   
@@ -170,6 +171,14 @@ rule compile-fail-iostreams ( sources * : requirements * : target-name ? ) {  
                              zstd_test.cpp ../build//boost_iostreams :  
                              [ ac.check-library /zstd//zstd : : <build>no ] ] ;  
       }  
+      if ! $(NO_BROTLI)  
+      {  
+          using brotli ;  
+          all-tests += [ test-iostreams  
+                             brotli_test.cpp ../build//boost_iostreams :  
+                             [ ac.check-library /brotli//brotlidec : : <build>no ]  
+                              [ ac.check-library /brotli//brotlienc : : <build>no ] ] ;  
+      }  
             
     test-suite "iostreams" : $(all-tests) ;  
       
diff --git a/test/brotli_test.cpp b/test/brotli_test.cpp  
new file mode 100644  
index 0000000..75a2402  
--- /dev/null  
+++ b/test/brotli_test.cpp  
@@ -0,0 +1,188 @@  
+// (C) COPYRIGHT 2024 Reimar Döffinger  
+// Distributed under the Boost Software License, Version 1.0. (See accompanying  
+// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt.)  
+  
+// See http://www.boost.org/libs/iostreams for documentation.  
+  
+// Note: basically a copy-paste of the gzip test  
+  
+#include <cstddef>  
+#include <string>  
+#include <boost/iostreams/copy.hpp>  
+#include <boost/iostreams/device/array.hpp>  
+#include <boost/iostreams/device/back_inserter.hpp>  
+#include <boost/iostreams/filter/brotli.hpp>  
+#include <boost/iostreams/filter/test.hpp>  
+#include <boost/iostreams/filtering_stream.hpp>  
+#include <boost/ref.hpp>  
+#include <boost/range/iterator_range.hpp>  
+#include <boost/test/test_tools.hpp>  
+#include <boost/test/unit_test.hpp>  
+#include "detail/sequence.hpp"  
+#include "detail/verification.hpp"  
+  
+using namespace boost;  
+using namespace boost::iostreams;  
+using namespace boost::iostreams::test;  
+namespace io = boost::iostreams;  
+using boost::unit_test::test_suite;  
+  
+template<class T> struct basic_test_alloc: std::allocator<T>  
+{  
+    basic_test_alloc()  
+    {  
+    }  
+  
+    basic_test_alloc( basic_test_alloc const& /*other*/ )  
+    {  
+    }  
+  
+    template<class U>  
+    basic_test_alloc( basic_test_alloc<U> const & /*other*/ )  
+    {  
+    }  
+  
+    template<class U> struct rebind  
+    {  
+        typedef basic_test_alloc<U> other;  
+    };  
+};  
+  
+typedef basic_test_alloc<char> brotli_allic;  
+  
+void compression_test()  
+{  
+    text_sequence      data;  
+  
+    // Test compression and decompression with custom allocator  
+    BOOST_CHECK(  
+        test_filter_pair( basic_brotli_compressor<brotli_allic>(),  
+                          basic_brotli_decompressor<brotli_allic>(),  
+                          std::string(data.begin(), data.end()) )  
+    );  
+}  
+  
+void multiple_member_test()  
+{  
+    text_sequence      data;  
+    std::vector<char>  temp, dest;  
+  
+    // Write compressed data to temp, twice in succession  
+    filtering_ostream out;  
+    out.push(brotli_compressor());  
+    out.push(io::back_inserter(temp));  
+    io::copy(make_iterator_range(data), out);  
+    out.push(io::back_inserter(temp));  
+    io::copy(make_iterator_range(data), out);  
+    BOOST_CHECK(std::equal(temp.begin(), temp.begin() + temp.size()/2, temp.begin() + temp.size()/2));  
+  
+    // Read compressed data from temp into dest  
+    filtering_istream in;  
+    in.push(brotli_decompressor());  
+    in.push(array_source(&temp[0], temp.size()));  
+    io::copy(in, io::back_inserter(dest));  
+  
+    // Check that dest consists of two copies of data  
+    BOOST_REQUIRE_EQUAL(data.size() * 2, dest.size());  
+    BOOST_CHECK(std::equal(data.begin(), data.end(), dest.begin()));  
+    BOOST_CHECK(std::equal(data.begin(), data.end(), dest.begin() + dest.size() / 2));  
+  
+    dest.clear();  
+    io::copy(  
+        array_source(&temp[0], temp.size()),  
+        io::compose(brotli_decompressor(), io::back_inserter(dest)));  
+  
+    // Check that dest consists of two copies of data  
+    BOOST_REQUIRE_EQUAL(data.size() * 2, dest.size());  
+    BOOST_CHECK(std::equal(data.begin(), data.end(), dest.begin()));  
+    BOOST_CHECK(std::equal(data.begin(), data.end(), dest.begin() + dest.size() / 2));  
+}  
+  
+void array_source_test()  
+{  
+    std::string data = "simple test string.";  
+    std::string encoded;  
+  
+    filtering_ostream out;  
+    out.push(brotli_compressor());  
+    out.push(io::back_inserter(encoded));  
+    io::copy(make_iterator_range(data), out);  
+  
+    std::string res;  
+    io::array_source src(encoded.data(),encoded.length());  
+    io::copy(io::compose(io::brotli_decompressor(), src), io::back_inserter(res));  
+  
+    BOOST_CHECK_EQUAL(data, res);  
+}  
+  
+void empty_file_test()  
+{  
+    // This test is in response to https://svn.boost.org/trac/boost/ticket/5237  
+    // The previous implementation of gzip_compressor only wrote the gzip file  
+    // header when the first bytes of uncompressed input were processed, causing  
+    // incorrect behavior for empty files  
+    BOOST_CHECK(  
+        test_filter_pair( brotli_compressor(),  
+                          brotli_decompressor(),  
+                          std::string() )  
+    );  
+}  
+  
+void multipart_test()  
+{  
+    // This test verifies that the brotli_decompressor properly handles a file  
+    // that consists of multiple concatenated files (matches unbrotli behaviour)  
+    static const char multipart_file[] = {  
+        '\x1b', '\x14', '\x00', '\x00', '\xc4', '\x0d', '\xce', '\x13', '\x0a', '\xb4', '\x12', '\x4d',  
+        '\x34', '\x58', '\x4e', '\x06', '\x24', '\x99', '\x25', '\x92', '\xf7', '\x7e', '\x14', '\xb8',  
+        '\x00', '\x1b', '\x14', '\x00', '\x00', '\xc4', '\x0d', '\xce', '\x13', '\x4a', '\xa0', '\x12',  
+        '\x4a', '\x34', '\x58', '\x4e', '\x06', '\x24', '\x99', '\x25', '\x92', '\xf7', '\x7e', '\x14',  
+        '\xb8', '\x00', '\x1b', '\x14', '\x00', '\x00', '\xc4', '\x0d', '\xce', '\x13', '\x4a', '\xac',  
+        '\x02', '\x4f', '\x34', '\x58', '\x4e', '\x06', '\x24', '\x99', '\x25', '\x92', '\xf7', '\x7e',  
+        '\x14', '\xb8', '\x00'  
+    };  
+  
+    filtering_istream in;  
+    std::string line;  
+  
+    in.push(brotli_decompressor());  
+    in.push(io::array_source(multipart_file, sizeof(multipart_file)));  
+  
+    // First part  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 1", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 2", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 3", line);  
+  
+    // Second part immediately follows  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 4", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 5", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 6", line);  
+  
+    // Then an empty part, followed by one last 3-line part.  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 7", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 8", line);  
+    std::getline(in, line);  
+    BOOST_CHECK_EQUAL("Line 9", line);  
+  
+    // Check for brotli errors too.  
+    BOOST_CHECK(!in.bad());  
+}  
+  
+test_suite* init_unit_test_suite(int, char* [])  
+{  
+    test_suite* test = BOOST_TEST_SUITE("brotli test");  
+    test->add(BOOST_TEST_CASE(&compression_test));  
+    test->add(BOOST_TEST_CASE(&multiple_member_test));  
+    test->add(BOOST_TEST_CASE(&array_source_test));  
+    test->add(BOOST_TEST_CASE(&empty_file_test));  
+    test->add(BOOST_TEST_CASE(&multipart_test));  
+    return test;  
+}  
  
```
