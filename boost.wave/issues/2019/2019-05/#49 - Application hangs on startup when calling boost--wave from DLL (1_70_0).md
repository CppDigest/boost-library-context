# #49 - Application hangs on startup when calling boost::wave from DLL (1_70_0) [Closed]

> Username: guyv666  
> Created at: 2019-05-25 19:57:26 UTC  
> Updated at: 2020-04-28 15:03:35 UTC  
> Closed at: 2020-04-28 15:03:35 UTC  
> Url: https://github.com/boostorg/wave/issues/49  

Hi,  
  
I've integrated boost::wave (from boost v1_60_0) in a my DLL with VS2013 and that works very well. However, I recently upgraded boost to v1_70_0 and now my application hangs on startup, meaning the main() function is not even called. I've isolated the problem to boost::wave.   
  
If I my application directly uses boost::wave then it works as expected. However, if boost::wave is wrapped in a DLL then that's when the problem occurs. I tried using both the static and shared version of the boost library and they both yields the same result - i.e. it hangs my app before main() is called.   
  
I've tried isolating the problem as much as I can.    
  
<code>  
/* This token type is one of the central types used throughout the library.  It is a template parameter to some of the public classes and instances of this type are returned from the iterators. */  
  
typedef boost::wave::cpplexer::lex_token<> token_type;  
  
/* The template boost::wave::cpplexer::lex_iterator<> is the lexer type to to use as the token source for the preprocessing engine. It is  parametrized with the token type.*/  
  
typedef boost::wave::cpplexer::lex_iterator<token_type> lex_iterator_type;  
  
/* This is the resulting context type. The first template parameter should match the iterator type used during construction of the context instance (see below). It is the type of the underlying input stream. */  
  
typedef boost::wave::context<std::string::iterator, lex_iterator_type,  
    boost::wave::iteration_context_policies::load_file_to_string,  
    boost::wave::context_policies::eat_whitespace<token_type>> context_type;  
  
/* The preprocessor iterator shouldn't be constructed directly. It is generated through a wave::context<> object. This wave:context<> object is additionally used to initialize and define different parameters of the actual preprocessing (not done here). The preprocessing of the input stream is done on the fly behind the scenes during iteration over the range of context_type::iterator_type instances. */  
  
context_type ctx( instring.begin( ), instring.end( ), strFile.c_str( ) );  
  
ctx.set_language( boost::wave::support_cpp11 );  
ctx.set_language( boost::wave::enable_single_line( ctx.get_language( ) ) );  
  
/* Get the preprocessor iterators and use them to generate the token sequence. */  
  
context_type::iterator_type first_ = ctx.begin( );  
context_type::iterator_type last_ = ctx.end( );  
  
/* The input stream is preprocessed for you while iterating over the range [first, last). The dereferenced iterator returns tokens holding information about the preprocessed input stream, such as token type, token value, and position. */  
  
while( first_ != last_ )   
{  
    ++first;  
}  
</code>  
  
If I simply comment out the while() loop then my application does not hang anymore.   
  
I have to admit, I'm a little loss here. I've been investigating this issue for the last 2 days and haven' t much headway. If anyone has any suggestion, I would be really appreciated.   
  
Regards,

---

## Comment 1

> Username: guyv666  
> Created at: 2019-05-25 23:50:08 UTC  
> Url: https://github.com/boostorg/wave/issues/49#issuecomment-495956488  

I rebuilt boost 1_70_0, my DLL and my test application with VS2017 and I'm getting a different behavior. Now, the application does not hang it suddenly exits when executing the line from the above code:  
  
<code>  
context_type ctx( instring.begin( ), instring.end( ), strFile.c_str( ) );  
</code>  
  
Still investigating...

---

## Comment 2

> Username: guyv666  
> Created at: 2019-05-26 00:18:23 UTC  
> Url: https://github.com/boostorg/wave/issues/49#issuecomment-495957517  

Please dis-regards my last comment. The following line  
  
<code>  
context_type ctx( instring.begin( ), instring.end( ), strFile.c_str( ) );  
</code>  
  
was failing because of another reason that has nothing to do with the original issue. I apologize for the confusion. The original issue is still occurring ;(

---

## Comment 3

> Username: guyv666  
> Created at: 2019-05-26 02:20:46 UTC  
> Updated at: 2019-05-26 14:50:55 UTC  
> Url: https://github.com/boostorg/wave/issues/49#issuecomment-495962754  

Calling   
  
> `ctx.set_language( boost::wave::support_cpp11)`  
  
 was causing me some grief while running with VS2017. It seems that   
  
> `boost::wave::support_cpp11`  
  
 now include a new flag called "support_option_no_newline_at_end_of_file" which which causes a problem when trying to parse custom directives. In essence, the found_unknown_directive() hook is never called. I was able to isolate the problem to the function below:  
  
<code>  
    // verify that there isn't anything significant left on the line  
    template <typename ContextT, typename IteratorT>  
    bool pp_is_last_on_line(ContextT &ctx, IteratorT &it, IteratorT const &end,  
        bool call_hook = true)  
    {  
        using namespace boost::wave;  
  
        // this token gets skipped  
        if (call_hook)  
            util::impl::call_skipped_token_hook(ctx, *it);  
  
        for (++it; it != end; ++it) {  
        token_id id = token_id(*it);  
  
            if (T_CPPCOMMENT == id || T_NEWLINE == id ||  
                context_policies::util::ccomment_has_newline(*it))  
            {  
                if (call_hook)  
                    util::impl::call_skipped_token_hook(ctx, *it);  
                ++it;           // skip eol/C/C++ comment  
                return true;    // no more significant tokens on this line  
            }  
  
            if (!IS_CATEGORY(id, WhiteSpaceTokenType))  
                break;  
  
            // this token gets skipped  
            if (call_hook)  
                util::impl::call_skipped_token_hook(ctx, *it);  
        }  
        return need_no_newline_at_end_of_file(ctx.get_language());  
    }  
</code>  
  
The problem I encountered is the last line of the function above which always returns true if "support_option_no_newline_at_end_of_file" is set. With version 1_60_0, it was always returning false.   
  
Is this intended?

---

## Comment 4

> Username: hkaiser  
> Created at: 2019-05-26 14:49:30 UTC  
> Url: https://github.com/boostorg/wave/issues/49#issuecomment-496006388  

Frankly, I can't imagine that the issue you describe has anything to do with the code either in Boost or in Wave in particular (or even your application). I think this is most likely caused by some mismatch between compilation flags used for Boost, Wave, your application, or any other piece of software involved. There simply have not been any significant changes in Wave for the latest Boost release.

---

## Comment 5

> Username: jefftrull  
> Created at: 2020-01-11 03:01:04 UTC  
> Url: https://github.com/boostorg/wave/issues/49#issuecomment-573273849  

@guyv666 do you have any update on this issue? If you can furnish a minimal example I can investigate. Otherwise we should probably close it. Thank you!
