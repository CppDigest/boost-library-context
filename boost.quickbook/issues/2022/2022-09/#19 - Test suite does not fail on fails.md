# #19 - Test suite does not fail on fails? [Closed]

> Username: Kojoley  
> Created at: 2022-09-24 13:42:11 UTC  
> Updated at: 2022-09-24 20:15:21 UTC  
> Closed at: 2022-09-24 20:15:21 UTC  
> Url: https://github.com/boostorg/quickbook/issues/19  

I see the same UBSAN error multiple times running the tests, but the result reports that everything is fine:  
```  
./b2 optimization=speed inlining=full cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=address,undefined cxxflags=-fsanitize-address-use-after-scope linkflags=-fsanitize=address,undefined tools/quickbook/test toolset=clang  
<...>  
clang-linux.link bin.v2/tools/quickbook/src/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/quickbook  
quickbook-testing.process-quickbook bin.v2/tools/quickbook/test/anchor-1_1.test/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/anchor-1_1  
Generating output file: bin.v2/tools/quickbook/test/anchor-1_1.test/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/anchor-1_1  
tools/quickbook/test/anchor-1_1.quickbook:3: warning: Quickbook version undefined. Version 1.1 is assumed  
/mnt/g/boost-repo/tools/quickbook/src/main_grammar.cpp:295:28: runtime error: load of value 190, which is not a valid value for type 'bool'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /mnt/g/boost-repo/tools/quickbook/src/main_grammar.cpp:295:28 in  
testing.capture-output bin.v2/tools/quickbook/test/anchor-1_1.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/anchor-1_1.run  
**passed** bin.v2/tools/quickbook/test/anchor-1_1.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/anchor-1_1.test  
<...>  
clang-linux.compile.c++ bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/text_diff.o  
quickbook-testing.process-quickbook-html bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2  
Generating output file: bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2  
/mnt/g/boost-repo/tools/quickbook/src/main_grammar.cpp:295:28: runtime error: load of value 190, which is not a valid value for type 'bool'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /mnt/g/boost-repo/tools/quickbook/src/main_grammar.cpp:295:28 in  
Unsupported tag: article  
Unsupported tag: xi:include  
Unsupported tag: xi:include  
Unsupported tag: xi:include  
Unsupported tag: xi:include  
clang-linux.link bin.v2/tools/quickbook/test/xinclude/xmlbase1-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase1-1_6-alt2-compare-html  
testing.capture-output bin.v2/tools/quickbook/test/xinclude/xmlbase1-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase1-1_6-alt2-compare-html.run  
**passed** bin.v2/tools/quickbook/test/xinclude/xmlbase1-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase1-1_6-alt2-compare-html.test  
clang-linux.link bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2  
clang-linux.compile.c++ bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/text_diff.o  
testing.capture-output bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2.run  
**passed** bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test2/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2.test  
testing.capture-output bin.v2/tools/quickbook/test/xinclude/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase-1_6-fail.run  
**passed** bin.v2/tools/quickbook/test/xinclude/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase-1_6-fail.test  
clang-linux.link bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2-compare-html  
testing.capture-output bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2-compare-html.run  
**passed** bin.v2/tools/quickbook/test/xinclude/xmlbase2-1_6-alt2.test-html/clang-linux-14/debug/cxxstd-0x-iso/inlining-full/link-static/optimization-speed/xmlbase2-1_6-alt2-compare-html.test  
...updated 4755 targets...  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-24 20:15:21 UTC  
> Url: https://github.com/boostorg/quickbook/issues/19#issuecomment-1257057780  

My bad, it needs`-fno-sanitize-recover=all` flag to force executable exit with a non-zero error code.
