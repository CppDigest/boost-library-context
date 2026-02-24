# #468 - Incorrect error message with caching_sha2_password user triggers a non-password error during handshake [Closed]

> Username: anarthal  
> Created at: 2025-04-23 16:24:50 UTC  
> Updated at: 2025-05-07 11:11:53 UTC  
> Closed at: 2025-05-07 11:11:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/468  

When a user authenticated by the `caching_sha2_password` plugin tries to login, their credentials are valid, but handshake still finishes with an error (e.g. if a `connect_params::database` names a database that does not exist), then the error packet is not correctly deserialized. It is mistaken by an `ok_packet`, tried to be deserialized as such, and fails with one of the deserialization error codes.
