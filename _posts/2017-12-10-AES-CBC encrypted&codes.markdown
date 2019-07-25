---
layout: post
title: "AES-CBC 加密算法的原理与代码实现"
date: 2017-12-10 周日
author: "Johan Niu"
header-img : "img/170827/bg.jpg"
tags:
    - 安全
    - 算法
    - Python
 
---

# AES CBC 加密算法的原理与代码实现

## 算法原理

![](http://niubencoolboy.github.io/img/post-cbc.png)


## python 代码实现

### account_encrypt.py

	#coding:utf-8
	from Crypto.Cipher import AES  # pip install pycrypto
	import binascii
	import base64
	
	class Account_encrypt:
	
		def __init__(self):
			self.__key = '1234567890abcdef'
			self.__iv = '1234567890abcdef'
	
		def encrypt(self,user):
			Bloack_Size=AES.block_size
			pad = lambda s: s + (Bloack_Size - len(s) % Bloack_Size) * chr(Bloack_Size - len(s) % Bloack_Size) 
			aes = AES.new(self.__key, AES.MODE_CBC, self.__iv)
			return base64.b64encode(aes.encrypt(pad(user)))
	
		def decrypt(self,encrypt_user):
			unpad = lambda s : s[0:-ord(s[-1])]
			aes =AES.new(self.__key,AES.MODE_CBC,self.__iv)
			return unpad(aes.decrypt(base64.b64decode(encrypt_user)))
	
	if __name__ == "__main__" :
		ae = Account_encrypt()
		username = "test"
		print ae.encrypt(username)
		print ae.decrypt(ae.encrypt(username))
		
### 模块使用方法
第一种 **不推荐**

	import account_encrypt
	ae = account_encrypt.Account_encrypt();
	ae.encrypt(username)

虽然通过`ae.__key`无法访问key与iv，但是通过`ae._Account_encrtpy__key`可以访问。

__双下划线的主要作用是用来区别与子类的成员变量


第二种 **推荐**

	from account_encrypt import Account_encrypt
	ae = Account_encrypt()
	ae.encrypt(username)
	
## C# 代码实现


	using System;
	using System.Diagnostics;
	using System.IO;
	using System.Security.Cryptography;
	using System.Text;
	
	namespace AES_CBC
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            try
	            {
	                Trace.Listeners.Clear();  //清除系统监听器 (就是输出到Console的那个)
	                Trace.Listeners.Add(new MyTraceListener()); //添加MyTraceListener实例
	                string _key = "znxckjhqiweuiq19";
	                var _iv = "o1i23hbxcb1piw32";
	                string username = "niuyuehan";  //"kevinwangxiaoning" 测试当用户名长度超过十六位时。
	                string encryptResult = AES_encrypt(username, _key, _iv);
	                Console.WriteLine("AES_encrypt function exe result:\n {0}", encryptResult);
	                string decryptResult = AES_decrypt(encryptResult, _key, _iv);
	                Console.WriteLine("AES_decrypt function exe result:\n {0}",decryptResult);
	            }
	            catch (Exception e)
	            {
	                Trace.WriteLine("异常出现： " + e.Message);
	            }
	        }
	        
	        /*
	        * Encrypt method
	        * Both Keys and IVs need to be 16 characters. 
	        */ 
	        static String AES_encrypt(String Input, String AES_Key, String AES_IV)
	        {
	            // Create encryptor
	            var aes = new RijndaelManaged();
	            aes.KeySize = 128;
	            aes.BlockSize = 128;
	            aes.Padding = PaddingMode.PKCS7;
	            aes.Key = Encoding.Default.GetBytes(AES_Key);
	            aes.IV = Encoding.Default.GetBytes(AES_IV);
	            //aes.IV = Convert.FromBase64String(AES_IV);
	            var encrypt = aes.CreateEncryptor(aes.Key, aes.IV);
	            // Encrypt Input
	            byte[] xBuff = null;
	            using (var ms = new MemoryStream())
	            {
	                // Convert from UTF-8 String to byte array, write to memory stream and encrypt, then convert to byte array
	                using (var cs = new CryptoStream(ms, encrypt, CryptoStreamMode.Write))
	                {
	                    byte[] xXml = Encoding.UTF8.GetBytes(Input);
	                    cs.Write(xXml, 0, xXml.Length);
	                }
	                xBuff = ms.ToArray();
	            }
	 
	            // Convert from byte array to base64 string then return
	            String Output = Convert.ToBase64String(xBuff);
	            return Output;
	        }
	        /*
	        * Decrypt method
	        * Both Keys and IVs need to be 16 characters. 
	        */ 
	        static String AES_decrypt(String Input, String AES_Key, String AES_IV)
	        {
	            // Create decryptor
	            RijndaelManaged aes = new RijndaelManaged();
	            aes.KeySize = 128;
	            aes.BlockSize = 128;
	            aes.Mode = CipherMode.CBC;
	            aes.Padding = PaddingMode.PKCS7;
	            //aes.Key = Convert.FromBase64String(AES_Key);
	            aes.Key = Encoding.Default.GetBytes(AES_Key);
	            aes.IV = Encoding.Default.GetBytes(AES_IV);
	            var decrypt = aes.CreateDecryptor();
	 
	            // Decrypt Input
	            byte[] xBuff = null;
	            using (var ms = new MemoryStream())
	            {
	                // Convert from base64 string to byte array, write to memory stream and decrypt, then convert to byte array.
	                using (var cs = new CryptoStream(ms, decrypt, CryptoStreamMode.Write))
	                {
	                    byte[] xXml = Convert.FromBase64String(Input);
	                    cs.Write(xXml, 0, xXml.Length);
	                }
	                xBuff = ms.ToArray();
	            }
	 
	            // Convert from byte array to UTF-8 string then return
	            String Output = Encoding.UTF8.GetString(xBuff);
	            return Output;
	        }
	    }
	}
	
## C 语言实现

	/*
	 ============================================================================
	 Name        : aes_cbc.c
	 Author      : Johan Niu
	 Version     :
	 Copyright   : Your copyright notice
	 Description : Hello World in C, Ansi-style
	 ============================================================================
	 */
	
	#include <openssl/conf.h>
	#include <openssl/evp.h>
	#include <openssl/err.h>
	#include <openssl/pem.h>
	#include <string.h>
	
	
	char *base64encode (const void *b64_encode_this, int encode_this_many_bytes){
	    BIO *b64_bio, *mem_bio;      //Declares two OpenSSL BIOs: a base64 filter and a memory BIO.
	    BUF_MEM *mem_bio_mem_ptr;    //Pointer to a "memory BIO" structure holding our base64 data.
	    b64_bio = BIO_new(BIO_f_base64());                      //Initialize our base64 filter BIO.
	    mem_bio = BIO_new(BIO_s_mem());                           //Initialize our memory sink BIO.
	    BIO_push(b64_bio, mem_bio);            //Link the BIOs by creating a filter-sink BIO chain.
	    BIO_set_flags(b64_bio, BIO_FLAGS_BASE64_NO_NL);  //No newlines every 64 characters or less.
	    BIO_write(b64_bio, b64_encode_this, encode_this_many_bytes); //Records base64 encoded data.
	    BIO_flush(b64_bio);   //Flush data.  Necessary for b64 encoding, because of pad characters.
	    BIO_get_mem_ptr(mem_bio, &mem_bio_mem_ptr);  //Store address of mem_bio's memory structure.
	    BIO_set_close(mem_bio, BIO_NOCLOSE);   //Permit access to mem_ptr after BIOs are destroyed.
	    BIO_free_all(b64_bio);  //Destroys all BIOs in chain, starting with b64 (i.e. the 1st one).
	    BUF_MEM_grow(mem_bio_mem_ptr, (*mem_bio_mem_ptr).length + 1);   //Makes space for end null.
	    (*mem_bio_mem_ptr).data[(*mem_bio_mem_ptr).length] = '\0';  //Adds null-terminator to tail.
	    return (*mem_bio_mem_ptr).data; //Returns base-64 encoded data. (See: "buf_mem_st" struct).
	}
	
	char *base64decode (const void *b64_decode_this, int decode_this_many_bytes){
	    BIO *b64_bio, *mem_bio;      //Declares two OpenSSL BIOs: a base64 filter and a memory BIO.
	    char *base64_decoded = calloc( (decode_this_many_bytes*3)/4+1, sizeof(char) ); //+1 = null.
	    b64_bio = BIO_new(BIO_f_base64());                      //Initialize our base64 filter BIO.
	    mem_bio = BIO_new(BIO_s_mem());                         //Initialize our memory source BIO.
	    BIO_write(mem_bio, b64_decode_this, decode_this_many_bytes); //Base64 data saved in source.
	    BIO_push(b64_bio, mem_bio);          //Link the BIOs by creating a filter-source BIO chain.
	    BIO_set_flags(b64_bio, BIO_FLAGS_BASE64_NO_NL);          //Don't require trailing newlines.
	    int decoded_byte_index = 0;   //Index where the next base64_decoded byte should be written.
	    while ( 0 < BIO_read(b64_bio, base64_decoded+decoded_byte_index, 1) ){ //Read byte-by-byte.
	        decoded_byte_index++; //Increment the index until read of BIO decoded data is complete.
	    } //Once we're done reading decoded data, BIO_read returns -1 even though there's no error.
	    BIO_free_all(b64_bio);  //Destroys all BIOs in chain, starting with b64 (i.e. the 1st one).
	    return base64_decoded;        //Returns base-64 decoded data with trailing null terminator.
	}
	
	int main (void)
	{
	
	  /* A 128 bit key */
	  unsigned char *key = (unsigned char *)"1234567890123456";
	
	  /* A 128 bit IV */
	  unsigned char *iv = (unsigned char *)"1234567890123456";
	
	  /* Message to be encrypted */
	  unsigned char *plaintext =
	                (unsigned char *)"niuyuehan";
	
	  /* Buffer for ciphertext. Ensure the buffer is long enough for the
	   * ciphertext which may be longer than the plaintext, dependant on the
	   * algorithm and mode
	   */
	  unsigned char ciphertext[128];
	
	  /* Buffer for the decrypted text */
	  unsigned char decryptedtext[128];
	
	  int decryptedtext_len, ciphertext_len;
	  /* Encrypt the plaintext */
	  ciphertext_len = aes_cbc_encrypt(plaintext, strlen ((char *)plaintext), key, iv,
	                            ciphertext);
	
	  /*
	  //Do something useful with the ciphertext here
	  printf("Ciphertext is:\n");
	  BIO_dump_fp (stdout, (const char *)ciphertext, ciphertext_len);
	  */
	
	  /* Decrypt the ciphertext */
	  decryptedtext_len = aes_cbc_decrypt(ciphertext, ciphertext_len, key, iv,
	    decryptedtext);
	
	  /* Add a NULL terminator. We are expecting printable text */
	  decryptedtext[decryptedtext_len] = '\0';
	
	  /* Show the decrypted text */
	  char *base64_encryted_usename = base64encode(ciphertext, ciphertext_len);
	  printf("base64_encryted_usename is: \n%s\n", base64_encryted_usename);
	
	
	  printf("Decrypted text is:\n");
	  printf("%s\n", decryptedtext);
	
	
	  return 0;
	}
	
	void handleErrors(void)
	{
	  ERR_print_errors_fp(stderr);
	  abort();
	}
	
	int aes_cbc_encrypt(unsigned char *plaintext, int plaintext_len, unsigned char *key,
	  unsigned char *iv, unsigned char *ciphertext)
	{
	  EVP_CIPHER_CTX *ctx;
	
	  int len;
	
	  int ciphertext_len;
	
	  /* Create and initialise the context */
	  if(!(ctx = EVP_CIPHER_CTX_new())) handleErrors();
	
	  /* Initialise the encryption operation. IMPORTANT - ensure you use a key
	   * and IV size appropriate for your cipher
	   * In this example we are using 256 bit AES (i.e. a 256 bit key). The
	   * IV size for *most* modes is the same as the block size. For AES this
	   * is 128 bits */
	  if(1 != EVP_EncryptInit_ex(ctx, EVP_aes_128_cbc(), NULL, key, iv))
	    handleErrors();
	
	  /* Provide the message to be encrypted, and obtain the encrypted output.
	   * EVP_EncryptUpdate can be called multiple times if necessary
	   */
	  if(1 != EVP_EncryptUpdate(ctx, ciphertext, &len, plaintext, plaintext_len))
	    handleErrors();
	  ciphertext_len = len;
	
	  /* Finalise the encryption. Further ciphertext bytes may be written at
	   * this stage.
	   */
	  if(1 != EVP_EncryptFinal_ex(ctx, ciphertext + len, &len)) handleErrors();
	  ciphertext_len += len;
	
	  /* Clean up */
	  EVP_CIPHER_CTX_free(ctx);
	
	  return ciphertext_len;
	}
	
	int aes_cbc_decrypt(unsigned char *ciphertext, int ciphertext_len, unsigned char *key,
	  unsigned char *iv, unsigned char *plaintext)
	{
	  EVP_CIPHER_CTX *ctx;
	
	  int len;
	
	  int plaintext_len;
	
	  /* Create and initialise the context */
	  if(!(ctx = EVP_CIPHER_CTX_new())) handleErrors();
	
	  /* Initialise the decryption operation. IMPORTANT - ensure you use a key
	   * and IV size appropriate for your cipher
	   * In this example we are using 256 bit AES (i.e. a 256 bit key). The
	   * IV size for *most* modes is the same as the block size. For AES this
	   * is 128 bits */
	  if(1 != EVP_DecryptInit_ex(ctx, EVP_aes_128_cbc(), NULL, key, iv))
	    handleErrors();
	
	  /* Provide the message to be decrypted, and obtain the plaintext output.
	   * EVP_DecryptUpdate can be called multiple times if necessary
	   */
	  if(1 != EVP_DecryptUpdate(ctx, plaintext, &len, ciphertext, ciphertext_len))
	    handleErrors();
	  plaintext_len = len;
	
	  /* Finalise the decryption. Further plaintext bytes may be written at
	   * this stage.
	   */
	  if(1 != EVP_DecryptFinal_ex(ctx, plaintext + len, &len)) handleErrors();
	  plaintext_len += len;
	
	  /* Clean up */
	  EVP_CIPHER_CTX_free(ctx);
	
	  return plaintext_len;
	}




