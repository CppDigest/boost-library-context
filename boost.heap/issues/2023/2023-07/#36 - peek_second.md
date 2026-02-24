# #36 - peek_second [Closed]

> Username: mglisse  
> Created at: 2023-07-10 08:21:12 UTC  
> Updated at: 2023-12-17 08:15:51 UTC  
> Closed at: 2023-12-17 07:17:59 UTC  
> Url: https://github.com/boostorg/heap/issues/36  

Max-heap is good for looking at the highest elements lazily: look at top(), then pop(), look at top() again, etc. For some applications, it would be useful to be able to look at the top 2 elements (in my case, if the top 2 elements are equivalent, they cancel out and I should pop() twice to uncover the real top element). I can do that by storing top(), doing pop(), looking at top(), then reinserting the original max with push(), but that's rather wasteful. On the other hand, some data-structures (d_ary_heap in particular) can find the second element in constant time. So I think it would be nice to add such a function to those heaps that can support it efficiently.  
  
For optimality, we would want the work done in peek_second (this name is just a placeholder by the way, I don't care how it is named) not to be wasted if we end up doing a pop() afterwards. But I have trouble coming up with a nice interface for it (`maybe_pop(callback)` where the callback has access to the top 2 elements and returns true iff we should pop? That may be too ad-hoc). However, a way to peek, even suboptimal, would already be better than the current pop+top+push.

---

## Comment 1

> Username: timblechmann  
> Created at: 2023-12-17 07:17:59 UTC  
> Url: https://github.com/boostorg/heap/issues/36#issuecomment-1859059273  

please consider using the ordered iterators. that way can traverse the heap without modifying it

---

## Comment 2

> Username: mglisse  
> Created at: 2023-12-17 08:15:49 UTC  
> Url: https://github.com/boostorg/heap/issues/36#issuecomment-1859069461  

Thanks, I may have missed those.  
Note that when only looking at the first 1 or 2 elements, the ordered_iterators generate quite a lot of code compared to what is needed ([godbolt link](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:4,positionColumn:1,positionLineNumber:4,selectionStartColumn:1,selectionStartLineNumber:4,startColumn:1,startLineNumber:4),source:'%23include+%3Cboost/heap/d_ary_heap.hpp%3E%0Atypedef+boost::heap::d_ary_heap%3Clong,boost::heap::arity%3C2%3E%3E+H%3B%0A%0Along+f1(H%26h)%7Breturn+*h.ordered_begin()%3B%7D%0Along+f1ref(H%26h)%7Breturn+h.top()%3B%7D%0Along+f2(H%26h)%7Breturn+*%2B%2Bh.ordered_begin()%3B%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:32.3017978190392,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:g132,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:boost,ver:'183')),options:'-O3+-DNDEBUG',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+gcc+13.2+(Editor+%231)',t:'0')),k:34.36486884762747,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang_trunk,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!((name:boost,ver:'183')),options:'-stdlib%3Dlibc%2B%2B+-O3+-DNDEBUG',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+(trunk)+(Editor+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4)), and this is likely to affect the performance of this code.
