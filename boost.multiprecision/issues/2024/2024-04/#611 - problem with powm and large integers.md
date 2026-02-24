# #611 - problem with powm and large integers [Closed]

> Username: uwuebu  
> Created at: 2024-04-09 10:35:29 UTC  
> Updated at: 2024-04-09 12:06:51 UTC  
> Closed at: 2024-04-09 12:06:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/611  

So im trying to create RSA encryption algorythm. I did key generation using boosts cpp_int type to create basically a key whatever length i want. it works perfect. But now that i try to encrypt some text with it i encountered a problem.  
  
Mine code to encrypt text file:  
  
```  
void encryptFile(const std::string& inputFile, const std::string& outputFile, const RSAKey& public_key) {  
    std::ifstream input(inputFile);  
    if (!input.is_open()) {  
        std::cerr << "Error opening input file for encryption.\n";  
        return;  
    }  
  
    std::ofstream output(outputFile);  
    if (!output.is_open()) {  
        std::cerr << "Error opening output file for encryption.\n";  
        input.close();  
        return;  
    }  
  
    int bytes = public_key.key_length/8;  
    std::cout << bytes << "\t bytes\n";  
    while(!input.eof()){  
        std::cout << "Loop:\n";  
        mp::cpp_int data = 0;  
        for(int i = 0; i < bytes; i++){  
            if(!input.eof()){  
                data = data << 8;//Shift bits left by 8  
                data += static_cast<uint8_t>(input.get());  
               // std::cout << i << "\tdata altered!\n";  
            }  
            else{  
                std::cout << "file ended\n";  
                break;  
            }   
        }  
        std::cout << "Created data cpp_int:\t" << data << '\n';  
        data = Encrypt(63365, public_key);  
        std::cout << "Encrypted data!\n";  
        output << data;  
        std::cout << "written to file!\n";  
    }  
  
    input.close();  
    output.close();  
}  
```  
Code to encrypt large integer:  
  
```  
mp::cpp_int Encrypt(const mp::cpp_int &data, const RSAKey& p_key) {  
    std::cout << "Entered Encrypt:\t";  
    mp::cpp_int encrypted_data = mp::powm(data, p_key.exponent, p_key.modulus);  
    std::cout << "calculation made!\n";  
    return encrypted_data;  
}  
```  
RSAKey struct:  
  
```  
struct RSAKey {  
    mp::cpp_int modulus;  
    mp::cpp_int exponent;  
    unsigned short int key_length;  
};  
```  
My program just stops working without any error when i try to calculate mp::powm() modulus is 512 bit integer, exponent is 65537, data also 512 bit integer. Most strange is that it wont work even if data is small integer, like it is in code that works:  
  
```  
mp::cpp_int e = 65537;  
  
mp::cpp_int d = mp::powm(e, fn - 2, fn);  
```  
even tho here fn is also 512 bit integer.  
  
Now that i did more testing it works if i use 128 bit integers but wont work on 256 bit integers. I feel like i did something stupid and just cant see it

---

## Comment 1

> Username: uwuebu  
> Created at: 2024-04-09 11:30:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/611#issuecomment-2044841357  

Did more testing and found out that % modulus operator does not work with some specific values that occur in my code dont really know what the issue is

---

## Comment 2

> Username: uwuebu  
> Created at: 2024-04-09 12:06:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/611#issuecomment-2044990726  

nevermind issue was on my end
