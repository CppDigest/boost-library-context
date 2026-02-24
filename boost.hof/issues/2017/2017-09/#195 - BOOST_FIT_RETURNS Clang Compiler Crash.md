# #195 - BOOST_FIT_RETURNS Clang Compiler Crash [Open]

> Username: ricejasonf  
> Created at: 2017-09-19 18:01:53 UTC  
> Updated at: 2017-09-19 19:28:59 UTC  
> Url: https://github.com/boostorg/hof/issues/195  

This is just something I found during my review. I don't really think it needs a workaround. I'm not even sure that it is intrinsic to my use of the Fit macro at all.  
  
I tried implementing `make_array` using Fit. Here is a minimal example:  
  
```cpp  
#include <boost/fit/arg.hpp>  
#include <boost/fit/decay.hpp>  
#include <boost/fit/returns.hpp>  
#include <array>  
  
namespace fit = boost::fit;  
  
constexpr auto make_array = [](auto&& ...x) BOOST_FIT_RETURNS(  
  std::array<decltype(fit::decay(fit::arg_c<1>(x...))), sizeof...(x)>{  
    x...  
  }  
);  
  
int main()  
{  
  auto my_ints = make_array(  
    int{0}  
  );  
}  
```  
Here is the diagnostic output from the compiler:  
```  
#0 0x00007efe39191138 llvm::sys::PrintStackTrace(llvm::raw_ostream&) (/usr/local/bin/clang-4.0+0x12d9138)  
#1 0x00007efe3918eef6 llvm::sys::RunSignalHandlers() (/usr/local/bin/clang-4.0+0x12d6ef6)  
#2 0x00007efe3918f03c SignalHandler(int) (/usr/local/bin/clang-4.0+0x12d703c)  
#3 0x00007efe37a83670 __restore_rt (/lib/x86_64-linux-gnu/libpthread.so.0+0x11670)  
#4 0x00007efe3a2c14ae clang::Sema::tryCaptureVariable(clang::VarDecl*, clang::SourceLocation, clang::Sema::TryCaptureKind, clang::SourceLocation, bool, clang::QualType&, clang::QualType&, unsigned int const*) (/usr/local/bin/clang-4.0+0x24094ae)  
#5 0x00007efe3a2c33b1 clang::Sema::NeedToCaptureVariable(clang::VarDecl*, clang::SourceLocation) (/usr/local/bin/clang-4.0+0x240b3b1)  
#6 0x00007efe3a2c4e08 clang::Sema::BuildDeclRefExpr(clang::ValueDecl*, clang::QualType, clang::ExprValueKind, clang::DeclarationNameInfo const&, clang::CXXScopeSpec const*, clang::NamedDecl*, clang::TemplateArgumentListInfo const*) (/usr/local/bin/clang-4.0+0x240ce08)  
#7 0x00007efe3a2c50a7 clang::Sema::BuildDeclRefExpr(clang::ValueDecl*, clang::QualType, clang::ExprValueKind, clang::SourceLocation, clang::CXXScopeSpec const*) (/usr/local/bin/clang-4.0+0x240d0a7)  
#8 0x00007efe3a531d1f clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformSizeOfPackExpr(clang::SizeOfPackExpr*) (/usr/local/bin/clang-4.0+0x2679d1f)  
#9 0x00007efe3a526597 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformExpr(clang::Expr*) (/usr/local/bin/clang-4.0+0x266e597)  
#10 0x00007efe3a530cf5 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformTemplateArgument(clang::TemplateArgumentLoc const&, clang::TemplateArgumentLoc&, bool) (/usr/local/bin/clang-4.0+0x2678cf5)  
#11 0x00007efe3a5344f0 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformTemplateSpecializationType(clang::TypeLocBuilder&, clang::TemplateSpecializationTypeLoc, clang::TemplateName) (/usr/local/bin/clang-4.0+0x267c4f0)  
#12 0x00007efe3a51d39b clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformType(clang::TypeLocBuilder&, clang::TypeLoc) (/usr/local/bin/clang-4.0+0x266539b)  
#13 0x00007efe3a53686e clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformElaboratedType(clang::TypeLocBuilder&, clang::ElaboratedTypeLoc) (/usr/local/bin/clang-4.0+0x267e86e)  
#14 0x00007efe3a51e008 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformType(clang::TypeLocBuilder&, clang::TypeLoc) (/usr/local/bin/clang-4.0+0x2666008)  
#15 0x00007efe3a51f081 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformType(clang::TypeSourceInfo*) (/usr/local/bin/clang-4.0+0x2667081)  
#16 0x00007efe3a5282bc clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformCXXUnresolvedConstructExpr(clang::CXXUnresolvedConstructExpr*) (/usr/local/bin/clang-4.0+0x26702bc)  
#17 0x00007efe3a526364 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformExpr(clang::Expr*) (/usr/local/bin/clang-4.0+0x266e364)  
#18 0x00007efe3a51e7ec clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformType(clang::TypeLocBuilder&, clang::TypeLoc) (/usr/local/bin/clang-4.0+0x26667ec)  
#19 0x00007efe3a51f081 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformType(clang::TypeSourceInfo*) (/usr/local/bin/clang-4.0+0x2667081)  
#20 0x00007efe3a5282bc clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformCXXUnresolvedConstructExpr(clang::CXXUnresolvedConstructExpr*) (/usr/local/bin/clang-4.0+0x26702bc)  
#21 0x00007efe3a526364 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformExpr(clang::Expr*) (/usr/local/bin/clang-4.0+0x266e364)  
#22 0x00007efe3a52c1e7 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformCXXNoexceptExpr(clang::CXXNoexceptExpr*) (/usr/local/bin/clang-4.0+0x26741e7)  
#23 0x00007efe3a5263b7 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformExpr(clang::Expr*) (/usr/local/bin/clang-4.0+0x266e3b7)  
#24 0x00007efe3a52e8c8 clang::TreeTransform<(anonymous namespace)::TemplateInstantiator>::TransformExceptionSpec(clang::SourceLocation, clang::FunctionProtoType::ExceptionSpecInfo&, llvm::SmallVectorImpl<clang::QualType>&, bool&) (/usr/local/bin/clang-4.0+0x26768c8)  
#25 0x00007efe3a52ec34 clang::Sema::SubstExceptionSpec(clang::FunctionDecl*, clang::FunctionProtoType const*, clang::MultiLevelTemplateArgumentList const&) (/usr/local/bin/clang-4.0+0x2676c34)  
#26 0x00007efe3a5413f6 clang::Sema::InstantiateExceptionSpec(clang::SourceLocation, clang::FunctionDecl*) (/usr/local/bin/clang-4.0+0x26893f6)  
#27 0x00007efe3a28294a clang::Sema::ResolveExceptionSpec(clang::SourceLocation, clang::FunctionProtoType const*) (/usr/local/bin/clang-4.0+0x23ca94a)  
#28 0x00007efe3a417d48 CreateFunctionRefExpr(clang::Sema&, clang::FunctionDecl*, clang::NamedDecl*, bool, clang::SourceLocation, clang::DeclarationNameLoc const&) (/usr/local/bin/clang-4.0+0x255fd48)  
#29 0x00007efe3a449d88 clang::Sema::BuildCallToObjectOfClassType(clang::Scope*, clang::Expr*, clang::SourceLocation, llvm::MutableArrayRef<clang::Expr*>, clang::SourceLocation) (/usr/local/bin/clang-4.0+0x2591d88)  
#30 0x00007efe3a2cdb79 clang::Sema::ActOnCallExpr(clang::Scope*, clang::Expr*, clang::SourceLocation, llvm::MutableArrayRef<clang::Expr*>, clang::SourceLocation, clang::Expr*, bool) (/usr/local/bin/clang-4.0+0x2415b79)  
#31 0x00007efe39f33d0e clang::Parser::ParsePostfixExpressionSuffix(clang::ActionResult<clang::Expr*, true>) (/usr/local/bin/clang-4.0+0x207bd0e)  
#32 0x00007efe39f2ea93 clang::Parser::ParseCastExpression(bool, bool, bool&, clang::Parser::TypeCastState) (/usr/local/bin/clang-4.0+0x2076a93)  
#33 0x00007efe39f3108d clang::Parser::ParseCastExpression(bool, bool, clang::Parser::TypeCastState) (/usr/local/bin/clang-4.0+0x207908d)  
#34 0x00007efe39f327c9 clang::Parser::ParseAssignmentExpression(clang::Parser::TypeCastState) (/usr/local/bin/clang-4.0+0x207a7c9)  
#35 0x00007efe39f00aeb clang::Parser::ParseDeclarationAfterDeclaratorAndAttributes(clang::Declarator&, clang::Parser::ParsedTemplateInfo const&, clang::Parser::ForRangeInit*) (/usr/local/bin/clang-4.0+0x2048aeb)  
#36 0x00007efe39f0fda0 clang::Parser::ParseDeclGroup(clang::ParsingDeclSpec&, unsigned int, clang::SourceLocation*, clang::Parser::ForRangeInit*) (/usr/local/bin/clang-4.0+0x2057da0)  
#37 0x00007efe39f12bc4 clang::Parser::ParseSimpleDeclaration(unsigned int, clang::SourceLocation&, clang::Parser::ParsedAttributesWithRange&, bool, clang::Parser::ForRangeInit*) (/usr/local/bin/clang-4.0+0x205abc4)  
#38 0x00007efe39f12e4b clang::Parser::ParseDeclaration(unsigned int, clang::SourceLocation&, clang::Parser::ParsedAttributesWithRange&) (/usr/local/bin/clang-4.0+0x205ae4b)  
#39 0x00007efe39f73f0f clang::Parser::ParseStatementOrDeclarationAfterAttributes(llvm::SmallVector<clang::Stmt*, 32u>&, clang::Parser::AllowedContsructsKind, clang::SourceLocation*, clang::Parser::ParsedAttributesWithRange&) (/usr/local/bin/clang-4.0+0x20bbf0f)  
#40 0x00007efe39f74540 clang::Parser::ParseStatementOrDeclaration(llvm::SmallVector<clang::Stmt*, 32u>&, clang::Parser::AllowedContsructsKind, clang::SourceLocation*) (/usr/local/bin/clang-4.0+0x20bc540)  
#41 0x00007efe39f78390 clang::Parser::ParseCompoundStatementBody(bool) (/usr/local/bin/clang-4.0+0x20c0390)  
#42 0x00007efe39f7abab clang::Parser::ParseFunctionStatementBody(clang::Decl*, clang::Parser::ParseScope&) (/usr/local/bin/clang-4.0+0x20c2bab)  
#43 0x00007efe39ef0e15 clang::Parser::ParseFunctionDefinition(clang::ParsingDeclarator&, clang::Parser::ParsedTemplateInfo const&, clang::Parser::LateParsedAttrList*) (/usr/local/bin/clang-4.0+0x2038e15)  
#44 0x00007efe39f0fc53 clang::Parser::ParseDeclGroup(clang::ParsingDeclSpec&, unsigned int, clang::SourceLocation*, clang::Parser::ForRangeInit*) (/usr/local/bin/clang-4.0+0x2057c53)  
#45 0x00007efe39eeb0a1 clang::Parser::ParseDeclOrFunctionDefInternal(clang::Parser::ParsedAttributesWithRange&, clang::ParsingDeclSpec&, clang::AccessSpecifier) (/usr/local/bin/clang-4.0+0x20330a1)  
#46 0x00007efe39eeb709 clang::Parser::ParseDeclarationOrFunctionDefinition(clang::Parser::ParsedAttributesWithRange&, clang::ParsingDeclSpec*, clang::AccessSpecifier) [clone .part.148] (/usr/local/bin/clang-4.0+0x2033709)  
#47 0x00007efe39ef2740 clang::Parser::ParseExternalDeclaration(clang::Parser::ParsedAttributesWithRange&, clang::ParsingDeclSpec*) (/usr/local/bin/clang-4.0+0x203a740)  
#48 0x00007efe39ef35a7 clang::Parser::ParseTopLevelDecl(clang::OpaquePtr<clang::DeclGroupRef>&) (/usr/local/bin/clang-4.0+0x203b5a7)  
#49 0x00007efe39ee7cb2 clang::ParseAST(clang::Sema&, bool, bool) (/usr/local/bin/clang-4.0+0x202fcb2)  
#50 0x00007efe396afa36 clang::FrontendAction::Execute() (/usr/local/bin/clang-4.0+0x17f7a36)  
#51 0x00007efe3967cb3c clang::CompilerInstance::ExecuteAction(clang::FrontendAction&) (/usr/local/bin/clang-4.0+0x17c4b3c)  
#52 0x00007efe3974c533 clang::ExecuteCompilerInvocation(clang::CompilerInstance*) (/usr/local/bin/clang-4.0+0x1894533)  
#53 0x00007efe384cca28 cc1_main(llvm::ArrayRef<char const*>, char const*, void*) (/usr/local/bin/clang-4.0+0x614a28)  
#54 0x00007efe38483073 main (/usr/local/bin/clang-4.0+0x5cb073)  
#55 0x00007efe36c22421 __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x20421)  
#56 0x00007efe384cac0a _start (/usr/local/bin/clang-4.0+0x612c0a)  
Stack dump:  
0.	Program arguments: /usr/local/bin/clang-4.0 -cc1 -triple x86_64-unknown-linux-gnu -emit-obj -mrelax-all -disable-free -disable-llvm-verifier -discard-value-names -main-file-name main3.cpp -mrelocation-model static -mthread-model posix -mdisable-fp-elim -fmath-errno -masm-verbose -mconstructor-aliases -munwind-tables -fuse-init-array -target-cpu x86-64 -dwarf-column-info -debugger-tuning=gdb -resource-dir /usr/local/bin/../lib/clang/4.0.0 -internal-isystem /usr/local/bin/../include/c++/v1 -internal-isystem /usr/local/include -internal-isystem /usr/local/bin/../lib/clang/4.0.0/include -internal-externc-isystem /usr/include/x86_64-linux-gnu -internal-externc-isystem /include -internal-externc-isystem /usr/include -std=c++1z -fdeprecated-macro -fdebug-compilation-dir /opt/build -ferror-limit 19 -fmessage-length 106 -fobjc-runtime=gcc -fcxx-exceptions -fexceptions -fdiagnostics-show-option -o /tmp/main3-fddc10.o -x c++ /opt/src/main3.cpp   
1.	/opt/src/main3.cpp:19:3: current parser token ')'  
2.	/opt/src/main3.cpp:16:1: parsing function body 'main'  
3.	/opt/src/main3.cpp:16:1: in compound statement ('{}')  
clang-4.0: error: unable to execute command: Segmentation fault (core dumped)  
clang-4.0: error: clang frontend command failed due to signal (use -v to see invocation)  
clang version 4.0.0 (https://github.com/kripken/emscripten-fastcomp-clang.git f156a4f42ff4b0b72ef4839dfda2519b12f17b1d) (https://github.com/kripken/emscripten-fastcomp.git 51ce48a2c0599ef81b2830b72ff5b86b3cec6db3) (emscripten 1.37.19 : 1.37.19)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
clang-4.0: note: diagnostic msg: PLEASE submit a bug report to http://llvm.org/bugs/ and include the crash backtrace, preprocessed source, and associated run script.  
clang-4.0: note: diagnostic msg:   
```  
FWIW Here is my minimal example:  
```cpp  
#include <array>  
  
constexpr auto make_array = [](auto x1, auto ...x) noexcept(noexcept(  
  decltype(std::array<decltype(x1), (sizeof...(x) + 1)>{ x1, x... })(  
    std::array<decltype(x1), (sizeof...(x) + 1)>{ x1, x... }  
  )  
)) -> decltype(std::array<decltype(x1), (sizeof...(x) + 1)>{ x1, x... }) {  
  return std::array<decltype(x1), (sizeof...(x) + 1)>{ x1, x... };  
};  
  
  
int main()  
{  
  auto my_ints = make_array(  
    int{0}  
  );  
}  
```  
  
I'm not sure how I would word this in a bug report to LLVM/Clang. Any help on that would be great!

---

## Comment 1

> Username: pfultz2  
> Created at: 2017-09-19 19:28:59 UTC  
> Url: https://github.com/boostorg/hof/issues/195#issuecomment-330648307  

Let me try to creduce it.
