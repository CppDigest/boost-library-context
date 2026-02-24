# #839 - craypp crash compiling segments_view.cpp [Closed]

> Username: vinniefalco  
> Created at: 2024-04-16 20:28:57 UTC  
> Updated at: 2024-05-15 07:35:11 UTC  
> Closed at: 2024-04-16 22:23:22 UTC  
> Url: https://github.com/boostorg/url/issues/839  

rc3 triggers a compiler crash with crayCC 17.0.0  
13:40:37 [alainm@login2 rel]#crayCC --version  
Cray clang version 17.0.0  (b59b7a8e9169719529cf5ab440f3c301e515d047)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /opt/cray/pe/cce/17.0.0/cce-clang/x86_64/share/../bin  
13:40:45 [alainm@login2 rel]#  
  
when compiling libs/url/src/segments_view.cpp  
  
compiler crash stack:  
  
```  
1.    /lus/work/CT4/cin7233/SHARED/boost_build/boost/libs/url/src/segments_view.cpp:34:1: current parser token '{'  
2.    /lus/work/CT4/cin7233/SHARED/boost_build/boost/libs/url/src/segments_view.cpp:18:1: parsing namespace 'boost'  
3.    /lus/work/CT4/cin7233/SHARED/boost_build/boost/libs/url/src/segments_view.cpp:19:1: parsing namespace 'boost::urls'  
 #0 0x0000000003b7e487 llvm::sys::PrintStackTrace(llvm::raw_ostream&, int) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x3b7e487)  
 #1 0x0000000003b7b82b SignalHandler(int) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x3b7b82b)  
#0 0x0000000003b7e487 llvm::sys::PrintStackTrace(llvm::raw_ostream&, int) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x3b7e487)  
 #1 0x0000000003b7b82b SignalHandler(int) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x3b7b82b)  
 #2 0x00007f475636acef (/lib64/libpthread.so.0+0x12cef)  
 #3 0x00007f475508eace raise (/lib64/libc.so.6+0x4eace)  
 #4 0x00007f4755061ea4 abort (/lib64/libc.so.6+0x21ea4)  
 #5 0x00007f4755061d78 __assert_fail_base.cold.0 (/lib64/libc.so.6+0x21d78)  
 #6 0x00007f4755087425 __assert_fail (/lib64/libc.so.6+0x47425)  
 #7 0x00000000062e0306 clang::Sema::BuildDelegatingInitializer(clang::TypeSourceInfo*, clang::Expr*, clang::CXXRecordDecl*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x62e0306)  
 #8 0x00000000062e581e clang::Sema::BuildBaseInitializer(clang::QualType, clang::TypeSourceInfo*, clang::Expr*, clang::CXXRecordDecl*, clang::SourceLocation) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x62e581e)  
 #9 0x00000000062e6248 clang::Sema::BuildMemInitializer(clang::Decl*, clang::Scope*, clang::CXXScopeSpec&, clang::IdentifierInfo*, clang::OpaquePtr<clang::QualType>, clang::DeclSpec const&, clang::SourceLocation, clang::Expr*, clang::SourceLocation) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x62e6248)  
#10 0x0000000005f2b343 clang::Parser::ParseMemInitializer(clang::Decl*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f2b343)  
#11 0x0000000005f2e7c0 clang::Parser::ParseConstructorInitializer(clang::Decl*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f2e7c0)  
#12 0x0000000005ee8d87 clang::Parser::ParseFunctionDefinition(clang::ParsingDeclarator&, clang::Parser::ParsedTemplateInfo const&, clang::Parser::LateParsedAttrList*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5ee8d87)  
#13 0x0000000005f19cf2 clang::Parser::ParseDeclGroup(clang::ParsingDeclSpec&, clang::DeclaratorContext, clang::ParsedAttributes&, clang::SourceLocation*, clang::Parser::ForRangeInit*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f19cf2)  
#14 0x0000000005ee35b8 clang::Parser::ParseDeclarationOrFunctionDefinition(clang::ParsedAttributes&, clang::ParsedAttributes&, clang::ParsingDeclSpec*, clang::AccessSpecifier) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5ee35b8)  
#15 0x0000000005eec557 clang::Parser::ParseExternalDeclaration(clang::ParsedAttributes&, clang::ParsedAttributes&, clang::ParsingDeclSpec*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5eec557)  
#16 0x0000000005f31a5d clang::Parser::ParseInnerNamespace(llvm::SmallVector<clang::Parser::InnerNamespaceInfo, 4u> const&, unsigned int, clang::SourceLocation&, clang::ParsedAttributes&, clang::BalancedDelimiterTracker&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f31a5d)  
#17 0x0000000005f35f1d clang::Parser::ParseNamespace(clang::DeclaratorContext, clang::SourceLocation&, clang::SourceLocation) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f35f1d)  
#18 0x0000000005f1cde7 clang::Parser::ParseDeclaration(clang::DeclaratorContext, clang::SourceLocation&, clang::ParsedAttributes&, clang::ParsedAttributes&, clang::SourceLocation*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f1cde7)  
#19 0x0000000005eec585 clang::Parser::ParseExternalDeclaration(clang::ParsedAttributes&, clang::ParsedAttributes&, clang::ParsingDeclSpec*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5eec585)  
#20 0x0000000005f31a5d clang::Parser::ParseInnerNamespace(llvm::SmallVector<clang::Parser::InnerNamespaceInfo, 4u> const&, unsigned int, clang::SourceLocation&, clang::ParsedAttributes&, clang::BalancedDelimiterTracker&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f31a5d)  
#21 0x0000000005f35f1d clang::Parser::ParseNamespace(clang::DeclaratorContext, clang::SourceLocation&, clang::SourceLocation) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f35f1d)  
#22 0x0000000005f1cde7 clang::Parser::ParseDeclaration(clang::DeclaratorContext, clang::SourceLocation&, clang::ParsedAttributes&, clang::ParsedAttributes&, clang::SourceLocation*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5f1cde7)  
#23 0x0000000005eec585 clang::Parser::ParseExternalDeclaration(clang::ParsedAttributes&, clang::ParsedAttributes&, clang::ParsingDeclSpec*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5eec585)  
#24 0x0000000005eed46c clang::Parser::ParseTopLevelDecl(clang::OpaquePtr<clang::DeclGroupRef>&, clang::Sema::ModuleImportState&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5eed46c)  
#25 0x0000000005edbe11 clang::ParseAST(clang::Sema&, bool, bool) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x5edbe11)  
#26 0x00000000046fd308 clang::FrontendAction::Execute() (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x46fd308)  
#27 0x0000000004673f25 clang::CompilerInstance::ExecuteAction(clang::FrontendAction&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x4673f25)  
#28 0x00000000047d2011 clang::ExecuteCompilerInvocation(clang::CompilerInstance*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0x47d2011)  
#29 0x0000000000f0768a cc1_main(llvm::ArrayRef<char const*>, char const*, void*) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0xf0768a)  
#30 0x0000000000eff053 ExecuteCC1Tool(llvm::SmallVectorImpl<char const*>&, llvm::ToolContext const&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0xeff053)  
#31 0x0000000000f04a74 clang_main(int, char**, llvm::ToolContext const&) (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0xf04a74)  
#32 0x0000000000e1d010 main (/opt/cray/pe/cce/17.0.0/cce-clang/x86_64/bin/clang-17+0xe1d010)  
```

---

## Comment 1

> Username: mbs-c  
> Created at: 2024-05-15 07:35:10 UTC  
> Url: https://github.com/boostorg/url/issues/839#issuecomment-2111790863  

For the record, in case someone else stumbles upon this issue: This is https://github.com/llvm/llvm-project/issues/39319 and other clang-based compilers (in my case: ibm-clang) as well as previous versions of Boost.URL (in my case: 1.82.0) are also affected.
