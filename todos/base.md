---
title: Base
---
## Theory
- **Natural** : Positive integers starting from 1: $\{1, 2, 3, \dots\}$.
- **Whole** : Natural numbers plus 0: $\{0, 1, 2, 3, \dots\}$.
- **Integers** : Whole numbers and their negatives: $\{\dots, -3, -2, -1, 0, 1, 2, 3, \dots\}$.
- **Even** : An integer divisible by 2 (i.e., of the form $2k$).
- **Odd** : An integer not divisible by 2 (i.e., of the form $2k+1$).
- **Positive** : Greater than 0.
- **Negative** : Less than 0.
- **Rational** : Can be expressed as a fraction $\frac{p}{q}$ where $p$ and $q$ are integers and $q \neq 0$.
- **Irrational** : Cannot be expressed as a fraction; decimal expansion is non‑repeating and non‑terminating (e.g., $\sqrt{2}$, $\pi$).
- **Real** : Any number that can be found on the number line (includes rational and irrational).
- **Complex** : Number of the form $a + bi$, where $a$ and $b$ are real and $i^2 = -1$.
- **Prime** : Integer $> 1$ with exactly two positive divisors: 1 and itself.
- **Composite** : Integer $> 1$ with more than two divisors.
- **Coprime (Relatively Prime)** : Two numbers whose greatest common divisor (GCD) is 1.
- **Perfect Square** : An integer that is the square of some integer: $n = k^2$.
- **Perfect** : A number equal to the sum of its proper divisors (e.g., $6 = 1+2+3$).
- **Factorial** : Product of all positive integers up to $n$: $n! = 1 \cdot 2 \cdot \dots \cdot n$, with $0! = 1$.
- **Fibonacci** : Sequence defined by $F(0)=0$, $F(1)=1$, $F(n)=F(n-1)+F(n-2)$.
- **Catalan** : Numbers $C_n = \frac{\binom{2n}{n}}{n+1}$; count binary trees, bracketings, etc.
- **Derangement** : Permutations with no fixed points: $!n = (n-1)(!(n-1)+!(n-2))$, $!0=1$, $!1=0$.
- **Armstrong (Narcissistic)** : A number equal to the sum of its own digits each raised to the power of the number of digits.
- **Automorphic** : A number whose square ends with the number itself (e.g., 5, 6, 76).
- **Palindrome** : A number that reads the same forward and backward (e.g., 121).

## Time & Space Complexity

![[big-o-complexity.png]]

### 1. Time Complexity

| Time Complexity | Typical Maximum \(n\) |
| :--- | ---: |
| $O(1)$ | Huge |
| $O(\log n)$ | Huge |
| $O(\sqrt n)$ | $10^{12}+$ |
| $O(n)$ | $10^8$ |
| $O(n\log n)$ | $10^6$ |
| $O(n\sqrt n)$ | $10^5$ |
| $O(n^2)$ | $10^4$ |
| $O(n^2\log n)$ | $5 \times 10^3$ |
| $O(n^3)$ | $500$ |
| $O(n^4)$ | $100$ |
| $O(2^n \cdot n)$ | $20$ |
| $O(n!)$ | $10$–$11$ |


> [!tip]
> Most competitive programming judges execute roughly **10⁸ simple operations per second**.

> [!info]
> See [[Big-O Cheat Sheet]] for STL complexities, amortized analysis, and common algorithm complexities.

### 2. Space Complexity

| Space Complexity | Typical Maximum Size |
| :--- | :--- |
| $O(1)$ | Constant |
| $O(\log n)$ | Huge |
| $O(\sqrt n)$ | Huge |
| $O(n)$ | Up to $10^7$–$10^8$ elements |
| $O(n\log n)$ | Usually feasible |
| $O(n\sqrt n)$ | Usually feasible |
| $O(n^2)$ | Up to $\approx 5{,}000 \times 5{,}000$ |
| $O(n^3)$ | Rarely feasible |
| $O(2^n)$ | $n \le 25$ |
| $O(n!)$ | $n \le 11$ |

> [!tip]
> Most competitive programming problems have memory limits of **256 MB** or **512 MB**.

> [!info]
> See [[Big-O Cheat Sheet]] for memory estimation, STL container overhead, recursion depth, and amortized space complexity.


## Ranges
| Category | Type | Size | Range / Precision |
|----------|------|-----:|-------------------|
| Boolean | `bool` | 1 B | `false` (0), `true` (1) |
| Character | `char` | 1 B | Implementation-defined (signed or unsigned) |
| Character | `signed char` | 1 B | `-128` to `127` |
| Character | `unsigned char` | 1 B | `0` to `255` |
| Character | `wchar_t` | 2–4 B | Implementation-defined |
| Character | `char8_t` | 1 B | `0` to `255` |
| Character | `char16_t` | 2 B | `0` to `65,535` |
| Character | `char32_t` | 4 B | `0` to `4,294,967,295` |
| Integer | `short` | 2 B | `-32,768` to `32,767` |
| Integer | `unsigned short` | 2 B | `0` to `65,535` |
| Integer | `int` | 4 B | `-2,147,483,648` to `2,147,483,647` (≈ ±2.1×10⁹) |
| Integer | `unsigned int` | 4 B | `0` to `4,294,967,295` |
| Integer | `long` | 4 B (Windows)<br>8 B (Linux/macOS) | Platform-dependent |
| Integer | `unsigned long` | 4 B (Windows)<br>8 B (Linux/macOS) | Platform-dependent |
| Integer | `long long` | 8 B | `-9,223,372,036,854,775,808` to `9,223,372,036,854,775,807` (≈ ±9.2×10¹⁸) |
| Integer | `unsigned long long` | 8 B | `0` to `18,446,744,073,709,551,615` |
| Integer | `__int128_t` (GCC/Clang) | 16 B | ≈ ±1.7×10³⁸ |
| Integer | `__uint128_t` (GCC/Clang) | 16 B | `0` to ≈3.4×10³⁸ |
| Floating Point | `float` | 4 B | ≈ ±3.4×10³⁸ (~7 digits) |
| Floating Point | `double` | 8 B | ≈ ±1.8×10³⁰⁸ (~15–16 digits) |
| Floating Point | `long double` | 8–16 B | ≈ ±1.2×10⁴⁹³² (~18–21 digits) |
| Pointer | `T*` | 4 B (32-bit)<br>8 B (64-bit) | Memory address |
| Reference | `T&` | Same as pointer | Alias to an object |
| Null Pointer | `std::nullptr_t` | Same as pointer | `nullptr` |
| Empty Type | `void` | — | No value |

## Constants

| Symbol | Value | Notes |
|:---------|:------|:------|
| $\pi$ | `3.14159265358979323846` | Circle constant |
| $e$ | `2.71828182845904523536` | Euler's number |
| $\varphi$ | `1.61803398874989484820` | Golden ratio |
| $\sqrt2$ | `1.41421356237309504880` | Geometry |
| $\sqrt3$ | `1.73205080756887729353` | Geometry |
| $\ln2$ | `0.69314718055994530942` | Natural logarithms |
| $\ln10$ | `2.30258509299404568402` | Natural logarithms |
| $\log_{10}2$ | `0.30102999566398119521` | Digit formulas |
| $\log_210$ | `3.32192809488736234787` | Bit complexity |
| $\gamma$ | `0.57721566490153286060` | Euler–Mascheroni constant (harmonic numbers) |
| $\pi^2/6$ | `1.64493406684822643647` | Number theory |
| $\zeta(3)$ | `1.20205690315959428540` | Rare math problems |

## Algebra & Sequences

| Topic | Formula |
| :--- | :--- |
| LCM | $\operatorname{lcm}(a,b)=\dfrac{a\cdot b}{\gcd(a,b)}$ |
| Number of digits (base $b$) | $\lfloor\log_b(n)\rfloor+1,\qquad b=10\text{ for decimal}$ |
| nPr | $\displaystyle P(n,r)=\frac{n!}{(n-r)!}$ |
| nCr | $\displaystyle \binom{n}{r}=\frac{n!}{r!(n-r)!}$ |
| Multiplicative nCr | $\displaystyle\binom{n}{r}=\prod_{i=1}^{r}\frac{n-r+i}{i}$ |
| Sum of first $n$ naturals | $\displaystyle\frac{n(n+1)}2$ |
| Sum of squares | $\displaystyle\frac{n(n+1)(2n+1)}6$ |
| Sum of cubes | $\displaystyle\left(\frac{n(n+1)}2\right)^2$ |
| Difference of squares | $a^2-b^2=(a-b)(a+b)$ |
| Square of sum | $(a+b)^2=a^2+2ab+b^2$ |
| Square of difference | $(a-b)^2=a^2-2ab+b^2$ |
| Cube of sum | $(a+b)^3=a^3+3a^2b+3ab^2+b^3$ |
| Cube of difference | $(a-b)^3=a^3-3a^2b+3ab^2-b^3$ |
| Difference of cubes | $a^3-b^3=(a-b)(a^2+ab+b^2)$ |
| Sum of cubes | $a^3+b^3=(a+b)(a^2-ab+b^2)$ |
| Arithmetic Progression (AP) | $a_n=a+(n-1)d,\quad S_n=\dfrac{n}{2}(2a+(n-1)d)$                                        |
| Geometric Progression (GP)  | $a_n=ar^{n-1},\quad S_n=a\dfrac{r^n-1}{r-1},\quad S_\infty=\dfrac{a}{1-r}$ ($abs(r)<1$) |
| Harmonic Number | $\displaystyle H_n=\sum_{i=1}^{n}\frac1i\approx\ln n+\gamma$ |


## Divisibility Rules

| Divisible by | Rule |
| :--- | :--- |
| $2$ | Last digit even ($0,2,4,6,8$). |
| $3$ | Sum of digits divisible by 3. |
| $4$ | Last **two** digits divisible by 4. |
| $5$ | Last digit $0$ or $5$. |
| $6$ | Divisible by both $2$ and $3$. |
| $7$ | Double the **last digit**, subtract from the **rest**. Repeat. If the result is divisible by 7 (or zero), then the original number is divisible by 7. |
| $8$ | Last **three** digits divisible by 8. |
| $9$ | Sum of digits divisible by 9. |
| $10$ | Last digit $0$. |
| $11$ | (odd pos sum − even pos sum) is $0$ or multiple of $11$. |
| $12$ | Divisible by both $3$ and $4$. |
| $13$ | $4 \times$ last digit, **add** to the rest. Repeat. |
| $14$ | Divisible by both $2$ and $7$. |
| $15$ | Divisible by both $3$ and $5$. |
| $16$ | Last **four** digits divisible by 16. |
| $17$ | $5 \times$ last digit, **subtract** from the rest. Repeat. |
| $19$ | $2 \times$ last digit, **add** to the rest. Repeat. |
| $25$ | Last two digits divisible by 25 ($00,25,50,75$). |
| $125$ | Last three digits divisible by 125. |

## Bit Manipulation

| Operation | Formula / Code |
| :--- | :--- |
| Check $k$th bit (0‑indexed) | `(n >> k) & 1` |
| Set $k$th bit | `n OR (1 << k)` |
| Clear $k$th bit | `n & ~(1 << k)` |
| Toggle $k$th bit | `n ^ (1 << k)` |
| Turn off rightmost $1$ | `n & (n - 1)` |
| Isolate rightmost $1$ | `n & -n` |
| Check power of $2$ | `n > 0 && (n & (n - 1)) == 0` |
| Count set bits | `__builtin_popcount(n)` (C/C++) or `popcount` (C++20) |
| Reverse bits (32‑bit) | `std::bit_reverse (C++23)` / manual loop |
| Absolute value (no branch) | `(n ^ (n >> 31)) - (n >> 31)` |
| XOR $1$ to $N$ | $N \bmod 4 = 0 \to N,\; 1 \to 1,\; 2 \to N+1,\; 3 \to 0$ |
| XOR $L$ to $R$ | $\text{xor\_1\_to\_N}(R) \;\oplus\; \text{xor\_1\_to\_N}(L-1)$ |
| Gray code | `n ^ (n >> 1)` |

## Geometry

| Topic | Formula |
| :--- | :--- |
| Euclidean distance | $\sqrt{(x_2-x_1)^2 + (y_2-y_1)^2}$ |
| Manhattan distance | $\lvert x_2-x_1 \rvert + \lvert y_2-y_1 \rvert$ |
| Chebyshev distance | $\max(\lvert x_2-x_1 \rvert,\; \lvert y_2-y_1 \rvert)$ |
| Triangle area (coords) | $\dfrac{\left\lvert x_1(y_2-y_3) + x_2(y_3-y_1) + x_3(y_1-y_2) \right\rvert}{2}$ |
| Triangle area (Heron's) | $s = \dfrac{a+b+c}{2},\; \text{Area} = \sqrt{s(s-a)(s-b)(s-c)}$ |
| Collinearity (determinant) | $x_1(y_2-y_3) + x_2(y_3-y_1) + x_3(y_1-y_2) = 0$ |
| Point‑to‑line distance (infinite line) | $\dfrac{\left\lvert (y_2-y_1)x_0 - (x_2-x_1)y_0 + x_2 y_1 - y_2 x_1 \right\rvert}{\text{dist}(P_1,P_2)}$ |
| Point‑to‑segment distance | Let $\vec{AB}=B-A$, $\vec{AP}=P-A$. If $\vec{AP}\cdot\vec{AB} \le 0 \to \text{dist}(A,P)$; else if $\vec{AP}\cdot\vec{AB} \ge \vec{AB}\cdot\vec{AB} \to \text{dist}(B,P)$; else perpendicular distance to line. |
| Rotate vector $(x,y)$ by $\theta$ | $(x\cos\theta - y\sin\theta,\; x\sin\theta + y\cos\theta)$ |
| Dot product (2D) | $a_x b_x + a_y b_y$ |
| Cross product (2D scalar) | $a_x b_y - a_y b_x$ |
| Angle between vectors | $\operatorname{atan2}\bigl(\lvert \text{cross} \rvert,\; \text{dot}\bigr)$ or $\cos\theta = \dfrac{\text{dot}}{\lVert a \rVert \lVert b \rVert}$ |
| Projection of point onto line (through $A$, direction $\vec{AB}$) | $A + \dfrac{\vec{AP}\cdot\vec{AB}}{\vec{AB}\cdot\vec{AB}} \cdot \vec{AB}$ |
| Reflection of point over that line | $2 \times \text{projection} - P$ |
| Midpoint of segment | $\left( \dfrac{x_1+x_2}{2},\; \dfrac{y_1+y_2}{2} \right)$ |
| Centroid of triangle | $\left( \dfrac{x_1+x_2+x_3}{3},\; \dfrac{y_1+y_2+y_3}{3} \right)$ |
| Polygon area (Shoelace) | $\dfrac12 \left\lvert \displaystyle\sum_{i} (x_i y_{i+1} - x_{i+1} y_i) \right\rvert$ |
| Pick's Theorem (lattice polygon) | $\text{Area} = I + \dfrac{B}{2} - 1$ ($I$ = interior lattice points, $B$ = boundary lattice points) |
| Circle‑line intersection | Solve $\lVert P_0 + tD - C \rVert^2 = r^2$; use `hypot` for distance. |
| Distance between parallel lines $ax+by+c_1=0$ and $ax+by+c_2=0$ | $\dfrac{\lvert c_2 - c_1 \rvert}{\sqrt{a^2 + b^2}}$ |
| Area of parallelogram from vectors $u,v$ | $\lvert u_x v_y - u_y v_x \rvert$ |
| Circumference of circle | $2\pi r$ |
| Area of circle | $\pi r^2$ |
## Modular Arithmetic

| Topic                                       | Formula                                                                                                                                                                                                       |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Basic Addition                              | $(a + b) \bmod m = (a \bmod m + b \bmod m) \bmod m$                                                                                                                                                           |
| Basic Subtraction                           | $(a - b) \bmod m = (a \bmod m - b \bmod m + m) \bmod m$                                                                                                                                                       |
| Basic Multiplication                        | $(a \cdot b) \bmod m = (a \bmod m) \cdot (b \bmod m) \bmod m$                                                                                                                                                 |
| Division (needs inverse)                    | $(a / b) \bmod m = (a \bmod m) \cdot \operatorname{inv}(b) \bmod m$ (valid iff $\gcd(b,m)=1$)                                                                                                                 |
| Negative Modulo (C++ trick)                 | `long long norm(long long x) { x %= m; if (x < 0) x += m; return x; }`                                                                                                                                        |
| Modular Inverse (Prime Mod – Fermat)        | $\operatorname{inv}(a) \equiv a^{\,m-2} \pmod m$ (requires $m$ prime)                                                                                                                                         |
| Modular Inverse (Any Mod – Extended Euclid) | Solve $a x + m y = 1$ $\Longrightarrow$ $\operatorname{inv}(a) = (x \bmod m + m) \bmod m$                                                                                                                     |
| nCr mod Prime (factorial form)              | $\dbinom{n}{r} \equiv \text{fact}[n] \cdot \text{invfact}[r] \cdot \text{invfact}[n-r] \pmod m$                                                                                                               |
| Handling Giant Exponents (Euler's Theorem)  | If $\gcd(a,m)=1$, then $a^b \bmod m = a^{\,b \bmod \varphi(m)} \bmod m$                                                                                                                                       |
| Handling Giant Exponents (General case)     | If $\gcd(a,m) \neq 1$ and $b \ge \log_2(m)$, use $a^{\,b \bmod \varphi(m) + \varphi(m)} \bmod m$                                                                                                              |
| CRT (merge two congruences)                 | Merge $x \equiv a_1 \pmod{m_1}$ and $x \equiv a_2 \pmod{m_2}$ using: <br> $t = (a_2 - a_1) \cdot \operatorname{inv}(m_1, m_2) \bmod m_2$, <br> $x = a_1 + m_1 \cdot t$, new modulus $= m_1 m_2$ (if coprime). |
| Primitive Root                              | A number $g$ whose powers generate all units modulo $n$. Exists only for $n = 1,2,4,p^k,2p^k$.                                                                                                                |
| Use `__int128` for multiplication           | When $m$ is close to $10^{18}$, do `(__int128)a * b % m` to avoid overflow.                                                                                                                                   |

