# Hashing
A quick journey into the world of cryptographic hash functions and an attempt to generate hashes using popular hash functions like MD5, SHA1, SHA2 (224, 256, 384, 512), SHA3, PBKDF2, bcrypt, scrypt and the like.

## What is a Hash function?

  - A mathematical algorithm that transforms an **arbitrary sized input message** into a **smaller fixed length hash**.
  - The result of the hashing functions is often called by various names like **Hash, Digest, checksum, fingerprint** etc.,

## Characteristics of a good Hashing function:

  1. Should be **deterministic**.   
     Which means the hashing function **should always return the SAME hash** value for a given message consistently.
     
  2. Should be **one-way | preimage resistant**.  
     Which means it should be computationally **infeasible to reverse engineer** the hash value back to the original message.
     
  3. Should be **strongly collision free | second-preimage-resistant**  
     Which means it should be **infeasible to generate the SAME hash** from two different messages.
     Or to be precise, it should be **difficult to find two messages M1 and M2 such that H(M1) = H(M2)
     
  4. Should posses **avalanche-effect | aka diffusion in cryptographic parlance**  
     Which means **even a small change in the input** message should **result in a drastically different hash value**.
  
  5. Should be **computationally fast** to compute the hash.
  
 ## Let's rollup our sleeves and generate some *hashes (message digests)* using Java Security API.
 
 The following are the valid *Message Digest Algorithm Names* supported by the Java Security API to perform one-way hashing:
 
  | Algorithm Names |
  |---|
  | MD2 |
  | MD5 |  
  | SHA-1 |
  | SHA-224 |
  | SHA-256 |
  | SHA-384 |
  | SHA-512 |

  [Reference of the above can be found here](https://docs.oracle.com/javase/8/docs/technotes/guides/security/StandardNames.html#MessageDigest).
  
  ```java
package com.experiments;

import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class HashGenerator {

   public static String
          createDigest(String inputMessage,
                       String messageDigestAlgorithm) throws NoSuchAlgorithmException {
      MessageDigest digester = MessageDigest.getInstance(messageDigestAlgorithm);
      byte[] digestAsByteArray =
                               digester.digest(inputMessage.getBytes(StandardCharsets.UTF_8));
      return bytesToHex(digestAsByteArray);
   }

   private static String bytesToHex(byte[] bytes) {
      StringBuilder builder = new StringBuilder();
      for (byte each : bytes) {
         builder.append(String.format("%02x", each));
      }
      return builder.toString();
   }

   public static void main(String[] args) throws Exception {
      String inputMessage = "Welcome to the world of Cryptography!";
      
      String md5Algo = "MD5";
      String md5Hash = createDigest(inputMessage, md5Algo);
      System.out.println(String.format("%-10s : %-120s", md5Algo, md5Hash));
      
      String sha256Algo = "SHA-256";
      String sha256Hash = createDigest(inputMessage, sha256Algo);
      System.out.println(String.format("%-10s : %-120s", sha256Algo, sha256Hash));
      
      String sha512Algo = "SHA-512";
      String sha512Hash = createDigest(inputMessage, sha512Algo);
      System.out.println(String.format("%-10s : %-120s", sha512Algo, sha512Hash));
   }
}
```
**Output**  
>`MD5        : fafea129696d518803971f70561ef831`                                                                                     
>`SHA-256    : 3a9af738f328bfafcedfd3d1ee15c412ce4d4a21e5fff5d9f3074fe900efde1a`                                                       
>`SHA-512    : 8bbc672e5c2a28dc5bc56ccc5f0e4a3b330c60d6e54e943e00c14aab4ecf3602bae785e3c88111f08350f7934e14cac447b1f922a5aba6cf4e6f924fff2ebd60`

## Safeguarding from hash precomputation attacks like Dictionary Attacks and Rainbow Tables:

 1. Guessing the correct password (in plaintext) using the **bruteforce-attack**:
 
    The bruteforce-attack is trying all the possible combinations of alphanumeric and special characters for various password lengths.
    
    For example, It's like trying all the numbers between 000 to 999 for a 3 digit number lock in a briefcase.
 
 2. Guessing the correct password (in plaintext) using the **dictionary-attack**:
 
    The dictionary-attack is a refinement or trying a sub set of the password combinations which are very popular and those that are         derived from the user's family names, birthdates, place of origin and the like.
 
    Since the passwords to be tried are a handful compared to the bruteforce, there is a very good chance that the hacker may not guess     the correct password too.
    
    
    >###Avoiding Bruteforce / Dictionary Attacks###
    > 1. **Lock the accounts temporarily** (Don't engage in authentication at all for the next 3 hours or 24 hours) after 3 or 5 consecutive authentication failures. This would effectively make the automated hacking routines from keep on trying all the password combinations.
    > 2. **Using bigger password lengths** would increase the time taken to try all the combinations. For example, a 8 digit password (any combination of case insensitive alphanumeric, punctuations and special character allowed) would have <sup>**95**</sup>8 possible password combinations.
    
  3. Getting (Reverse Engineer) the password from the *hash* of it using **Rainbow Table attacks**:

     Rainbow Tables are one of the precomputation attacks that employ hash chaining which facilitates less storage compared to the            Dictionary attack.
    
     
  ### Adding **Salt** to the rescue:   

  Rainbow table attacks could be thwarted by adding a large sized **salt** to the password while hashing.
____  

## References

 0. [One Way Hashing!](https://www.cs.rit.edu/~ark/lectures/onewayhash/onewayhash.shtml)
 1. [Storing Salts!](https://security.stackexchange.com/questions/17421/how-to-store-salt)
 2. [Bytes to Hexadecimal conversion!](https://www.mkyong.com/java/java-how-to-convert-bytes-to-hex/)
 3. [How many password combinations!](https://math.stackexchange.com/questions/739874/how-many-possible-combinations-in-8-character-password)
