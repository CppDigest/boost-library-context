# #293 - UBSAN reports unaligned access error. [Closed]

> Username: eddeighton  
> Created at: 2024-10-25 13:58:39 UTC  
> Updated at: 2025-10-26 22:01:31 UTC  
> Closed at: 2025-10-26 22:01:31 UTC  
> Url: https://github.com/boostorg/container/issues/293  

godbolt reproduces issue: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:13,positionColumn:1,positionLineNumber:13,selectionStartColumn:1,selectionStartLineNumber:13,startColumn:1,startLineNumber:13),source:'%0A%23include+%3Cboost/container/vector.hpp%3E%0A%0Astruct+TypeIsCustomAlignment+%7B%0A++++char+buffer%5B1000%5D+__attribute__((+aligned(64)+))+%3B%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++boost::container::vector%3C+TypeIsCustomAlignment+%3E+v(1)%3B%0A++++return+0%3B%0A%7D%0A'),l:'5',n:'1',o:'C%2B%2B+source+%231',t:'0')),k:37.36593736593737,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:g142,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1',verboseDemangling:'0'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:boost,ver:'185')),options:'-fsanitize%3Dundefined',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+gcc+14.2+(Editor+%231)',t:'0')),k:50,l:'4',m:61.511423550087876,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+14.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+gcc+14.2+(Compiler+%231)',t:'0')),header:(),l:'4',m:38.488576449912124,n:'0',o:'',s:0,t:'0')),k:62.63406263406264,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-10-26 22:01:31 UTC  
> Url: https://github.com/boostorg/container/issues/293#issuecomment-3448955287  

Thanks for the report. The issue is that new_allocator does not support over-aligned types. Fixed in commit:  
  
https://github.com/boostorg/container/commit/e18078f846a32a346e34b5bfae409befb5c359c7
