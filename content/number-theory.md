---
title: 2. Number Theory
---
Using prime factorization, any number $N$ can be represented as:
$$N = p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$$
where $p_i$ are distinct prime factors and $e_i$ are their respective powers.
## Number of Divisors
Formula:
$$\text{Count}(N) = (e_1 + 1) \times (e_2 + 1) \times \dots \times (e_k + 1)$$
> [!abstract]- Mathematical Proof
> By the Fundamental Theorem of Arithmetic, any integer $N$ can be uniquely factorized as:
$$N = p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$$
Any divisor $d$ of $N$ must also be composed of the same prime factors, but raised to some power less than or equal to the powers in $N$. Therefore, $d$ takes the form:
$$d = p_1^{a_1} \cdot p_2^{a_2} \cdots p_k^{a_k}$$
where each exponent $a_i$ must satisfy the condition $0 \le a_i \le e_i$.
For every prime factor $p_i$, you can choose its exponent $a_i$ in exactly $e_i + 1$ different ways (since you can choose $0, 1, 2, \dots, e_i$).

**Time Complexity:** $\mathcal{O}(\sqrt{N})$
**Space Complexity:** $\mathcal{O}(1)$

```cpp
long long countDivisors(long long n) {
    long long count = 1;
    
    for (long long d = 2; d * d <= n; d++) {
        if (n % d == 0) {
            int e = 0; 
            while (n % d == 0) {
                e++;
                n /= d;
            }
            count *= (e + 1);
        }
    }
    if (n > 1) {
        count *= (1 + 1); 
    }
    
    return count;
}
```

**Algorithm:**
* Handle edge cases early by returning `0` if $N \le 0$.
* Initialize a running total `count = 1`.
* Iterate through potential prime factors $d$ from $2$ up to $N/d$ (using $N/d$ instead of $\sqrt{N}$ prevents integer overflow for very large numbers).
* If $d$ divides $N$, determine its frequency (exponent $e$) by repeatedly dividing $N$ by $d$.
* Multiply the running `count` by $(e + 1)$.
* If $N > 1$ after the loop completes, the remaining value is a prime factor itself (with an exponent of $1$); multiply the final `count` by $2$ (which is $1 + 1$).

## Sum of Divisors
**Formula:**
$$\text{Sum}(N) = \left( \frac{p_1^{e_1+1}-1}{p_1-1} \right) \times \left( \frac{p_2^{e_2+1}-1}{p_2-1} \right) \times \dots \times \left( \frac{p_k^{e_k+1}-1}{p_k-1} \right)$$
> [!abstract]- Mathematical Proof
>Consider the algebraic expansion of the following product of polynomials:
$$\left(1 + p_1 + p_1^2 + \dots + p_1^{e_1}\right) \times \left(1 + p_2 + \dots + p_2^{e_2}\right) \times \dots \times \left(1 + p_k + \dots + p_k^{e_k}\right)$$
When you expand this product, every resulting term is formed by choosing exactly one power from each bracket. This means every term takes the exact form of a unique divisor $d = p_1^{a_1}p_2^{a_2}\cdots p_k^{a_k}$.
Because every possible divisor is generated exactly once, the evaluated sum of this expanded polynomial is exactly the sum of all divisors of $N$.
Notice that each bracket is a finite geometric series. The previously known formula for the sum of a finite geometric series $1 + r + r^2 + \dots + r^e$ is:
$$S = \frac{r^{e+1}-1}{r-1}$$
Substituting this known sum formula into each bracket of our product yields the final formula:
$$\text{Sum}(N) = \left( \frac{p_1^{e_1+1}-1}{p_1-1} \right) \left( \frac{p_2^{e_2+1}-1}{p_2-1} \right) \dots \left( \frac{p_k^{e_k+1}-1}{p_k-1} \right)$$

**Time Complexity:** $\mathcal{O}(\sqrt{N})$
**Space Complexity:** $\mathcal{O}(1)$

```cpp
long long divisorFunctions(long long n, bool wantSum) {
    long long count = 1;
    long long sum = 1;
    
    for (long long d = 2; d * d <= n; d++) {
        if (n % d == 0) {
            int e = 0;
            long long p_pow = d;
            while (n % d == 0) {
                e++;
                p_pow *= d;
                n /= d;
            }
            count *= (e + 1);
            sum *= (p_pow - 1) / (d - 1);
        }
    }
    if (n > 1) {
        count *= (1 + 1);
        sum *= (n * n - 1) / (n - 1); // equivalent to (n + 1)
    }
    
    return wantSum ? sum : count;
}

```

**Algorithm:**
* Find the prime factorization of $N$ using Trial Division.
* For each prime factor $p$, count its frequency (exponent $e$).
* Maintain running totals for `count` and `sum` by multiplying the respective formula terms at each prime step.

## Euler's Totient Function 
Counts the number of positive integers up to $n$ that are relatively prime (coprime) to $n$.
**Formula:**

$$\phi(n) = n \cdot \prod_{p|n} \left(1 - \frac{1}{p}\right)$$
*(Where $p$ iterates over all distinct prime factors of $n$)

> [!abstract]- Mathematical Proof
> This formula is derived using the **Principle of Inclusion-Exclusion (PIE)**.
> Let the distinct prime factors of $n$ be $p_1, p_2, \dots, p_k$. We want to find the count of integers in the set $\{1, 2, \dots, n\}$ that are coprime to $n$ (meaning they are not divisible by any of $n$'s prime factors).
>- The total number of integers in the range is $n$.
>- The number of integers divisible by a prime $p_i$ is $\frac{n}{p_i}$. We must subtract these.
>- However, numbers divisible by both $p_i$ and $p_j$ were subtracted twice. We must add them back. There are $\frac{n}{p_i p_j}$ such numbers.
>- Numbers divisible by three primes were added back too many times, so we subtract them again: $\frac{n}{p_i p_j p_m}$, and so on.
> By PIE, the number of integers coprime to $n$ is:
> $$\phi(n) = n - \sum_{i} \frac{n}{p_i} + \sum_{i < j} \frac{n}{p_i p_j} - \sum_{i < j < m} \frac{n}{p_i p_j p_m} + \dots + (-1)^k \frac{n}{p_1 p_2 \dots p_k}$$
If we factor out the common term $n$, we get:
> $$\phi(n) = n \left( 1 - \sum_{i} \frac{1}{p_i} + \sum_{i < j} \frac{1}{p_i p_j} - \dots + \frac{(-1)^k}{p_1 p_2 \dots p_k} \right)$$
This alternating sum of fractions is the exact algebraic expansion of the product:
$$\phi(n) = n \left(1 - \frac{1}{p_1}\right) \left(1 - \frac{1}{p_2}\right) \dots \left(1 - \frac{1}{p_k}\right) = n \prod_{p|n} \left(1 - \frac{1}{p}\right)$$

**Time Complexity:** $\mathcal{O}(\sqrt{N})$
**Space Complexity:** $\mathcal{O}(1)$

```cpp
int phi(int n) {
    int result = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0) n /= i;
            result -= result / i; // equivalent to result = result * (1 - 1/i)
        }
    }
    if (n > 1) {
        result -= result / n;
    }
    return result;
}

```
**Algorithm:**
* Initialize `result = n`.
* Iterate through possible prime factors from $2$ to $\sqrt{n}$.
* If $i$ divides $n$, it is a prime factor. Remove all occurrences of $i$ from $n$ to avoid counting the same factor twice.
* Multiply the `result` by $(1 - \frac{1}{i})$ (handled programmatically as `result -= result / i`).
* If $n > 1$ after the loop, the remaining $n$ is a prime factor itself; apply the formula one last time for $n$.



## GCD & LCM
The Euclidean algorithm is the standard way to find the largest number that divides two numbers.
**Formula for LCM:**
$$\text{LCM}(a, b) = \frac{a \times b}{\text{GCD}(a, b)}$$

> [!abstract]- Mathematical Proof
> Let $p_1, p_2, \dots, p_k$ represent all the distinct prime factors present in either $a$ or $b$. We can express $a$ and $b$ using prime factorization as:
>	$$a = p_1^{\alpha_1} p_2^{\alpha_2} \dots p_k^{\alpha_k}$$
$$b = p_1^{\beta_1} p_2^{\beta_2} \dots p_k^{\beta_k}$$
(If a prime factor is present in one number but not the other, its exponent is $0$).
Based on the prime factorization method:
> - The **GCD** takes the lowest power of each prime:    $$\text{GCD}(a, b) = p_1^{\min(\alpha_1, \beta_1)} \dots p_k^{\min(\alpha_k, \beta_k)}$$
> - The **LCM** takes the highest power of each prime:    $$\text{LCM}(a, b) = p_1^{\max(\alpha_1, \beta_1)} \dots p_k^{\max(\alpha_k, \beta_k)}$$
> Now, multiply the GCD and the LCM together:
$$\text{GCD}(a, b) \times \text{LCM}(a, b) = \prod_{i=1}^k p_i^{\min(\alpha_i, \beta_i) + \max(\alpha_i, \beta_i)}$$
For any two real numbers $x$ and $y$, the sum of their minimum and maximum is simply the sum of the numbers themselves: $\min(x, y) + \max(x, y) = x + y$.
Applying this logic to our exponents:
$$\text{GCD}(a, b) \times \text{LCM}(a, b) = \prod_{i=1}^k p_i^{\alpha_i + \beta_i}$$
We can split this product back into two parts:
$$\prod_{i=1}^k p_i^{\alpha_i + \beta_i} = \left( \prod_{i=1}^k p_i^{\alpha_i} \right) \times \left( \prod_{i=1}^k p_i^{\beta_i} \right) = a \times b$$
Therefore, $\text{GCD}(a, b) \times \text{LCM}(a, b) = a \times b$. Rearranging this equation isolates the LCM:
$$\text{LCM}(a, b) = \frac{a \times b}{\text{GCD}(a, b)}$$

**Time Complexity:** $\mathcal{O}(\log(\min(a, b)))$
**Space Complexity:** $\mathcal{O}(\log(\min(a, b)))$ (due to recursion stack)

```cpp
long long gcd(long long a, long long b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}

long long lcm(long long a, long long b) {
    return (a / gcd(a, b)) * b; 
}
```

**Algorithm:**
- Base case: If $b$ is $0$, the GCD is $a$.
- Otherwise, recursively call the function replacing $a$ with $b$, and $b$ with the remainder of $a$ divided by $b$ ($a \pmod b$).

## Primality Test
Standard trial division test to check if a single number is prime.  
- **Time Complexity:** $\mathcal{O}(\sqrt{n})$  
- **Space Complexity:** $\mathcal{O}(1)$

```cpp
bool isPrime(int n) {
    if(n <= 1) return false;
    if(n <= 3) return true;
    if(n % 2 == 0 || n % 3 == 0) return false;
    for(int i = 5; i * i <= n; i += 6) {
        if(n % i == 0 || n % (i + 2) == 0) return false;
    }
    return true;
}
```

**Algorithm**  
- Handle small cases (≤ 3) directly.  
- Check divisibility by 2 and 3.  
- Then test all possible divisors from 5 up to √n, stepping by 6 (covers numbers of the form 6k±1).  
- If any divisor divides `n`, it’s composite; otherwise it’s prime.

## Linear Sieve
Computes the Smallest Prime Factor (SPF) and finds primes strictly in linear time.  
- **Time Complexity:** $\mathcal{O}(n)$  
- **Space Complexity:** $\mathcal{O}(n)$

```cpp
vector<int> linearSieve(int n) {
    vector<int> lp(n + 1, 0), primes;
    for(int i = 2; i <= n; i++) {
        if(lp[i] == 0) { lp[i] = i; primes.push_back(i); }
        for(int p : primes) {
            if(p > lp[i] || 1LL * i * p > n) break;
            lp[i * p] = p;
        }
    }
    return primes;
}
```

**Algorithm**  
- Maintain an array `lp` that stores the smallest prime factor for each number.  
- Iterate `i` from 2 to `n`. If `lp[i]` is 0, `i` is prime – store its SPF and add it to the prime list.  
- For each prime `p` already found, assign `p` as the SPF of `i*p`, stopping when `p` exceeds `lp[i]` or `i*p` exceeds `n`.  
- This ensures every composite is generated exactly once.

## Sieve of Eratosthenes
Generates all prime numbers up to a given limit $N$.  
- **Time Complexity:** $\mathcal{O}(N \log \log N)$  
- **Space Complexity:** $\mathcal{O}(N)$

```cpp
vector<int> sieve(int n) {
    vector<bool> isPrime(n + 1, true);
    vector<int> primes;
    isPrime[0] = isPrime[1] = false;
    for(int i = 2; i <= n; i++) {
        if(isPrime[i]) {
            primes.push_back(i);
            for(long long j = 1LL * i * i; j <= n; j += i)
                isPrime[j] = false;
        }
    }
    return primes;
}
```

**Algorithm**  
- Start with all numbers from 2 to `n` marked as prime.  
- For each `i` that is still marked prime, add it to the list and mark all its multiples (starting from `i*i`) as non‑prime.  
- Return the list of primes.

## Segmented Sieve
Finds all prime numbers in a specific range $[L, R]$. Highly useful when $R$ is large but the difference $R-L$ is small enough to fit in memory.  
- **Time Complexity:** $\mathcal{O}((R-L+1) \log \log R + \sqrt{R} \log \log \sqrt{R})$  
- **Space Complexity:** $\mathcal{O}((R-L+1) + \sqrt{R})$

```cpp
vector<int> segmentedSieve(long long L, long long R) {
    long long lim = sqrt(R);
    vector<int> base = sieve(lim);
    vector<bool> isPrime(R - L + 1, true);
    for(long long p : base) {
        long long start = max(p * p, (L + p - 1) / p * p);
        for(long long j = start; j <= R; j += p)
            isPrime[j - L] = false;
    }
    vector<int> primes;
    for(long long i = 0; i <= R - L; i++)
        if(isPrime[i] && L + i >= 2) primes.push_back(L + i);
    return primes;
}
```

**Algorithm**  
- Generate all primes up to √R (these will be used to cross out composites).  
- Create a boolean array for the range `[L, R]` and initialise all as prime.  
- For each base prime `p`, find its first multiple within `[L, R]` and mark every multiple of `p` in that range as non‑prime.  
- Scan the range and collect the numbers still marked prime.

## Prime Factorization using SPF
Uses precomputed Smallest Prime Factors (SPF) to answer prime factorization queries almost instantly.  
- **Build Time:** $\mathcal{O}(N \log \log N)$  
- **Query Time:** $\mathcal{O}(\log x)$ per factorization  
- **Space Complexity:** $\mathcal{O}(N)$

```cpp
vector<int> spf;

void buildSPF(int n) {
    spf.resize(n + 1);
    for(int i = 0; i <= n; i++) spf[i] = i;
    for(int i = 2; i * i <= n; i++) {
        if(spf[i] == i) {
            for(int j = i * i; j <= n; j += i)
                if(spf[j] == j) spf[j] = i;
        }
    }
}

vector<pair<int,int>> factorize(int x) {
    vector<pair<int,int>> res;
    while(x > 1) {
        int p = spf[x], cnt = 0;
        while(x % p == 0) { x /= p; cnt++; }
        res.push_back({p, cnt});
    }
    return res;
}
```

**Algorithm**  
- **Build:** Initialize `spf[i] = i`. For each prime `i`, iterate over its multiples starting from `i*i` and set `spf[j] = i` if it hasn’t been set yet.  
- **Factorize:** Repeatedly extract the smallest prime factor `spf[x]`, count how many times it divides `x`, divide it out, and record the pair `(prime, exponent)`.
