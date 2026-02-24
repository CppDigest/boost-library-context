# #61 - Add et mix-and-match policy rationale [Closed]

> Username: tzlaine  
> Created at: 2018-02-21 05:56:25 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-25 20:19:13 UTC  
> Url: https://github.com/boostorg/yap/issues/61  

>> - You say that it's fine to mix and match expressions that  
>>   are instantiated from different templates.  Why would  
>>   I want to do this?  The first thing that comes to mind  
>>   is combining two independent libraries that both use YAP,  
>>   but I suspect that that won't work out very well.  
>>   It seems a bit too easy for a transform to inadvertently  
>>   recurse into an unrelated expression tree in this case.  
>>  
>  
> I have two types, M and S (m and s are two objects of those respective  
> types).  M is matrix-like, and has m*m in its set of well-formed  
> expressions; m[x] is ill-formed for any x.  S is string-like, and has  
> s[int(2)] in its set of well-formed expressions; s*s is ill-formed.  M and  
> S are in the same library, one that I maintain.  
>  
> If I want to use these in Yap expressions, I probably want to be able to  
> write m*m and s[5], but not m[2] or s*s.  So I write two expression  
> templates with the right operations defined:  
>  
> template <...>  
> struct m_expr  
> {  
>     // ...  
>     BOOST_YAP_USER_BINARY_OPERATOR_MEMBER(times, ::m_expr)  
> };  
>  
> template <...>  
> struct s_expr  
> {  
>     // ...  
>     BOOST_YAP_USER_BINARY_OPERATOR_MEMBER(aubscript, ::s_expr)  
> };  
>  
> Now I can write a Yap expression like:  
>  
> lookup_matrix(S("my_matrix")) * some_matrix  
>  
> and transform() it how ever I like.  Requiring the two *_expr templates to  
> be unified would be weird.  
>  
  
  It seems a bit odd to have matrices and strings in  
one library, rather than matrices and vectors, but  
I get your point.  Please consider adding something  
like this to the documentation.  
  
Will do.
