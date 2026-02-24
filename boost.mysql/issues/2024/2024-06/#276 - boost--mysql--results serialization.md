# #276 - boost::mysql::results serialization [Closed]

> Username: jphz  
> Created at: 2024-06-05 16:58:49 UTC  
> Updated at: 2024-06-06 11:59:54 UTC  
> Closed at: 2024-06-06 11:59:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/276  

Result sets are sent over the network to other processes, how can I serialize them quickly instead of traversing the result set.

---

## Comment 1

> Username: jphz  
> Created at: 2024-06-05 17:05:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2150549670  

```cpp  
#define FIELD_SERIALIZE_INT(name)                \  
    if (is_unsigned) {                           \  
        packet->writeU##name(field.as_uint64()); \  
    } else {                                     \  
        packet->write##name(field.as_int64());   \  
    }  
  
bool DBServer::serialize(const boost::mysql::results& results, River::Packet::Ptr packet) {  
    try {  
        if (!results.has_value()) {  
            return false;  
        }  
  
        packet->writeUInt32(results.rows().size());  
        for (auto rows : results.rows()) {  
            for (std::size_t i = 0; i < rows.size(); ++i) {  
                auto field = rows[i];  
                auto type = results.meta()[i].type();  
                auto is_unsigned = results.meta()[i].is_unsigned();  
                switch (type) {  
                    case boost::mysql::column_type::tinyint: {  
                        FIELD_SERIALIZE_INT(Int8);  
                        break;  
                    }  
                    case boost::mysql::column_type::year:  
                    case boost::mysql::column_type::smallint: {  
                        FIELD_SERIALIZE_INT(Int16);  
                        break;  
                    }  
                    case boost::mysql::column_type::mediumint:  
                    case boost::mysql::column_type::int_: {  
                        FIELD_SERIALIZE_INT(Int32);  
                        break;  
                    }  
                    case boost::mysql::column_type::bit:  
                    case boost::mysql::column_type::bigint: {  
                        FIELD_SERIALIZE_INT(Int64);  
                        break;  
                    }  
                    case boost::mysql::column_type::float_: {  
                        packet->writeFloat(field.as_float());  
                        break;  
                    }  
                    case boost::mysql::column_type::double_: {  
                        packet->writeDouble(field.as_double());  
                        break;  
                    }  
                    case boost::mysql::column_type::blob: {  
                        std::stringstream ss{};  
                        for (auto& blob : field.as_blob()) {  
                            ss << blob;  
                        }  
                        packet->writeString(ss.str());  
                        break;  
                    }  
                    case boost::mysql::column_type::time: {  
                        packet->writeUInt64(field.as_time().count());  
                        break;  
                    }  
                    case boost::mysql::column_type::date: {  
                        packet->writeUInt64(field.as_date().as_time_point().time_since_epoch().count());  
                        break;  
                    }  
                    case boost::mysql::column_type::timestamp:  
                    case boost::mysql::column_type::datetime: {  
                        packet->writeUInt64(field.as_datetime().as_time_point().time_since_epoch().count());  
                        break;  
                    }  
                    case boost::mysql::column_type::char_:  
                    case boost::mysql::column_type::varchar:  
                    case boost::mysql::column_type::binary:  
                    case boost::mysql::column_type::text:  
                    case boost::mysql::column_type::json:  
                    case boost::mysql::column_type::decimal:  
                    case boost::mysql::column_type::varbinary: {  
                        packet->writeString(field.as_string());  
                        break;  
                    }  
                    default: {  
                        LOG_ERROR(gLogger, "DBServer::serialize unknown type:{}", static_cast<uint16_t>(type));  
                        return false;  
                    }  
                }  
            }  
        }  
    } catch (std::exception const& e) {  
        LOG_ERROR(gLogger, "DBServer::serialize exception:{}", e.what());  
        return false;  
    }  
  
    return true;  
}  
```

---

## Comment 2

> Username: anarthal  
> Created at: 2024-06-05 20:22:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2150901432  

`results` is not intended as a type to be easily serialized (you usually convert it to a type that knows a business logic before sending it over the network). Your code looks correct. If you're happy to send all integers as `int64` or `uint64`, you can use `field_view::kind` instead of the metadata type:  
  
```cpp  
    try {  
        // Note that if your query succeeded, has_value() is always true  
        if (!results.has_value()) {  
            return false;  
        }  
  
        packet->writeUInt32(results.rows().size());  
        for (auto rows : results.rows()) {  
            for (auto field: rows) {  
                switch (field.kind()) {  
                case boost::mysql::field_kind::int64:  
                    packet->writeU64(field.as_uint64());  
                    break;  
                // insert the rest of the types here  
                }  
            }  
        }  
    }  
```

---

## Comment 3

> Username: anarthal  
> Created at: 2024-06-05 20:23:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2150903160  

What's your use case? How are values deserialized on the other end?

---

## Comment 4

> Username: jphz  
> Created at: 2024-06-06 00:53:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2151189486  

The other end reads according to the type.  
readType() ....

---

## Comment 5

> Username: jphz  
> Created at: 2024-06-06 00:58:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2151196934  

When I use it, I know what type of read it is, so I want to write the result set to the network packet one at a time and send it out instead of traversing the result set to write.

---

## Comment 6

> Username: anarthal  
> Created at: 2024-06-06 09:52:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2151873373  

I'm afraid you're out of luck - there is no such built-in functionality. You will need to traverse the results to achieve it. What serialization format are you using? Also, how are you distinguishing between integers, dates and datetimes in the receiving end?  
  
Doing this in the general case is tricky, as there are many serialization formats out there, and results contains a lot of information (some operations may generate several resultsets, resultsets contain metadata objects, there's also affected row counts, warnings, etc).

---

## Comment 7

> Username: jphz  
> Created at: 2024-06-06 10:05:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2151898239  

Well, I changed the design so that each process has its own database service，thank you.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-06-06 11:59:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/276#issuecomment-2152225839  

That might be the best. Closing this.
