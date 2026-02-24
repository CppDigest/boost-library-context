# #53 - Improve exception handling in example code [Closed]

> Username: ohz10  
> Created at: 2017-05-29 05:32:43 UTC  
> Updated at: 2018-09-23 22:26:37 UTC  
> Closed at: 2018-08-10 20:46:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/53  

The exception handling in the documentation and example code always catches `std::exception`.  For the purposes of documenting the behavior of the library, it would be better (in my opinion) if the code in the documentation and example programs caught the specific exceptions expected.   
  
For example, rather than catch `std::exception` like this  
   
    try   
    {  
        char i = -1;  
        unsigned char j = safe_cast<unsigned char>(i);   
    }  
    catch(const std::exception&)  
    {  
    }  
  
Do this instead:  
  
    try  
    {  
        char i = -1;  
        unsigned char j = safe_cast<unsigned char>(i);  
    }  
    catch(const std::out_of_range&)  
    {  
    }  
  
Doing this will help to document what the actual expected exceptions are expected. If multiple exceptions are expected, then in my opinion, the example code and documentation code should have a catch for each possible exception.

---

## Comment 1

> Username: robertramey  
> Created at: 2017-05-29 15:41:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/53#issuecomment-304689379  

Thanks for your feedback.  I'm currently making changes required to fix bugs and rough edges as highlighted in the boost review.  
  
The error policy design as it is now isn't quite right so it's in the process of being updated.  I'll take a look at this when I make these changes.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-10 20:20:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/53#issuecomment-412195002  

The problem I see with this is that there is a wide variety of exception types which could be thrown.  The current method works fine and I don't think anything would be added by making this change so I'm not going to do it.  
  
Note that this applies to the test/example code.  There's no reason why someone who needed this facility shouldn't feel free to do it.  maybe the best would be a documentation note.
