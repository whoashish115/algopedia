---
title: Binary Exponentiation
---
Binary exponentiation is an efficient algorithm for computing $a^n$ in **$O(\log n)$** time. Instead of multiplying $a$ by itself $n$ times, it repeatedly **squares the base** and **halves the exponent**. This works because every exponent can be represented in binary, so only the powers corresponding to `1` bits need to be multiplied into the answer. Since the exponent is divided by $2$ at every step, the number of operations grows logarithmically rather than linearly.
For example,

$$
13 = 1101_2 = 2^3 + 2^2 + 2^0 = 8 + 4 + 1
$$

Hence,

$$
a^{13}
= a^{8+4+1}
= a^8 \cdot a^4 \cdot a^1.
$$

Thus, binary exponentiation only multiplies the powers corresponding to the set (`1`) bits in the binary representation of the exponent.
## Implementation

```cpp
long long binpow(long long a, long long n) {
    long long res = 1;
    while (n > 0) {
        if (n & 1) res *= a;
        a *= a;
        n >>= 1;
    }
    return res;
}
```

For modular exponentiation, apply % mod after every multiplication to keep values bounded:

```cpp
long long modpow(long long a, long long n, long long mod) {
    long long res = 1;
    a %= mod;
    while (n > 0) {
        if (n & 1) res = (res * a) % mod;
        a = (a * a) % mod;
        n >>= 1;
    }
    return res;
}
```
