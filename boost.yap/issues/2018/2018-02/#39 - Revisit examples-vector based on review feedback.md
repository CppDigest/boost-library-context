# #39 - Revisit examples/vector based on review feedback [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 00:31:31 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-24 21:58:25 UTC  
> Url: https://github.com/boostorg/yap/issues/39  

>>> examples/vector.html:  
>>>>  
>>>> - return boost::yap::make_terminal(std::move(vec[n]));  
>>>>   Move is probably wrong as you're working with a reference  
>>>>   to begin with.  (Of course, it doesn't really matter for  
>>>>   double, but imagine that you have a vector<unique_ptr>  
>>>>   instead.)  
>>>>  
>>>  
>>> The move is required to force a copy; just because the reference is valid  
>>> now doesn't mean it won't dangle later.  
>>  
>>   Sure, but when using the evaluate(transform()) idiom,  
>> you're guaranteed that the references returned by transform  
>> will not be left dangling.  
>  
>  
> Sure.  But that's just in this example, and using that (admittedly  
> dominant) idiom.  I still want to reinforce in the example code how you  
> make copies of value types, especially built-in ones.  
>  
  
  Perfect-forwarding through a chain of  
transforms is also an important use case that  
needs to be explained.  I don't mind having  
the examples demonstrate copying as long as  
they are written such in a way that copying  
is really the correct behavior.  In this particular  
example, I don't think that it is.  
  
That's a really good point.  I'll revisit that example. *TODO*.
