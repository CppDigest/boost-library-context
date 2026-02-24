# #342 - `BOOST_TEST_CONTEXT` and custom loggers [Open]

> Username: jonesmz  
> Created at: 2022-06-16 00:03:55 UTC  
> Updated at: 2022-06-16 00:03:55 UTC  
> Url: https://github.com/boostorg/test/issues/342  

I have a custom logger which inherits from `boost::unit_test::unit_test_log_formatter`. The actual logging takes place via an in-house proprietary logging framework that has a complex binary format, with quite a few features, and several reader implementations used.   
  
So I'm adapting from what the `unit_test_log_formatter` interface can provide me into the in-house logger's API.  
  
One of the major deficiencies I'm running into is that our logging system has the notion of context data, just like `boost::test` does, but our context variables must have a unique name for each log message.  
  
`BOOST_TEST_CONTEXT`, on the other hand, is transformed into a single string at the point of it's capture. This means that there's no possibility to provide a name, and my only realistic mapping would be to either dump the context information into the log as arbitrarily formatted plain-text, or try to provide a name based on the `frame_id` of the context, which is not very descriptive.  
  
```cpp  
    struct context_frame {  
        context_frame( std::string const& d, int id, bool sticky )  
        : descr( d )  
        , frame_id( id )  
        , is_sticky( sticky )  
        {}  
  
        std::string descr;  
        int         frame_id;  
        bool        is_sticky;  
    };  
```  
However, that's not even possible because the `unit_test_log_formatter` interface isn't even given the context_frame, it's given only the string... So i'm left with trying to count how many context lines i've been given, and crossing my fingers that they line up.  
  
I'd really like to see `unit_test_log_formatter::log_entry_context` enhanced to take the entire `context_frame`, or `unit_test_log_formatter::entry_context_start` and `unit_Test_log_formatter::entry_context_finish` deprecated and removed, and replaced with `unit_test_log_formatter::log_entry_context` being given the entire vector of context_frames.  
  
---------------------------------------------------  
  
Further, if each context frame could hold an optional name, so that log formatters that have some meaningful thing they can do with the name have a name to do something with, that'd be great too.  
  
---------------------------------------------------  
  
Building on the above, but not completely dependent on it: `BOOST_DATA_TEST_CASE` combines all parameters to the function into a single `BOOST_TEST_CONTEXT` variable, which is not particularly helpful, as they're combined into a string right away, making it impossible to access the individual context parameters, regardless of the parameter name.  
  
```cpp  
**  
#define BOOST_DATA_TEST_CASE_PARAM(r, _, i, param)  (BOOST_PP_CAT(Arg, i) const& param)  
#define BOOST_DATA_TEST_CONTEXT(r, _, param)  << BOOST_STRINGIZE(param) << " = " << boost::test_tools::tt_detail::print_helper(param) << "; "  
  
#define BOOST_DATA_TEST_CASE_PARAMS( params )                           \  
    BOOST_PP_SEQ_ENUM(                                                  \  
        BOOST_PP_SEQ_FOR_EACH_I(BOOST_DATA_TEST_CASE_PARAM, _, params)) \  
/**/  
  
#define BOOST_DATA_TEST_CASE_IMPL(arity, F, test_name, dataset, params) \  
struct BOOST_PP_CAT(test_name, case) : public F {                       \  
    template<BOOST_PP_ENUM_PARAMS(arity, typename Arg)>                 \  
    static void test_method( BOOST_DATA_TEST_CASE_PARAMS( params ) )    \  
    {                                                                   \  
        BOOST_TEST_CONTEXT( ""                                          \  
            BOOST_PP_SEQ_FOR_EACH(BOOST_DATA_TEST_CONTEXT, _, params))  \**  
```  
  
What would be much easier to work with, both so the context variables are printed one-per-line by the plain_log_formatter, but also for custom loggers to be able to deal with the data driven test case parameters separately, is if this were done as an individual BOOST_TEST_CONTEXT macro call *PER* parameter to the data driven test function.  
  
---------------------------------------------------  
  
```cpp  
int  
boost::unit_test::framework::add_context( ::boost::unit_test::lazy_ostream const& context_descr, bool sticky )  
{  
    std::stringstream buffer;  
    context_descr( buffer );  
    int res_idx  = impl::s_frk_state().m_context_idx++;  
  
    impl::s_frk_state().m_context.push_back( state::context_frame( buffer.str(), res_idx, sticky ) );  
  
    return res_idx;  
}  
```  
  
Lastly, assuming that it's not practical to provide the entire context_frame to the logger due to backward compat issues, if I had some way to hook when context variables were created or destroyed so that my custom logger could intercept that, that would be a great alternative.  
  
Since my context variables are strictly scope based, but the context variables in boost test are only "scope based" is they have the "sticky" property, i have the additional impedance mismatch of the context for the data driven test case only being provided to some of the log statements, where i'd actually much rather have them be implicitly added (by my own logger is fine) for every log statement inside of a function.  
  
If I could hook `boost::unit_test::framework::add_context` to be notified of the creation / deletion of context variables, I could easily do that in my custom log formatter, but as far as I can tell, there is no way to hook this at this time.
