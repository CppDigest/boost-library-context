# #253 - Detect fatal errors [Closed]

> Username: anarthal  
> Created at: 2024-04-29 16:34:49 UTC  
> Updated at: 2024-04-30 14:17:18 UTC  
> Closed at: 2024-04-30 14:17:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/253  

When issuing operations on connections, two kind of errors may arise:  
  
* Recoverable errors (e.g. a SQL query failed). Operations following a recoverable error can succeed without reconnecting the connection.  
* Fatal errors (e.g. packet mismatch). After such an error happens, the connection is left in an unknown state, and should be reconnected.  
  
We should detect such errors and provide a way for the user to check them. For instance:  
  
```  
bool is_fatal_error(boost::mysql::error_code ec) noexcept  
{  
    // If there is no failure, it's not fatal  
    if (!ec)  
        return false;  
  
    // Retrieve the error category  
    const auto& cat = ec.category();  
  
    if (cat == boost::mysql::get_common_server_category())  
    {  
        // Server errors may or may not be fatal. MySQL defines a ton of different errors.  
        // After some research, these are the ones I'd recommend to consider fatal  
        auto code = static_cast<boost::mysql::common_server_errc>(ec.value());  
        switch (code)  
        {  
        // Diferent flavors of communication errors. These usually indicate that the connection  
        // has been left in an unspecified state, and the safest is to reconnect it.  
        case boost::mysql::common_server_errc::er_unknown_com_error:  
        case boost::mysql::common_server_errc::er_aborting_connection:  
        case boost::mysql::common_server_errc::er_net_packet_too_large:  
        case boost::mysql::common_server_errc::er_net_read_error_from_pipe:  
        case boost::mysql::common_server_errc::er_net_fcntl_error:  
        case boost::mysql::common_server_errc::er_net_packets_out_of_order:  
        case boost::mysql::common_server_errc::er_net_uncompress_error:  
        case boost::mysql::common_server_errc::er_net_read_error:  
        case boost::mysql::common_server_errc::er_net_read_interrupted:  
        case boost::mysql::common_server_errc::er_net_error_on_write:  
        case boost::mysql::common_server_errc::er_net_write_interrupted:  
        case boost::mysql::common_server_errc::er_malformed_packet:  
        // This one indicates that you are preparing statements dynamically and  
        // never calling statement::close or connection::reset_connection.  
        // Restarting the connection will clean up any leaked statements.  
        // It's recommended that you design your code so this never happens.  
        // But better safe than sorry.  
        case boost::mysql::common_server_errc::er_max_prepared_stmt_count_reached: return true;  
        default: return false;  
        }  
    }  
    else if (cat == boost::mysql::get_mysql_server_category() || cat == boost::mysql::get_mariadb_server_category())  
    {  
        // This is a MySQL-specific or a MariaDB specific error. They are all  
        // considered non-fatal. If you're working with a specific DB system,  
        // you can comment the one you're not using  
        return false;  
    }  
    else if (ec == boost::mysql::client_errc::wrong_num_params)  
    {  
        // Errors in the boost::mysql::get_client_category() are all fatal  
        // except for this one.  
        return false;  
    }  
    else  
    {  
        // Errors in any all category are considered fatal. They will usually  
        // be asio or SSL related errors. The only option here is to reconnect.  
        return true;  
    }  
}  
```
