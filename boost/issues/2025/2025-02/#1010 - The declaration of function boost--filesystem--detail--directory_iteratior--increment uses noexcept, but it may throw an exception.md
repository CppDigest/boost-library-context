# #1010 - The declaration of function boost::filesystem::detail::directory_iteratior::increment uses noexcept, but it may throw an exception [Open]

> Username: SonoJiang  
> Created at: 2025-02-11 13:43:42 UTC  
> Updated at: 2025-02-11 13:43:42 UTC  
> Url: https://github.com/boostorg/boost/issues/1010  

Increment call detail::directory_iterator_increment, while detail::directory_iterator_increment may throw an exception.  
  
 directory_iterator& increment(system::error_code& ec) **noexcept**  
    {  
        detail::directory_iterator_increment(*this, &ec);  
        return *this;  
    }  
  
BOOST_FILESYSTEM_DECL  
void **directory_iterator_increment**(directory_iterator& it, system::error_code* ec)  
{  
    BOOST_ASSERT_MSG(!it.is_end(), "attempt to increment end iterator");  
  
    if (ec)  
        ec->clear();  
  
    try  
    {  
        path filename;  
        file_status file_stat, symlink_file_stat;  
        system::error_code increment_ec;  
  
        while (true)  
        {  
            increment_ec = dir_itr_increment(*it.m_imp, filename, file_stat, symlink_file_stat);  
  
            if (BOOST_UNLIKELY(!!increment_ec)) // happens if filesystem is corrupt, such as on a damaged optical disc  
            {  
                boost::intrusive_ptr< detail::dir_itr_imp > imp;  
                imp.swap(it.m_imp);  
                path error_path(imp->dir_entry.path().parent_path()); // fix ticket #5900  
                if (!ec)  
                    BOOST_FILESYSTEM_THROW(filesystem_error("boost::filesystem::directory_iterator::operator++", error_path, increment_ec));  
  
                *ec = increment_ec;  
                return;  
            }  
  
            if (it.m_imp->handle == nullptr) // eof, make end  
            {  
                it.m_imp.reset();  
                return;  
            }  
  
            const path::string_type::value_type* filename_str = filename.c_str();  
            if (!(filename_str[0] == path::dot // !(dot or dot-dot)  
                  && (filename_str[1] == static_cast< path::string_type::value_type >('\0') ||  
                      (filename_str[1] == path::dot && filename_str[2] == static_cast< path::string_type::value_type >('\0')))))  
            {  
                it.m_imp->dir_entry.replace_filename_with_status(filename, file_stat, symlink_file_stat);  
                return;  
            }  
        }  
    }  
    catch (std::bad_alloc&)  
    {  
        if (!ec)  
            throw;  
  
        it.m_imp.reset();  
        *ec = make_error_code(system::errc::not_enough_memory);  
    }  
}
