# #272 - ERROR An operation controlled by Boost.MySQL timed out [Closed]

> Username: jphz  
> Created at: 2024-05-23 11:05:28 UTC  
> Updated at: 2024-05-23 14:40:31 UTC  
> Closed at: 2024-05-23 14:40:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/272  

I have encountered this problem, how can I make it work properly.

---

## Comment 1

> Username: jphz  
> Created at: 2024-05-23 11:05:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126834067  

Appears when querying a large amount of data.

---

## Comment 2

> Username: jphz  
> Created at: 2024-05-23 11:18:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126857654  

```cpp  
   boost::mysql::pooled_connection connection;  
   while (true) {  
       connection = _gameDB->async_get_connection(yield[ec]);  
       if (!ec) {  
           break;  
       }  
       continue;  
   }  
···  
Is there any other way to solve it?

---

## Comment 3

> Username: anarthal  
> Created at: 2024-05-23 11:46:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126909929  

This means that the connection pool has reached its upper limit and all connections are now in use. You can solve this in two ways:  
* Increase the maximum number of concurrent connections. Please read [this section](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/connection_pool.html#mysql.connection_pool.pool_size) of the docs to learn how to do it.  
* Manage your connections more effectively. Bear in mind that a connection is in use until the `pooled_connection` object returned by `async_get_connection` is destroyed.  
  
If you post a code snippet with more info, I can provide some help on the second point.

---

## Comment 4

> Username: jphz  
> Created at: 2024-05-23 11:51:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126919072  

```cpp  
    boost::asio::spawn(_threadPool, [this, self = shared_from_this(), conn = conn->shared_from_this(),  
                                     packet = packet->shared_from_this()](boost::asio::yield_context yield) {  
        auto msgID = packet->readUInt16();  
        uint64_t sn = packet->getParam();  
        auto sql = packet->readString();  
        LOG_DEBUG("DBServer::onReqMySQLQueryBySession type:{} sn:{} sql:{}", conn->getType(), sn, sql);  
  
        POP_PACKET(sendPacket, Proto::REP_MYSQL_QUERY_BY_SESSION);  
        sendPacket->setParam(sn);  
        sendPacket->writeUInt16(msgID);  
  
        boost::system::error_code ec{};  
        boost::mysql::pooled_connection connection;  
        while (true) {  
            connection = _gameDB->async_get_connection(yield[ec]);  
            if (!ec) {  
                break;  
            }  
  
            LOG_ERROR("DBServer::onReqMySQLQueryBySession type:{} sn:{} code:{} error:{}", conn->getType(), sn,  
                      ec.value(), ec.message());  
            continue;  
        }  
  
        boost::mysql::results results{};  
        connection->async_execute(sql, results, yield[ec]);  
        if (ec) {  
            LOG_ERROR("DBServer::onReqMySQLQueryBySession type:{} sn:{} code:{} error:{}", conn->getType(), sn,  
                      ec.value(), ec.message());  
            conn->sendMsg(sendPacket);  
            return;  
        }  
  
        if (!serialize(results, sendPacket)) {  
            sendPacket->reset();  
            sendPacket->setMsgID(Proto::REP_MYSQL_QUERY_BY_SESSION);  
            sendPacket->setParam(sn);  
            sendPacket->writeUInt16(msgID);  
        }  
  
        conn->sendMsg(sendPacket);  
    });  
```

---

## Comment 5

> Username: jphz  
> Created at: 2024-05-23 11:52:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126920919  

Do need any additional processing?

---

## Comment 6

> Username: anarthal  
> Created at: 2024-05-23 12:06:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126945316  

Are the `conn->sendMsg` calls blocking?

---

## Comment 7

> Username: jphz  
> Created at: 2024-05-23 12:08:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126949250  

```cpp  
void Connection::sendMsg(Packet::Ptr packet) {  
    boost::asio::post(_stream.get_executor(),  
                      [this, self = shared_from_this(), packet = packet->shared_from_this()]() {  
                          onPack(packet);  
                      });  
}  
```

---

## Comment 8

> Username: jphz  
> Created at: 2024-05-23 12:09:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126949951  

Not blocking

---

## Comment 9

> Username: anarthal  
> Created at: 2024-05-23 12:12:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126955724  

Does `onPack` call any blocking function (like network read or write)?  
How many concurrent requests is your application receiving?

---

## Comment 10

> Username: anarthal  
> Created at: 2024-05-23 12:13:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126957997  

If you don't need any timeout, you can replace this  
```  
        while (true) {  
            connection = _gameDB->async_get_connection(yield[ec]);  
            if (!ec) {  
                break;  
            }  
  
            LOG_ERROR("DBServer::onReqMySQLQueryBySession type:{} sn:{} code:{} error:{}", conn->getType(), sn,  
                      ec.value(), ec.message());  
            continue;  
        }  
```  
  
by simply  
  
```  
// A zero timeout means "wait indefinitely until a connection is available"  
connection = _gameDB->async_get_connection(std::chrono::seconds(0), yield[ec]);  
```

---

## Comment 11

> Username: jphz  
> Created at: 2024-05-23 12:17:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126964833  

Thank you very much.

---

## Comment 12

> Username: anarthal  
> Created at: 2024-05-23 12:18:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126966337  

If the rest of your application uses blocking functions and doesn't integrate well with Asio's async model, you may consider using [this code](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/connection_pool.html#mysql.connection_pool.sync) to get a synchronous equivalent of `asnc_get_connection`.

---

## Comment 13

> Username: jphz  
> Created at: 2024-05-23 12:19:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126969605  

My code is all asynchronous, except for parsing network packets, which takes some time.

---

## Comment 14

> Username: anarthal  
> Created at: 2024-05-23 12:23:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126975620  

Then you should be fine with what you are doing. Try increasing the number of allowed connections to the server if you get too much latency.

---

## Comment 15

> Username: jphz  
> Created at: 2024-05-23 12:24:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/272#issuecomment-2126978266  

Yes, I need to increase the number of connections. The previous number of connections was too small.
