# #518 - Unified formatting through clang-format [Open]

> Username: simmplecoder  
> Created at: 2020-08-27 09:38:26 UTC  
> Updated at: 2021-04-12 16:51:50 UTC  
> Url: https://github.com/boostorg/gil/issues/518  

### Is your feature request related to a problem? Please describe.  
  
During PRs developers spend time on making formatting consistent across the project. That takes time and a thing that could be totally automated.  
  
### Describe the solution you'd like  
  
Add a `.clang-format` file that would dictate how files are formatted, and possibly create a hook so that it gets reformatted automatically  
  
### Describe alternatives you've considered  
  
I haven't found any formatting engine that would be as good as clang-format. Other IDEs either have their proprietary version or have moved to clang-format  
  
### Proposed .clang-format  
  
```---  
Language:        Cpp  
# BasedOnStyle:  LLVM  
AccessModifierOffset: -4  
AlignAfterOpenBracket: Align  
AlignConsecutiveMacros: false  
AlignConsecutiveAssignments: false  
AlignConsecutiveDeclarations: false  
AlignEscapedNewlines: Right  
AlignOperands:   true  
AlignTrailingComments: true  
AllowAllArgumentsOnNextLine: true  
AllowAllConstructorInitializersOnNextLine: true  
AllowAllParametersOfDeclarationOnNextLine: true  
AllowShortBlocksOnASingleLine: Never  
AllowShortCaseLabelsOnASingleLine: false  
AllowShortFunctionsOnASingleLine: None  
AllowShortLambdasOnASingleLine: None  
AllowShortIfStatementsOnASingleLine: Never  
AllowShortLoopsOnASingleLine: false  
AlwaysBreakAfterDefinitionReturnType: None  
AlwaysBreakAfterReturnType: None  
AlwaysBreakBeforeMultilineStrings: false  
AlwaysBreakTemplateDeclarations: Yes  
BinPackArguments: true  
BinPackParameters: true  
BraceWrapping:  
  AfterCaseLabel:  true  
  AfterClass:      true  
  AfterControlStatement: true  
  AfterEnum:       true  
  AfterFunction:   true  
  AfterNamespace:  false  
  AfterObjCDeclaration: true  
  AfterStruct:     true  
  AfterUnion:      false  
  AfterExternBlock: false  
  BeforeCatch:     false  
  BeforeElse:      true  
  BeforeLambdaBody: true  
  IndentBraces:    false  
  SplitEmptyFunction: false  
  SplitEmptyRecord: true  
  SplitEmptyNamespace: false  
BreakBeforeBinaryOperators: None  
BreakBeforeBraces: Custom  
BreakBeforeInheritanceComma: false  
BreakInheritanceList: BeforeColon  
BreakBeforeTernaryOperators: true  
BreakConstructorInitializersBeforeComma: false  
BreakConstructorInitializers: BeforeColon  
BreakAfterJavaFieldAnnotations: false  
BreakStringLiterals: true  
ColumnLimit:     100  
CommentPragmas:  '^ IWYU pragma:'  
CompactNamespaces: true  
ConstructorInitializerAllOnOneLineOrOnePerLine: false  
ConstructorInitializerIndentWidth: 4  
ContinuationIndentWidth: 4  
Cpp11BracedListStyle: true  
DeriveLineEnding: true  
DerivePointerAlignment: false  
DisableFormat:   false  
ExperimentalAutoDetectBinPacking: false  
FixNamespaceComments: true  
ForEachMacros:  
  - foreach  
  - Q_FOREACH  
  - BOOST_FOREACH  
IncludeBlocks:   Preserve  
IncludeCategories:  
  - Regex:           '^"(llvm|llvm-c|clang|clang-c)/'  
    Priority:        2  
    SortPriority:    0  
  - Regex:           '^(<|"(gtest|gmock|isl|json)/)'  
    Priority:        3  
    SortPriority:    0  
  - Regex:           '.*'  
    Priority:        1  
    SortPriority:    0  
IncludeIsMainRegex: '(Test)?$'  
IncludeIsMainSourceRegex: ''  
IndentCaseLabels: false  
IndentGotoLabels: true  
IndentPPDirectives: None  
IndentWidth:     4  
IndentWrappedFunctionNames: false  
JavaScriptQuotes: Leave  
JavaScriptWrapImports: true  
KeepEmptyLinesAtTheStartOfBlocks: true  
MacroBlockBegin: ''  
MacroBlockEnd:   ''  
MaxEmptyLinesToKeep: 1  
NamespaceIndentation: None  
ObjCBinPackProtocolList: Auto  
ObjCBlockIndentWidth: 2  
ObjCSpaceAfterProperty: false  
ObjCSpaceBeforeProtocolList: true  
PenaltyBreakAssignment: 2  
PenaltyBreakBeforeFirstCallParameter: 19  
PenaltyBreakComment: 300  
PenaltyBreakFirstLessLess: 120  
PenaltyBreakString: 1000  
PenaltyBreakTemplateDeclaration: 10  
PenaltyExcessCharacter: 1000000  
PenaltyReturnTypeOnItsOwnLine: 60  
PointerAlignment: Left  
ReflowComments:  true  
SortIncludes:    true  
SortUsingDeclarations: true  
SpaceAfterCStyleCast: false  
SpaceAfterLogicalNot: false  
SpaceAfterTemplateKeyword: true  
SpaceBeforeAssignmentOperators: true  
SpaceBeforeCpp11BracedList: false  
SpaceBeforeCtorInitializerColon: true  
SpaceBeforeInheritanceColon: true  
SpaceBeforeParens: ControlStatements  
SpaceBeforeRangeBasedForLoopColon: true  
SpaceInEmptyBlock: false  
SpaceInEmptyParentheses: false  
SpacesBeforeTrailingComments: 1  
SpacesInAngles:  false  
SpacesInConditionalStatement: false  
SpacesInContainerLiterals: true  
SpacesInCStyleCastParentheses: false  
SpacesInParentheses: false  
SpacesInSquareBrackets: false  
SpaceBeforeSquareBrackets: false  
Standard:        Latest  
StatementMacros:  
  - Q_UNUSED  
  - QT_REQUIRE_VERSION  
TabWidth:        4  
UseCRLF:         false  
UseTab:          Never  
...  
```  
  
I have tried this out, and it works for our codebase. The only problem being `BeforeLambdaBody` is introduced in clang-format 12 I believe, which is yet to be released.

---

## Comment 1

> Username: mloskot  
> Created at: 2020-08-27 10:00:53 UTC  
> Updated at: 2021-04-12 16:51:50 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-681851554  

First, I'm a huge supporter of incorporating clang-format. I'm happy to be not a lonely one :)  
  
Have you seen these?  
- https://github.com/boostorg/gil/pull/87  
- [example/clang-format](https://github.com/boostorg/gil/blob/23702f9879e81bb3b08971eb153b199ea56bb0af/example/clang-format/)

---

## Comment 2

> Username: simmplecoder  
> Created at: 2020-08-27 10:08:17 UTC  
> Updated at: 2020-08-27 10:16:00 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-681855305  

Perhaps I could submit a PR then to update the file, and the people who want it will just   
`ln -s $PWD/example/clang-format/.clang-format $PWD/.clang-format` ?  
  
I believe that would be a reasonable tradeoff. @lpranam @codejaeger @stefanseefeld what do you think?  
  
Perhaps we could try testing it on latest PRs and put it at the root of the project?

---

## Comment 3

> Username: mloskot  
> Created at: 2020-08-27 10:21:35 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-681861582  

I personally give  
- higher priority to **consistent** coding style and formatting throughout the whole codebase  
- lower priority to what particular coding style is used  
  
As long as the coding style makes code readable, compact, horizontally limited, I'm fine with it.  
  
The lack of consistency in GIL, especially in parts of IO, have been annoying, but manual improvements and maintenance cost lots of time. Hence, I'm eager to require use of clang-format, even if I need to trade personal preferences :-)

---

## Comment 4

> Username: lpranam  
> Created at: 2020-09-05 18:15:12 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-687644160  

> Perhaps we could try testing it on latest PRs and put it at the root of the project?  
  
I agree with this.  At least we can have consistent styling in new code.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-09-08 08:49:46 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-688721865  

I'd add to the overall discussion this RFC of mine https://trac.osgeo.org/geos/wiki/RFC4 where some details and implications are discussed, including external references, especially three parts of MongoDB story how they managed the "big reformat":  
  
- [Succeeding With ClangFormat, Part 1: Pitfalls And Planning](https://engineering.mongodb.com/post/succeeding-with-clangformat-part-1-pitfalls-and-planning/)  
- [Succeeding With ClangFormat, Part 2: The Big Reformat](https://engineering.mongodb.com/post/succeeding-with-clangformat-part-2-the-big-reformat/)  
- [Succeeding With ClangFormat, Part 3: Persisting The Change](https://engineering.mongodb.com/post/succeeding-with-clangformat-part-3-persisting-the-change/)

---

## Comment 6

> Username: mloskot  
> Created at: 2020-10-22 10:41:05 UTC  
> Updated at: 2020-10-22 10:41:29 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714404404  

We should give this a try :-)  
  
https://www.youtube.com/watch?v=_EaU3VsDOJM  
  
> Clang Format Detector automatically finds the best matching Clang-Format Style for your code files.

---

## Comment 7

> Username: sdebionne  
> Created at: 2020-10-22 11:53:56 UTC  
> Updated at: 2020-10-22 11:54:55 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714441268  

I am also all in for clang-format.  
  
To avoid introducing a `git diff` barrier, making difficult to compare code before and after the "big reformat", we recently end-up rewriting the all repos history with `git filter-repo`. We kept "replace reference" for the old commits so that it did not break the reference to the commits in the master project (and since GIL is a git submodule for Boost we are in the same situation). Note that sometimes `clang-format` needs to be applied several times (or even does not converge!), aka first run leads some reformating, reapplying leads to more reformating. At least that's my experience with my repo.

---

## Comment 8

> Username: mloskot  
> Created at: 2020-10-22 12:00:43 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714444692  

Once we agree on the definition of [.clang-format](https://github.com/boostorg/gil/tree/23702f9879e81bb3b08971eb153b199ea56bb0af/example/clang-format), I don't mind either exploring the history rewrite option or just swallowing the "big reformat" pill.

---

## Comment 9

> Username: lpranam  
> Created at: 2020-10-22 14:45:18 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714544982  

In past, I have used `git filter-branch --tree-filter` to reformate the entire code base while also maintaining the git commit history for blaming but I think this too would require force push and actually rewriting history. I have never used `git filter-repo` but I suspect it would be doing the same thing and would rewrite the history...

---

## Comment 10

> Username: sdebionne  
> Created at: 2020-10-22 16:13:15 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714601499  

Quoting the `git filter-branch` doc:  
> The `git filter-repo` tool is an alternative to `git-filter-branch` which does not suffer from these performance problems or the safety problems (mentioned below).  
  
Is there any Boost wide `.clang-format` that we could use?

---

## Comment 11

> Username: mloskot  
> Created at: 2020-10-22 16:18:36 UTC  
> Url: https://github.com/boostorg/gil/issues/518#issuecomment-714604737  

@sdebionne   
> Is there any Boost wide .clang-format that we could use?  
  
No, or I don't know about any, but some individual libraries have some (including us):  
  
```  
/d/boost.win (develop u= origin/develop) $ find . -name .clang-format  
./libs/gil/example/clang-format/.clang-format  
./libs/graph/.clang-format  
./libs/histogram/.clang-format  
./libs/multiprecision/.clang-format  
./libs/nowide/.clang-format  
./libs/numeric/ublas/.clang-format  
./libs/outcome/.clang-format  
./libs/yap/.clang-format  
```
