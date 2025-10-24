# Implementation of ElGamal Encryption Algorithm

## AIM
To implement message encryption and decryption using the ElGamal cryptosystem.

---

## ALGORITHM
1. Select a large prime number `q` and a primitive root `g` of `q` (publicly known).  
2. The receiver selects a private key `a` and computes public key `h = g^a mod q`.  
3. The sender chooses a random number `k` and computes:
   - `p = g^k mod q`  
   - `s = h^k mod q`  
4. Each character of the message `m` is encrypted as `c = s * ord(m)`.  
5. The receiver decrypts using `m = c / (p^a mod q)`.  

---

## PROGRAM

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

long long mod_exp(long long base, long long exp, long long mod) {
    long long result = 1;
    while (exp > 0) {
        if (exp % 2 == 1)
            result = (result * base) % mod;
        exp = exp >> 1;
        base = (base * base) % mod;
    }
    return result;
}

int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}

// Generate a key coprime to q
long long gen_key(long long q) {
    long long key = rand() % (q - 2) + 2; // 2 <= key <= q-1
    while (gcd(key, q) != 1) {
        key = rand() % (q - 2) + 2;
    }
    return key;
}

void encrypt(char* msg, long long q, long long h, long long g, long long* c, long long* p_out) {
    long long k = gen_key(q); // sender's private key
    long long s = mod_exp(h, k, q);
    long long p = mod_exp(g, k, q);
    *p_out = p;
    for (int i = 0; msg[i] != '\0'; i++) {
        c[i] = s * msg[i];
    }
    printf("g^k used: %lld\n", p);
    printf("g^ak used: %lld\n", s);
}

void decrypt(long long* c, long long p, long long key, long long q, char* msg, int len) {
    long long h = mod_exp(p, key, q);
    for (int i = 0; i < len; i++) {
        msg[i] = (char)(c[i] / h);
    }
    msg[len] = '\0';
}

int main() {
    srand(time(0));
    char msg[] = "encryption";
    int len = sizeof(msg) - 1;

    printf("Original Message: %s\n", msg);

    long long q = 467;       // large prime
    long long g = 2;         // primitive root

    long long key = gen_key(q);      // receiver private key
    long long h = mod_exp(g, key, q); // receiver public key

    printf("g used: %lld\n", g);
    printf("g^a used: %lld\n", h);

    long long c[len], p;
    encrypt(msg, q, h, g, c, &p);

    char decrypted[len + 1];
    decrypt(c, p, key, q, decrypted, len);

    printf("Decrypted Message: %s\n", decrypted);

    return 0;
}
```
## OUTPUT
<img width="3839" height="1849" alt="Screenshot 2025-10-24 221835" src="https://github.com/user-attachments/assets/eaf4b76c-47d5-4493-b492-5d252ad9adcc" />

## RESULT
The ElGamal encryption algorithm has been successfully implemented and tested. The original message is correctly encrypted and decrypted, demonstrating asymmetric cryptography.

