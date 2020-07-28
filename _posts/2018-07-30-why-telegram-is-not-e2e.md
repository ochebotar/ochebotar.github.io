---
layout: post
title: Why Telegram Passport is not End to End
cover-img: /assets/img/telegram.png
thumbnail-img: /assets/img/telegram.png
# gh-repo: daattali/beautiful-jekyll
# gh-badge: [star, fork, follow]
tags: [privacy, e2e, telegram, messengers]
comments: true
---
*This is a translation of the [original article](https://habr.com/post/418535/){:target="_blank"} by [Scratch](https://habr.com/users/Scratch/){:target="_blank"}.*

There is a lot of discussion about how secure the new Telegram Passport service, so lets talk about how the service encrypts your personal data and understand how is real End-To-End works.

How Telegram Passport works:

* It uses the password to encrypt locally all your personal data (name, email, passport scan, other documents).

* Encrypted data and metadata loaded into the Telegram cloud.

* When you need to log in to the service, the client downloads data from the cloud, decrypts it with a password, re-encrypts it to the public key of the service that requested the information, and sends it to service.

Lets analyse the first part, which deals with the encryption and storage of your personal data.

According to the developers understanding of End to End — Telegram cloud cannot decrypt your personal data.

Lets inspect the [encrypting algorithm](https://github.com/telegramdesktop/tdesktop/blob/a919737f6ef98b56cd7db41577ecfc269a60f444/Telegram/SourceFiles/passport/passport_encryption.cpp){:target="_blank"}. It starts from the password when it turns into an intermediate encryption key:

```cpp
bytes::vector CountPasswordHashForSecret(
        bytes::const_span salt,
        bytes::const_span password) {
    return openssl::Sha512(bytes::concatenate(
        salt,
        password,
        salt));
}
```

The random salt is taken and concatenated twice with a password and then sent through the SHA-512 hash.

We are in 2k18! One good GPU can brute force a little more than a billion SHA-512 per second. 10 GPU will handle all possible combinations of 8-digit passwords in 80 days. One hundred will be managed in a week.

There are many ways to make life difficult for those who brute force passwords on the GPU, but the developers of Telegram decided not to bother themselves with its implementation.

Further, password hash encrypts another *almost* random key, which is generated like this:

```cpp
bytes::vector GenerateSecretBytes() {
    auto result = bytes::vector(kSecretSize);
    memset_rand(result.data(), result.size());
    const auto full = ranges::accumulate(
        result,
        0ULL,
        [](uint64 sum, gsl::byte value) { return sum + uchar(value); });  
    const auto mod = (full % 255ULL);
    const auto add = 255ULL + 239 - mod;  
    auto first = (static_cast<uchar>(result[0]) + add) % 255ULL; result[0] = static_cast<gsl::byte>(first);
    return result;
}
```

It is “almost” random because Telegram developers have never heard of HMAC and AEAD and instead of using conventional tools to check the correctness of the decryption, they make sure that the remainder of the division of the byte sum of the key is equal to 239, which verified on a decryption:

```cpp
bool CheckBytesMod255(bytes::const_span bytes) {  
    const auto full = ranges::accumulate(   
        bytes,
        0ULL,
        [](uint64 sum, gsl::byte value) { return sum + uchar(value); });
    const auto mod = (full % 255ULL);  
    return (mod == 239); 
}
```

This array of bytes is not so random. It will be many false positives on a brute force. However, to calculate the amount of bytes after decoding is much simpler than HMAC, so basically this design is accelerating the brute force.

Now lets observe the data encrypting method:

```cpp
EncryptedData EncryptData(   
        bytes::const_span bytes,  
        bytes::const_span dataSecret) {  
    constexpr auto kFromPadding = kMinPadding + kAlignTo - 1;   
    constexpr auto kPaddingDelta = kMaxPadding - kFromPadding;  
    const auto randomPadding = kFromPadding   
        + (rand_value<uint32>() % kPaddingDelta);  
    const auto padding = randomPadding   
        - ((bytes.size() + randomPadding) % kAlignTo);  
    Assert(padding >= kMinPadding && padding <= kMaxPadding);   

    auto unencrypted = bytes::vector(padding + bytes.size());  
    Assert(unencrypted.size() % kAlignTo == 0);   

    unencrypted[0] = static_cast<gsl::byte>(padding);  
    memset_rand(unencrypted.data() + 1, padding - 1);  
    bytes::copy(   
        gsl::make_span(unencrypted).subspan(padding),
        bytes);
```

Data is appended from 32 to 255 random bytes. It developed to diversify
variable dataHash. This is a hash of unencrypted data mixed with random bytes.

```cpp
const auto dataHash = openssl::Sha256(unencrypted);  
const auto bytesForEncryptionKey = bytes::concatenate(   
    dataSecret,   
    dataHash);   

auto params = PrepareAesParams(bytesForEncryptionKey);  
return {
    { dataSecret.begin(), dataSecret.end() },   
    { dataHash.begin(), dataHash.end() },   
    Encrypt(unencrypted, std::move(params))  
}; 
```

The personal data encryption key is generated. It’s obtained with another SHA-512 call from the *almost* random generated key, which is concatenated with the dataHash.

### Conclusion

The cloud transmits:

* Hash from personal data mixed with random bytes.
* Password-encrypted *almost* random key.
* Salt
* Encrypted data

This is definitely not a “random noise”, there is everything, including the password-protected encryption key, which allows you to get the user data much, much faster than brute forcing all possible combinations of AES keys (2 ^ 256).

Much doubt about key validation mechanisms invented by Telegram team instead of HMAC.

An example of a brute force algorithm:

* Generate a hash from password and salt (GPU).
* Trying to decipher the key (AES-NI).
* Filter out the invalid passwords based on a bytes amount.
* Generating key-candidate for data decryption using another SHA-512 call (GPU).
* Trying to decode the first data block (AES-NI).
* In order not to waste time for full decoding and another SHA-256, you can speed up the brute force by checking the first alignment byte as well as they do:

```cpp
if (padding < kMinPadding   
        || padding > kMaxPadding   
        || padding > decrypted.size()) {
```

The first byte of the decrypted text always be “{“ or “[“, because of the JSON use.

We figured out that the encryption of personal data critically depends on the complexity of the password. All stages of a brute force perfectly accelerated by hardware, either using a GPU or AES-NI instructions.

Of course, you can set a long secure password and hope it’ll do the job. But how do you think, what percentage of the two hundred million Telegram users will make their passwords longer than eight characters?

In addition the dubious techniques of generating keys and verifying the validity of the decrypted information which do not use standard proven mechanisms, directly violate the **”Do not roll your own crypto”** principle, it becomes clear that this is not an End-to-End.

E2E is called so, because it allows you to show third parties encrypted data without fear for their safety. As you can see, this condition is not fulfilled by Telegram Passport.

Thank you for attention.

<style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#ffffff !important;background-color:#5F7FFF !important;border-radius: 5px !important;border: 1px solid transparent !important;padding: 7px 15px 7px 10px !important;font-size: 22px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#ffffff !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/kip0d"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>

