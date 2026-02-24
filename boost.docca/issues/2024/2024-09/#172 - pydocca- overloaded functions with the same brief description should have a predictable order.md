# #172 - pydocca: overloaded functions with the same brief description should have a predictable order [Open]

> Username: anarthal  
> Created at: 2024-09-02 14:57:46 UTC  
> Updated at: 2024-11-02 16:36:43 UTC  
> Url: https://github.com/boostorg/docca/issues/172  

This case arises a lot when using `\copybrief`. I've noticed unpredictable behavior with the following functions in Boost.MySQL (slightly simplified):  
  
```cpp  
  
template <class Stream>  
class connection {  
    // -- snip --  
public:  
    /**  
     * \brief Reads a batch of rows.  
     * \details  
     * The number of rows that will be read is unspecified. If the operation represented by `st`  
     * has still rows to read, at least one will be read. If there are no more rows, or  
     * `st.should_read_rows() == false`, returns an empty `rows_view`.  
     * \n  
     * The number of rows that will be read depends on the connection's buffer size. The bigger the buffer,  
     * the greater the batch size (up to a maximum). You can set the initial buffer size in the  
     * constructor. The buffer may be  
     * grown bigger by other read operations, if required.  
     * \n  
     * The returned view points into memory owned by `*this`. It will be valid until  
     * `*this` performs the next network operation or is destroyed.  
     */  
    rows_view read_some_rows(execution_state& st, error_code& err, diagnostics& diag)  
    {  
        return impl_.run(impl_.make_params_read_some_rows(st), err, diag);  
    }  
  
    /// \copydoc read_some_rows(execution_state&,error_code&,diagnostics&)  
    rows_view read_some_rows(execution_state& st)  
    {  
        error_code err;  
        diagnostics diag;  
        rows_view res = read_some_rows(st, err, diag);  
        detail::throw_on_error_loc(err, diag, BOOST_CURRENT_LOCATION);  
        return res;  
    }  
  
    /**  
     * \brief Reads a batch of rows.  
     * \details  
     * Reads a batch of rows of unspecified size into the storage given by `output`.  
     * At most `output.size()` rows will be read. If the operation represented by `st`  
     * has still rows to read, and `output.size() > 0`, at least one row will be read.  
     * \n  
     * Returns the number of read rows.  
     * \n  
     * If there are no more rows, or `st.should_read_rows() == false`, this function is a no-op and returns  
     * zero.  
     * \n  
     * The number of rows that will be read depends on the connection's buffer size. The bigger the buffer,  
     * the greater the batch size (up to a maximum). You can set the initial buffer size in `connection`'s  
     * constructor, using \ref buffer_params::initial_read_size. The buffer may be  
     * grown bigger by other read operations, if required.  
     * \n  
     * Rows read by this function are owning objects, and don't hold any reference to  
     * the connection's internal buffers (contrary what happens with the dynamic interface's counterpart).  
     * \n  
     * The type `SpanElementType` must be the underlying row type for one of the types in the  
     * `StaticRow` parameter pack (i.e., one of the types in `underlying_row_t<StaticRow>...`).  
     * The type must match the resultset that is currently being processed by `st`. For instance,  
     * given `static_execution_state<T1, T2>`, when reading rows for the second resultset, `SpanElementType`  
     * must exactly be `underlying_row_t<T2>`. If this is not the case, a runtime error will be issued.  
     * \n  
     * This function can report schema mismatches.  
     */  
    template <class SpanElementType, class... StaticRow>  
    std::size_t read_some_rows(  
        static_execution_state<StaticRow...>& st,  
        span<SpanElementType> output,  
        error_code& err,  
        diagnostics& diag  
    )  
    {  
        return impl_.run(impl_.make_params_read_some_rows_static(st, output), err, diag);  
    }  
  
    /**  
     * \brief Reads a batch of rows.  
     * \details  
     * Reads a batch of rows of unspecified size into the storage given by `output`.  
     * At most `output.size()` rows will be read. If the operation represented by `st`  
     * has still rows to read, and `output.size() > 0`, at least one row will be read.  
     * \n  
     * Returns the number of read rows.  
     * \n  
     * If there are no more rows, or `st.should_read_rows() == false`, this function is a no-op and returns  
     * zero.  
     * \n  
     * The number of rows that will be read depends on the connection's buffer size. The bigger the buffer,  
     * the greater the batch size (up to a maximum). You can set the initial buffer size in `connection`'s  
     * constructor, using \ref buffer_params::initial_read_size. The buffer may be  
     * grown bigger by other read operations, if required.  
     * \n  
     * Rows read by this function are owning objects, and don't hold any reference to  
     * the connection's internal buffers (contrary what happens with the dynamic interface's counterpart).  
     * \n  
     * The type `SpanElementType` must be the underlying row type for one of the types in the  
     * `StaticRow` parameter pack (i.e., one of the types in `underlying_row_t<StaticRow>...`).  
     * The type must match the resultset that is currently being processed by `st`. For instance,  
     * given `static_execution_state<T1, T2>`, when reading rows for the second resultset, `SpanElementType`  
     * must exactly be `underlying_row_t<T2>`. If this is not the case, a runtime error will be issued.  
     * \n  
     * This function can report schema mismatches.  
     */  
    template <class SpanElementType, class... StaticRow>  
    std::size_t read_some_rows(static_execution_state<StaticRow...>& st, span<SpanElementType> output)  
    {  
        error_code err;  
        diagnostics diag;  
        std::size_t res = read_some_rows(st, output, err, diag);  
        detail::throw_on_error_loc(err, diag, BOOST_CURRENT_LOCATION);  
        return res;  
    }  
};  
```  
  
Interestingly, `any_connection` features functions with the same parameters and gets a different order.  
  
This is what I got:  
  
![image](https://github.com/user-attachments/assets/7d31b731-329d-4ac5-9047-77d7f3513029)  
  
I can workaround the issue by making a slight change to the templated overloads so they're placed together.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-11-02 10:23:17 UTC  
> Url: https://github.com/boostorg/docca/issues/172#issuecomment-2452946128  

@anarthal can you check if the error persists with the current develop?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-11-02 16:36:42 UTC  
> Url: https://github.com/boostorg/docca/issues/172#issuecomment-2453042401  

It does.
