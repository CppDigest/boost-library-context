# #1048 - Consider message::reset [Open]

> Username: vinniefalco  
> Created at: 2018-02-27 16:21:22 UTC  
> Updated at: 2019-04-19 03:00:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1048  

This function could be used in the message-oriented `read` overloads to clear the message before use, eliminating a common class of user errors.  
  
The signature could accept a variable argument list, with this implementation:  
```  
template<bool isRequest, class Body, class Fields>  
template<class... Args>  
void  
message<isRequest, Body, Fields>::  
reset(Args&&... args)  
{  
    // This means you are trying to call reset() on a message that is not default constructible  
    static_assert(sizeof...(args)>0 || std::is_default_constructible<message>::value,  
        "DefaultConstructible requirements not met");  
    // This means you did not pass the correct parameters to construct a message  
    static_assert(sizeof...(args)==0 || std::is_constructible<message, Args...>::value,  
        "Constructor argument list mismatch");  
    // reset may only be used on messages that are MoveAssignable  
    static_assert(std::is_move_assignable<message>::value,  
        "MoveAssignable requirements not met");  
    message m(std::forward<Args>(args...));  
    *this = std::move(m);  
}  
```  
  
The `read` overloads would need to be modified to require messages that are **DefaultConstructible**, or changed to accept a list of constructor arguments.

---

## Comment 1

> Username: djarek  
> Created at: 2018-02-27 21:08:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1048#issuecomment-369026542  

MoveAssignable prevents the use of references, which is a fairly sensible thing to have in a Body, in some cases.  
  
One idea: if body is not MoveAssignable, but is NothrowMoveConstructible (or Swappable?), we could use that along with `std::aligned_storage` to emulate move assignment.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1048#issuecomment-384003564  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-24 22:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1048#issuecomment-384096097  

>MoveAssignable prevents the use of references, which is a fairly sensible thing to have in a Body, in some cases.  
  
I'm okay with `reset` not working for all instances of `message`.
