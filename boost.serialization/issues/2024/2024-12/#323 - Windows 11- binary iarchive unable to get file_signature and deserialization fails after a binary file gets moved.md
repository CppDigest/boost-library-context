# #323 - Windows 11: binary iarchive unable to get file_signature and deserialization fails after a binary file gets moved [Closed]

> Username: RLWOHIO  
> Created at: 2024-12-23 14:50:18 UTC  
> Updated at: 2024-12-27 15:53:16 UTC  
> Closed at: 2024-12-27 15:53:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/323  

I have two binary files. The fileOne.bin is serialized from a nested folder of subfolders where each subfolder contains a single text file. The file2.bin has similar file structure except each subfolder contains multiple text files. The serialization is successful for both but deserialization of file2.bin depends on whether or not it has been moved after serialization. If it has been moved (e.g., drag and drop), deserialization fails. Upon copy /b or Copy-Item, the copied file can be deserialized again successfully. Two versions of the same file (i.e., moved and copied) is identical according to fc.exe /b. In Visual Studio (17.12.3), I tracked down the deserialization error of the moved version of file2.bin to  
line 65 this->basic_binary_iarchive<Archive>::init()  
line 77 this-This() >> file_signature  
  
while for the copied version, the step above returns "serialization::archive" as the file _signature and deserialization is successful.   
  
![Image](https://github.com/user-attachments/assets/b42cd1c1-e9ea-4e4c-9d0c-8cbda2c50cda)  
![Image](https://github.com/user-attachments/assets/9da0a73a-9ee0-43d9-b876-cd2fc5d44772)  
![Image](https://github.com/user-attachments/assets/633abe39-62d0-4cfe-92ea-8dcf3676ce5a)  
  
This behavior is only seen in file2.bin.  The file1.bin always deserializes successfully, moved or not. I tried both boost 1.83 and boost 1.86 and saw the same outcome.  
  
Any idea why? Thanks much for your insight and help!

---

## Comment 1

> Username: RLWOHIO  
> Created at: 2024-12-27 15:53:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/323#issuecomment-2563823308  

There is a bug in my own code which caused the file2.bin gets loaded from a wrong place. This introduced "input stream error" exception.
