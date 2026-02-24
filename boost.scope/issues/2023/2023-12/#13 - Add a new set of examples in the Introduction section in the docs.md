# #13 - Add a new set of examples in the Introduction section in the docs [Closed]

> Username: Lastique  
> Created at: 2023-12-02 17:27:19 UTC  
> Updated at: 2024-01-30 00:35:20 UTC  
> Closed at: 2024-01-30 00:35:19 UTC  
> Url: https://github.com/boostorg/scope/issues/13  

From Andrzej Krzemienski:  
  
>>> Given that in Boost.ScopeExit I need to implement the conditional  
>>> execution manually, I can do it in two ways. One is:  
```  
template< typename T >  
void add_object(T const& arg)  
{  
    auto it = objects.insert(Object());  
  
    auto uncaught_count = boost::core::uncaught_exceptions();  
          
    BOOST_SCOPE_EXIT_TPL(this_, it, uncaught_count)  
    {  
        if (uncaught_count != boost::core::uncaught_exceptions())  
            this_->objects.erase(it);  
    }  
    BOOST_SCOPE_EXIT_END;  
  
    it->start(arg);  
}  
```  
  
>>> The other is:  
  
```  
template< typename T >  
void add_object(T const& arg)  
{  
    auto it = objects.insert(Object());  
  
    bool active = true;  
  
    BOOST_SCOPE_EXIT_TPL(this_, it, &active)  
    {  
        if (active)  
            this_->objects.erase(it);  
    }  
    BOOST_SCOPE_EXIT_END;  
  
    it->start(arg);  
    active = false;  
}  
```  
>>> Given these options, I have zero motivation for using the former. The  
>>> latter one:  
>>> * is C++11 compatible (boost::uncaught_exceptions doesn't necessarily  
>>> work for prec++17 compilers)  
>>> * works correctly in 100% cases rather than in 99% of the cases (I mean  
>>> the caveats regarding coroutines and fibers)  
>>> * avoids two calls to a C++ runtime.  
>>>  
>>> I think the example with the `active` flag should be used in comparison.  
>>> And then, if you want to compare apples to apples, you would have to use  
>>> scope_exit and set_active. Otherwise, I think the comparison should  
>>> acknowledge that the solution with scope_fail has caveats and is slower.  
>>  
>> We've discussed this before, and there are pros and cons to each  
>> approach. The uncaught_exceptions-based solution encapsulates the  
>> rollback logic in one place, you don't have to set the active flag  
>> manually on every return point. It does have downsides as well, like  
>> higher performance cost (unless the compiler implements it using an  
>> intrinsic) and incompatibility with coroutines (which might not matter  
>> if you don't use or care about coroutines). So neither one is wrong or  
>> bad, it all depends on your requirements. Personally, I have used the  
>> solution based on uncaught_exceptions quite a few times back when I was  
>> using Boost.ScopeExit, for the reasons I mentioned.  
>>  
>> Back to the table in the docs, there are two main points I'd like to  
>> make. First, this is an Introduction section. It is not meant to be an  
>> exhaustive description of library features and caveats. It only serves  
>> to give the reader the basic understanding of what the library is about  
>> and a glimpse of how it might look in his code. Hence I do not think  
>> that it is the right place to dive into details and corner cases, there  
>> are other sections in the docs for that.  
>>  
>> Second, the reason I'm giving the uncaught_exceptions-based example in  
>> the table, besides that this is the kind of code I actually wrote and  
>> eventually converted to Boost.Scope, is that the  
>> uncaught_exceptions-based version of the Boost.ScopeExit code is the  
>> exact equivalent of the Boost.Scope example that uses scope_fail. IOW,  
>> this is apples-to-apples comparison, as opposed to using different  
>> solutions for different libraries. I think, this is only fair.  
>>  
>> You may argue to include a row of examples, where Boost.ScopeExit code  
>> uses the `active` flag. I suppose, I could do this, although to a great  
>> degree it would duplicate the row that already exists. I'm not sure how  
>> useful it would be, as it would considerably increase the size of the  
>> section for little value.  
>  
> I would encourage you to add a third row.  
> I understand that "Introduction" is only to give a potential user the first impression, and this is exactly why I am pushing for this. The current presentation has two downsides:  
> * It presents Boost.ScopeExit as worse than it really is.  
> * It might give an impression that the author is unaware of coroutine/fiber-related problems, and offers no solution for this case.
