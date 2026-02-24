# #3079 - How to use correctly boost::beast::websocket::stream::async_read_some to read a framed bin message? [Closed]

> Username: GyroPower  
> Created at: 2026-02-02 20:48:01 UTC  
> Updated at: 2026-02-03 06:21:43 UTC  
> Closed at: 2026-02-03 06:21:43 UTC  
> Url: https://github.com/boostorg/beast/issues/3079  

I was testing sending a file in chunks in a websocket client with `beast::websocket::streamboost::beast::tcp_stream::async_write_some` and when the completion handler was called after being done the task it show the bytes transferred as expected (64 kilobytes) but in the server when it was expecting to receive data with `async_read_some` it get's triggered, meaning it suppose to had received the data, but nothing was written to the flat_buffer, so I do not know what I'm doing wrong or did I miss understand the docs about `async_read_some`, it shows 0 bytes transferred and size 0 for the buffer that expects to store the data, if this was already discussed would be a great help if you link me where to understand this better :) and thanks.  
  
```  
// ... setting up the websocket session in client  
// constexpr std::size_t kiloBytesToSend = 1024 * 64 defined as global var   
// method called when is some file selected to be sended  
void own_session::on_send_some_bin() {  
    
  
  std::ifstream fileToSend(this->filePathToSend, std::ios::binary);  
  
  if (!fileToSend)  
    return;  
  
  char chunkByte[kiloBytesToSend];  
  memset(chunkByte, '\0', kiloBytesToSend);  
  fileToSend.seekg(0, std::ios::end);  
  std::size_t fileSize = fileToSend.tellg();  
  fileToSend.seekg(0, std::ios::beg);  
  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "File size: " + std::to_string(fileSize));  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "Size of chunk to get: " +  
                               std::to_string(kiloBytesToSend));  
  
  fileToSend.read(chunkByte, kiloBytesToSend);  
  fileToSend.close();  
    
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "Chunk size: " + std::to_string(sizeof(chunkByte)));  
  this->ws_.binary(true);  
  
  int chunkIndex = 0;  
  int numChunks = (fileSize + (kiloBytesToSend / 2)) / kiloBytesToSend;  
  
  this->ws_.async_write_some(  
      false, net::buffer(chunkByte),  
      beast::bind_front_handler(&own_session::on_write_some_bin,  
                                this->shared_from_this(), numChunks,  
                                chunkIndex));  
}  
  
void own_session::on_write_some_bin(int numChunks, int chunkIndex,  
                                    beast::error_code ec,  
                                    std::size_t bytes_transferred) {  
  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "Num index: " + std::to_string(chunkIndex));  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "Num of chunks: " + std::to_string(numChunks));  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                           "bytes_transferred?: " +  
                               std::to_string(bytes_transferred));  
  
  if (chunkIndex < numChunks) {  
  
    std::ifstream fileToSend(this->filePathToSend, std::ios::binary);  
  
    if (!fileToSend)  
      return;  
  
    fileToSend.seekg(kiloBytesToSend * chunkIndex, std::ios::beg);  
  
    char chunkByte[kiloBytesToSend];  
    memset(chunkByte, '\0', kiloBytesToSend);  
  
    fileToSend.read(chunkByte, kiloBytesToSend);  
    fileToSend.close();  
  
    chunkIndex++;  
    bool end = chunkIndex == numChunks;  
    this->ws_.async_write_some(  
        end, net::buffer(chunkByte),  
        beast::bind_front_handler(&own_session::on_write_some_bin,  
                                  shared_from_this(), numChunks, numChunks));  
  } else if (!this->queue_.empty()) {  
    this->ws_.text(true);  
    DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeader,  
                             "Queue is not empty");  
  
    std::shared_ptr<std::string> message = this->queue_.front();  
    this->ws_.async_write(net::buffer(*message),  
                          beast::bind_front_handler(&own_session::on_write,  
                                                    this->shared_from_this()));  
  }  
}  
  
  
// -----------------------------------------------------------------------  
// server part defined for listening for incoming bin data  
// constexpr std::size_t kiloBytesToUse = 1024 * 64 defined as global var  
  
void websocket_session::read_some_binary() {  
  this->buffer_binary.consume(this->buffer_binary.size());  
  this->buffer_binary.reserve(kiloBytesToUse);  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeaders,  
                           "Expecting bin buffer in chunks");  
  
  this->ws_.async_read_some(  
      this->buffer_binary.data(),  
      beast::bind_front_handler(&websocket_session::on_read_some_binary,  
                                shared_from_this()));  
}  
  
void websocket_session::on_read_some_binary(beast::error_code ec,  
                                            std::size_t bytes_transferred) {  
  
  // Just logging to see if it got something but the buffer and the bytes_transferred are 0  
  // But it gets here which means the completion handler is trigger, so it suppose that the   
  // server received the data  
    
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeaders,  
                           "Bytes transferred: " +  
                               std::to_string(bytes_transferred));  
  DebugLog::logClassStatus(DebugLog::LOG_INFO, this->logHeaders,  
                           "Size of buffer wrote to: " +  
                               std::to_string(this->buffer_binary.size()));  
  this->buffer_binary.clear();  
  this->buffer_.clear();  
  
  // set it again to receive just text  
  this->ws_.async_read(this->buffer_,  
                       beast::bind_front_handler(&websocket_session::on_read,  
                                                 this->shared_from_this()));  
}  
```

---

## Comment 1

> Username: GyroPower  
> Created at: 2026-02-03 06:21:43 UTC  
> Url: https://github.com/boostorg/beast/issues/3079#issuecomment-3839310022  

I missed so bad that I have to prepare the writable part of the buffer, I changed to use `multi_buffer` and use `prepare` method with the desired size (64 kilobytes) for the writable buffer sequence to use, I'm sorry for so dumb question, the solution was this:  
```  
//  
this->ws_.async_read_some(  
        this->buffer_binary.prepare(kiloBytesToUse),  
        beast::bind_front_handler(&websocket_session::on_read_some_binary,  
                                  this->shared_from_this()));  
  
```  
And in the completion handler function:  
```  
// After this manipulate the bytes in the buffer with data which is the readable part   
this->buffer_binary.commit(bytes_transferred);  
```   
I'm sorry but the docs sometimes are a bit blur in getting exactly somethings but still great library.
