# #177 - connection should be able to detect moved-from state [Open]

> Username: anarthal  
> Created at: 2023-09-25 13:16:40 UTC  
> Updated at: 2023-10-14 07:34:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/177  

Provide a function `bool connection::valid() const` or similar to detect moved-from state.

---

## Comment 1

> Username: jphz  
> Created at: 2023-10-11 09:47:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/177#issuecomment-1757286956  

I don't know when to reconnect?

---

## Comment 2

> Username: jphz  
> Created at: 2023-10-11 09:49:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/177#issuecomment-1757289090  

Is there any way for me to know that this connection failed, not due to a business failure, such as the mysql server stopping.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-10-11 21:42:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/177#issuecomment-1758588878  

There is no one-liner to determine this, but it's doable. MySQL is a little bit clunky with errors (there are a ton of them and not very well documented). I've written a `is_fatal_error` function that, given an error code, returns whether you should close the connection and reconnect (`true`) or not. It's based on checking the error code's category:  
  
```  
// Checks whether ec is a fatal error. When a fatal error occurs,  
// you should close the connection and open it again.  
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
  
When you receive an error in any operation other that the initial `connect` (e.g. `execute`, `prepare_statement` and so on), you should check whether the returned error code is fatal using this function. If it is, you should close the connection and reconnect. Otherwise, you can keep going.  
  
Recall that "reconnect", in this context, depends on the type of connection you're using:  
* If using `tcp_connection` or `unix_connection`, I recommend to perform a `close`, ignoring any generated errors, followed by a `connect`.  
* If using `tcp_ssl_connection` or any other SSL connection, you should perform the `close` ignoring errors, re-create the connection, and then `connect` again. This is a limitation on the underlying Asio stream.  
  
Please let me know if this helps.  
  
I'm currently working in a way to make this easier. Would providing the `is_fatal_error` as a library function suit your needs? Or would you prefer having some kind of state-tracking mechanism in `connection` that records when a fatal error occurs?  
  
Thanks,  
Ruben.

---

## Comment 4

> Username: jphz  
> Created at: 2023-10-14 07:34:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/177#issuecomment-1762701490  

thank you.
