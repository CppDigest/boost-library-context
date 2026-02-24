# #110 - Minor documentation improvements [Closed]

> Username: kuzminrobin  
> Created at: 2019-07-26 19:27:01 UTC  
> Updated at: 2020-04-24 09:02:27 UTC  
> Closed at: 2020-04-24 09:02:19 UTC  
> Url: https://github.com/boostorg/context/issues/110  

In the file "https://www.boost.org/doc/libs/1_70_0/libs/context/doc/html/context/ff.html"  
**A. Major Questions**  
1. The second occurrence of `return std::move(f2);`.  
Why does labda (that must return void) actually return something? See fragment "**The function passed as argument must** accept a rvalue reference to fiber and **return void**."  
1.  Sentence "**Another option** is to execute a function on top of the fiber that throws an exception".  
a. Causes the questions "**Another option** of what"? Of exception handling? Of executing a piece of code on top of a fiber?  
b. Looking at the example code I feel that the sentence needs to be reworded to  
".. is to execute a function **that throws an exception** on top of the fiber"  
1. Fragment "and catched inside the **for-loop**".  
Where is the **for-loop**?  
1. Multiple occurrences of `return {}`. Is that a valid C++? Is that a correct return value?  
1. f{std::allocator_arg,preallocated(**sp**,size,sctx),salloc,**entry_func**}  
What is **entry_func**?  
What if **sp** is replaced with `static_cast<void*>(this)`?   
(and the parameter **sp** is removed from the constructor _'my_control_structure(**void \* sp,**'_,   
and the argument **sp** is removed from the call _'cs=new(sp)my_control_structure( **sp,** size,sctx,salloc)'_)  
---  
**B. Typos**  
1. I see:  
The local**e** variables b and next  
I expected:  
The **local** variables b and next  
(in the word "local**e**" the last letter 'e' seems to me misleading).  
1. I see: "inside **c**1,i==1",  
I expected: "inside **f**1,i==1".  
1. I see  
emits an**s** exception  
I expected  
emits an exception  
1. propagation of**s** the  
1. requires to allocat**ed** the stack_context   
1. catch**ed** inside the for-loop  
**caught** inside the for-loop  
1. micr**o-p**rocessors  
micr**op**rocessors  
1. has to specify a**n** fiber  
has to specify **a** fiber  
1. variables b and next re**m**ain their values  
variables b and next re**t**ain their values  
1. of a**n** valid  
of **a** valid  
---  
**C. Recommended for clarity**  
1. **sink** is captured **(current-fiber)**  
**sink (current-fiber)** is captured  
1. The function passed as argument must accept a rvalue reference  
The function passed as **an** argument must accept a**n** rvalue reference  
---  
**D. I expect the confirmation form the author whether the following notes are applicable.**  
1. When talking about `resume_with()` I would recommend to give different names (in the form of the code comments) to the 2 lambdas - the one associated with `f1` (e.g. `// Lambda A.`) and the other one invoked with `resume_with()` (e.g. `// Lambda B.`).  
Then I would recommend to reword the sentence  
"The expression f1.resume_with(...) executes a lambda on top of fiber f1, e.g. an additional stack frame is allocated on top of the stack"   
to something like this:  
"The expression `f1.resume_with(..)` executes **the lambda B immediately prior to switching to lambda A, in particular** an additional stack frame is allocated **for lambda B** on top of the stack **frame of lambda A**".  
(In this sentence I also replaced "e.g." with "**in particular**" because the fragment "e.g." tells me that it is _one of many possible ways_, whereas I intuitively feel that it is the _only implemented way_. If I'm mistaken then feel free to leave "e.g.", however in that case I would recommend to add a clarification like this "e.g. one possible implementation can be ")  
1. I see  
This lambda assigns -1 to data and returns to the **second** invocation of f1.resume().  
I expected  
This lambda assigns -1 to data and returns to the **third** invocation of f1.resume().  
1. can be **applied** to and returned from a function  
can be **passed** to and returned from a function  
1. If the function executed inside a context-function emits ans exception  
If an exception escapes from the context-function  
1. Do not **jump from** inside a catch block  
Do not **switch to a different fiber** inside of a catch block  
1. Sometimes it is useful to execute a new function on top of a resumed fiber  
Sometimes it is useful to execute some function immediately prior to resuming the other fiber  
---  
**E. Note**  
I did not run through the **Inverting the control flow** section.

---

## Comment 1

> Username: kuzminrobin  
> Created at: 2019-07-26 19:29:14 UTC  
> Url: https://github.com/boostorg/context/issues/110#issuecomment-515572355  

In the mean time I proceed to making fixes and preparing the pull-request.

---

## Comment 2

> Username: olk  
> Created at: 2019-07-28 07:50:49 UTC  
> Url: https://github.com/boostorg/context/issues/110#issuecomment-515741243  

**A. Major Questions**  
1. the lambda has to return a fiber  
2. a) one option is to modify some data (example: lambda assigns -1 to data) or the lambda throws an exception (can be used to unwind the stack)  
2. b) OK  
3.  "and catched inside the for-loop" is unnecessary  
4. yes, its correct C++ code  
5. should be:  
```struct my_control_structure  {  
    // captured fiber  
    ctx::fiber   f;  
  
    ctx::fiber entry_func(ctx::fiber&&) {  
      ...  
    }  
  
    template< typename StackAllocator >  
    my_control_structure(void * sp,std::size_t size,stack_context sctx,StackAllocator salloc) :  
        // create captured fiber  
        f{std::allocator_arg,preallocated(sp,size,sctx),salloc,entry_func} {  
    }  
    ...  
};  
```  
  
**B** and **C** are OK  
  
**D**  
1. first part is OK,   
"The expression f1.resume_with(..) executes the lambda B immediately prior to switching to lambda A, in particular an additional stack frame is allocated for lambda B on top of the stack frame of lambda A".  
-> lambda B is immediately executed after switching to lambda A but is executed before entering lambda A again (resume_with() suspends main(), switches to f1 but executed lambda B inside f1 first)  
2.  not sure what you mean, but the example output is correct:  
```output:  
    f1: entered first time: 0  
    f1: returned first time: 1  
    f1: entered second time: 2  
    f1: returned second time: 3  
    f2: entered: 4  
    f1: entered third time: -1  
    f1: returned third time  
```  
3. OK  
4. OK  
5. OK  
6. OK  
  
ty

---

## Comment 3

> Username: kuzminrobin  
> Created at: 2019-07-29 18:16:27 UTC  
> Updated at: 2019-07-29 18:42:36 UTC  
> Url: https://github.com/boostorg/context/issues/110#issuecomment-516104311  

@olk ,   
(I observe a slight mismatch between   
the enumeration of the questions and  
the enumeration of the answers.  
I assume that your   
answer A.3.b relates to the question A.2.b,  
answers A.4, A.5, and A.6 relate to the question A.3, A.4, and A.5 correspondingly)  
  
**A.1:** You wrote: _"the lambda has to return a fiber"_.   
If the lambda has to return a fiber then in the sentence   
> _"The function passed as argument must accept a rvalue reference to fiber and return **void**"_   
  
the fragment _"and return **void**"_ misleads. The fragment needs to be   
either replaced with _"and return **fiber**"_   
or removed.  
Do you agree? If yes then shall I (in my fix) replace the fragment with _"and return **fiber**"_? (In this case feel free to answer: "A.1: I agree, replace **void** with **fiber**")  
  
**A.2.a:** (Still is not clear for me. I'll ask differently later if the answer is still required)  
**A.2.b:** I assume your answer is   
Yes, please reword to _".. is to execute a function **that throws an exception** on top of the fiber"_  
(feel free to confirm with "A.2.b: Confirmed")  
  
**A.3:** (I have noticed extra fragments that attracted my attention, may be they are a result of recent changes in the documentation?) I assume your answer is:  
_Yes, please reword the sentence_   
> "In this **exception** my_exception is thro**w from** a function invoked on-top of fiber f **and catched inside the for-loop**"  
  
_to_   
> "In this **fragment** my_exception is throw**n by** a function invoked on-top of fiber f"  
  
(feel free to confirm with "A.3: Confirmed").  
  
**A.4:** Consists of 2 parts (sub-questions). You answered the first part (_"its correct C++ code"_). But the second part (_"Is that a correct return value?"_) is still unclear for me.  
As I understand the statement `return {}` returns the default-constructed instance of type `ctx::fiber`, and that instance _has no any relation_ to the lambda's parameter `ctx::fiber && f`. But I intuitively feel that the value being returned _should somehow be associated with the parameter_ `f`.  
Please clarify.  
  
**A.5.**{What if `sp` is ..}**:** What will happen if I write (optimize) like this? See the code comments only:  
```c++  
my_control_structure * cs=new(sp)my_control_structure(size,sctx,salloc);  // I removed the first-most argument `sp`.  
...  
struct my_control_structure  {  
    ...  
    template< typename StackAllocator >  
    my_control_structure(                                                // I removed the first-most parameter `sp`.  
            std::size_t size,stack_context sctx,StackAllocator salloc) :  
        f{std::allocator_arg,preallocated(static_cast<void*>(this),      // I replaced the first-most argument `sp` with `static_cast<void*>(this)` (because the value is the same in case of a placement `new`).  
            size,sctx),salloc,entry_func} {  
    }  
    ...  
};  
```  
**D.1** and **D.2:** I assume the fragment:  
> The expression f1.resume_with(...) executes a lambda on top of fiber f1, e.g. an additional stack frame is allocated on top of the stack. This lambda assigns -1 to data and returns to the second invocation of f1.resume()  
  
can be re-written like this:  
> The expression `f1.resume_with(..)` executes **the lambda B immediately after switching to but before resuming the lambda A, in particular an additional stack frame is allocated for lambda B on top of the stack frame of lambda A. Lambda B** assigns -1 to data and returns **to an address in the beginning of the fragment `std::cout << "f1: entered third time: "`**.

---

## Comment 4

> Username: olk  
> Created at: 2019-07-29 20:08:47 UTC  
> Url: https://github.com/boostorg/context/issues/110#issuecomment-516142908  

A.1: "The function passed as argument must accept a rvalue reference to fiber and return a fiber."  
A.2.b: Confirmed  
A.3: Confirmed  
A.4: `return {}` returns a default constructed fiber  
```f=std::move(f).resume_with([](ctx::fiber && f) ->ctx::fiber {  
    throw my_exception(std::move(f),"abc");  
    return {};  
});  
```  
resumes `f` and executes the lambda on top of the resumed `f` - `return {}` is correct because the exception already takes the fiber (moveable only)  
A.5: my_control_structure gets overwritten  
D.1 and D.2 correct

---

## Comment 5

> Username: olk  
> Created at: 2020-04-24 09:02:19 UTC  
> Updated at: 2020-04-24 09:02:27 UTC  
> Url: https://github.com/boostorg/context/issues/110#issuecomment-618894267  

no response - closing
