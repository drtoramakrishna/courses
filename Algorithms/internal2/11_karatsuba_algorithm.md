# Chapter 11: Integer Multiplication using Karatsuba Algorithm

## 🎯 Learning Objectives
- Understand integer multiplication complexity
- Master Karatsuba's divide-and-conquer approach
- Analyze recurrence relations
- Implement efficient large integer multiplication
- Compare with grade-school algorithm

---

## 11.1 The Integer Multiplication Problem

### 📚 **Problem Definition**

**Input:** Two n-digit integers x and y

**Output:** Product x × y

**Question:** What is the time complexity?

### 🔑 **Grade-School Algorithm**

**Method:** Multiply each digit of y with all digits of x, then add

**Example:** 1234 × 5678

```
      1234
    × 5678
    ------
      9872  (1234 × 8)
     8638   (1234 × 7, shifted)
    6170    (1234 × 6, shifted)
   6170     (1234 × 5, shifted)
   -------
   7006652
```

**Complexity:**
- **Multiplications:** n × n = n² single-digit multiplications
- **Additions:** n additions of numbers up to 2n digits = O(n²)
- **Total:** O(n²)

### 📊 **Representation**

**Decimal:** x = x_{n-1}...x_1 x_0

**Value:** x = Σ x_i × 10^i (base 10)

**General base b:** x = Σ x_i × b^i

**For simplicity:** Use base 10 in examples, analysis holds for any base

---

## 11.2 Divide-and-Conquer Approach

### 📚 **Naive Recursive Method**

**Split each number in half:**

For n-digit numbers x and y:
```
x = a × 10^(n/2) + b
y = c × 10^(n/2) + d
```

where a, b, c, d are n/2 digit numbers

**Example:** n = 4
```
x = 1234 = 12 × 10² + 34
y = 5678 = 56 × 10² + 78

a = 12, b = 34
c = 56, d = 78
```

### 🔑 **Computing Product**

```
x × y = (a × 10^(n/2) + b) × (c × 10^(n/2) + d)
      = a×c × 10^n + (a×d + b×c) × 10^(n/2) + b×d
```

**Four multiplications:**
1. a × c
2. a × d
3. b × c
4. b × d

**Additions and shifts:** O(n)

### 📚 **Recurrence Relation**

```
T(n) = 4T(n/2) + O(n)
```

**Solving with Master Theorem:**
- a = 4, b = 2, f(n) = O(n)
- n^(log_b a) = n^(log_2 4) = n²
- f(n) = O(n) is smaller than n²

**Case 1 applies:** T(n) = Θ(n²)

**No improvement over grade-school!** 😞

---

## 11.3 Karatsuba's Insight

### 📚 **Key Observation**

**Notice:** a×d + b×c can be computed differently!

```
a×d + b×c = (a + b)(c + d) - a×c - b×d
```

**We already compute a×c and b×d!**

**Trade:** One extra multiplication for three additions

### 🔑 **Karatsuba's Algorithm**

**Three multiplications instead of four:**

1. **P₁ = a × c**
2. **P₂ = b × d**  
3. **P₃ = (a + b) × (c + d)**

**Then:**
```
x × y = P₁ × 10^n + (P₃ - P₁ - P₂) × 10^(n/2) + P₂
```

### ✅ **Verification**

```
P₃ - P₁ - P₂ = (a + b)(c + d) - a×c - b×d
              = a×c + a×d + b×c + b×d - a×c - b×d
              = a×d + b×c  ✓
```

**Therefore:**
```
P₁ × 10^n + (P₃ - P₁ - P₂) × 10^(n/2) + P₂
= a×c × 10^n + (a×d + b×c) × 10^(n/2) + b×d  ✓
```

---

## 11.4 Karatsuba Algorithm Details

### 📚 **Algorithm**

```
Karatsuba(x, y, n):
  // Base case
  If n ≤ 1:
    Return x × y  (single-digit multiplication)
  
  // Split numbers
  m = ⌈n/2⌉
  a = ⌊x / 10^m⌋
  b = x mod 10^m
  c = ⌊y / 10^m⌋
  d = y mod 10^m
  
  // Three recursive multiplications
  P₁ = Karatsuba(a, c, m)
  P₂ = Karatsuba(b, d, m)
  P₃ = Karatsuba(a + b, c + d, m)
  
  // Combine
  Return P₁ × 10^(2m) + (P₃ - P₁ - P₂) × 10^m + P₂
```

### 💻 **C Implementation**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

#define MAX_DIGITS 1000

// Represent large integer as string of digits
typedef struct {
    char digits[MAX_DIGITS];
    int length;
} BigInt;

// Convert string to BigInt
BigInt str_to_bigint(const char *str) {
    BigInt num;
    num.length = strlen(str);
    
    for (int i = 0; i < num.length; i++) {
        num.digits[i] = str[num.length - 1 - i] - '0';  // Reverse for easier indexing
    }
    
    return num;
}

// Print BigInt
void print_bigint(BigInt num) {
    for (int i = num.length - 1; i >= 0; i--) {
        printf("%d", num.digits[i]);
    }
}

// Add two BigInts
BigInt add_bigint(BigInt a, BigInt b) {
    BigInt result;
    int max_len = (a.length > b.length) ? a.length : b.length;
    int carry = 0;
    
    for (int i = 0; i < max_len || carry; i++) {
        int sum = carry;
        if (i < a.length) sum += a.digits[i];
        if (i < b.length) sum += b.digits[i];
        
        result.digits[i] = sum % 10;
        carry = sum / 10;
    }
    
    result.length = max_len + (carry ? 1 : 0);
    return result;
}

// Subtract two BigInts (assume a >= b)
BigInt subtract_bigint(BigInt a, BigInt b) {
    BigInt result;
    int borrow = 0;
    
    for (int i = 0; i < a.length; i++) {
        int diff = a.digits[i] - borrow;
        if (i < b.length) diff -= b.digits[i];
        
        if (diff < 0) {
            diff += 10;
            borrow = 1;
        } else {
            borrow = 0;
        }
        
        result.digits[i] = diff;
    }
    
    // Remove leading zeros
    result.length = a.length;
    while (result.length > 1 && result.digits[result.length - 1] == 0) {
        result.length--;
    }
    
    return result;
}

// Multiply BigInt by 10^k (left shift)
BigInt shift_left(BigInt num, int k) {
    BigInt result;
    
    // Shift digits
    for (int i = 0; i < k; i++) {
        result.digits[i] = 0;
    }
    for (int i = 0; i < num.length; i++) {
        result.digits[i + k] = num.digits[i];
    }
    
    result.length = num.length + k;
    return result;
}

// Split BigInt at position m
void split_bigint(BigInt num, int m, BigInt *high, BigInt *low) {
    // Low part: digits[0..m-1]
    low->length = (m < num.length) ? m : num.length;
    for (int i = 0; i < low->length; i++) {
        low->digits[i] = num.digits[i];
    }
    
    // High part: digits[m..]
    if (num.length > m) {
        high->length = num.length - m;
        for (int i = 0; i < high->length; i++) {
            high->digits[i] = num.digits[i + m];
        }
    } else {
        high->length = 1;
        high->digits[0] = 0;
    }
}

// Grade-school multiplication for small numbers
BigInt multiply_simple(BigInt x, BigInt y) {
    BigInt result;
    memset(result.digits, 0, sizeof(result.digits));
    result.length = x.length + y.length;
    
    for (int i = 0; i < x.length; i++) {
        int carry = 0;
        for (int j = 0; j < y.length || carry; j++) {
            int prod = result.digits[i + j] + carry;
            if (j < y.length) {
                prod += x.digits[i] * y.digits[j];
            }
            result.digits[i + j] = prod % 10;
            carry = prod / 10;
        }
    }
    
    // Remove leading zeros
    while (result.length > 1 && result.digits[result.length - 1] == 0) {
        result.length--;
    }
    
    return result;
}

// Karatsuba multiplication
int karatsuba_calls = 0;  // Track recursive calls

BigInt karatsuba(BigInt x, BigInt y, int depth) {
    karatsuba_calls++;
    
    // Base case: small numbers
    if (x.length <= 2 || y.length <= 2) {
        printf("%*sBase case: multiply directly\n", depth * 2, "");
        return multiply_simple(x, y);
    }
    
    printf("%*sKaratsuba(%d digits × %d digits)\n", 
           depth * 2, "", x.length, y.length);
    
    // Split at middle
    int m = (x.length > y.length ? x.length : y.length) / 2;
    
    BigInt a, b, c, d;
    split_bigint(x, m, &a, &b);
    split_bigint(y, m, &c, &d);
    
    printf("%*sSplit x into high=%d digits, low=%d digits\n", 
           depth * 2, "", a.length, b.length);
    printf("%*sSplit y into high=%d digits, low=%d digits\n", 
           depth * 2, "", c.length, d.length);
    
    // Three recursive multiplications
    printf("%*sP₁ = high(x) × high(y)\n", depth * 2, "");
    BigInt P1 = karatsuba(a, c, depth + 1);
    
    printf("%*sP₂ = low(x) × low(y)\n", depth * 2, "");
    BigInt P2 = karatsuba(b, d, depth + 1);
    
    printf("%*sP₃ = (high(x) + low(x)) × (high(y) + low(y))\n", depth * 2, "");
    BigInt sum_x = add_bigint(a, b);
    BigInt sum_y = add_bigint(c, d);
    BigInt P3 = karatsuba(sum_x, sum_y, depth + 1);
    
    // Combine: P₁ × 10^(2m) + (P₃ - P₁ - P₂) × 10^m + P₂
    printf("%*sCombining results\n", depth * 2, "");
    
    BigInt middle = subtract_bigint(P3, add_bigint(P1, P2));
    
    BigInt term1 = shift_left(P1, 2 * m);
    BigInt term2 = shift_left(middle, m);
    
    BigInt result = add_bigint(add_bigint(term1, term2), P2);
    
    return result;
}

// Wrapper function
BigInt multiply_karatsuba(BigInt x, BigInt y) {
    printf("\n=== Karatsuba Multiplication ===\n\n");
    
    printf("x = ");
    print_bigint(x);
    printf(" (%d digits)\n", x.length);
    
    printf("y = ");
    print_bigint(y);
    printf(" (%d digits)\n\n", y.length);
    
    karatsuba_calls = 0;
    BigInt result = karatsuba(x, y, 0);
    
    printf("\n=== Result ===\n");
    printf("x × y = ");
    print_bigint(result);
    printf("\n");
    printf("Total recursive calls: %d\n", karatsuba_calls);
    
    return result;
}

// Example usage
int main() {
    // Example 1: Small numbers
    printf("Example 1: Small multiplication\n");
    printf("================================\n");
    
    BigInt x1 = str_to_bigint("1234");
    BigInt y1 = str_to_bigint("5678");
    
    BigInt result1 = multiply_karatsuba(x1, y1);
    
    printf("\nVerification: 1234 × 5678 = 7006652\n");
    
    printf("\n\n");
    
    // Example 2: Larger numbers
    printf("Example 2: Larger multiplication\n");
    printf("================================\n");
    
    BigInt x2 = str_to_bigint("123456789");
    BigInt y2 = str_to_bigint("987654321");
    
    BigInt result2 = multiply_karatsuba(x2, y2);
    
    printf("\nVerification: 123456789 × 987654321 = 121932631112635269\n");
    
    return 0;
}
```

### 📊 **Example Output**

```
Example 1: Small multiplication
================================

=== Karatsuba Multiplication ===

x = 1234 (4 digits)
y = 5678 (4 digits)

Karatsuba(4 digits × 4 digits)
Split x into high=2 digits, low=2 digits
Split y into high=2 digits, low=2 digits
P₁ = high(x) × high(y)
  Base case: multiply directly
P₂ = low(x) × low(y)
  Base case: multiply directly
P₃ = (high(x) + low(x)) × (high(y) + low(y))
  Base case: multiply directly
Combining results

=== Result ===
x × y = 7006652
Total recursive calls: 4

Verification: 1234 × 5678 = 7006652
```

---

## 11.5 Complexity Analysis

### 📚 **Recurrence Relation**

```
T(n) = 3T(n/2) + O(n)
```

**Where:**
- 3T(n/2): Three recursive multiplications
- O(n): Additions, subtractions, shifts

### 🔑 **Master Theorem**

**Compare:** T(n) = aT(n/b) + f(n)
- a = 3, b = 2, f(n) = O(n)
- n^(log_b a) = n^(log_2 3) ≈ n^1.585

**Check:** f(n) = O(n) vs n^1.585
- f(n) is polynomially smaller

**Case 1 applies:**
```
T(n) = Θ(n^(log_2 3))
     ≈ Θ(n^1.585)
```

### 📊 **Comparison**

| Algorithm | Time Complexity | For n = 1000 |
|-----------|----------------|--------------|
| **Grade-School** | O(n²) | ~10⁶ ops |
| **Karatsuba** | O(n^1.585) | ~40,000 ops |
| **Speedup** | - | **~25×** |

### 🔑 **Recursion Tree**

```
Level 0:            n^1.585           Work: n
                   /  |  \
Level 1:      (n/2)^1.585 ×3          Work: 3(n/2) = 1.5n
                / | \
Level 2:   (n/4)^1.585 ×9              Work: 9(n/4) = 2.25n
           ...

Levels: log₂ n
Growth per level: × 1.5
Total: n^(log₂ 3) ≈ n^1.585
```

---

## 11.6 Practical Considerations

### 🔧 **Threshold for Base Case**

**Trade-off:** Recursion overhead vs. speedup

**Practical:** Switch to grade-school for n < 32 or n < 64

```c
BigInt karatsuba_optimized(BigInt x, BigInt y) {
    const int THRESHOLD = 32;
    
    if (x.length < THRESHOLD || y.length < THRESHOLD) {
        return multiply_simple(x, y);  // Grade-school
    }
    
    // Karatsuba recursion...
}
```

### 🔧 **Handling Different Lengths**

**Problem:** x and y have different number of digits

**Solution:** Pad shorter number with leading zeros

```c
int max_len = (x.length > y.length) ? x.length : y.length;

// Pad x
while (x.length < max_len) {
    x.digits[x.length++] = 0;
}

// Pad y  
while (y.length < max_len) {
    y.digits[y.length++] = 0;
}
```

---

## 11.7 Even Faster Algorithms

### 📚 **Toom-Cook Algorithm**

**Idea:** Split into k parts instead of 2

**Toom-3 (k=3):**
```
T(n) = 5T(n/3) + O(n)
```

**Time:** O(n^(log_3 5)) ≈ O(n^1.465)

**Faster than Karatsuba!**

### 📚 **Fast Fourier Transform (FFT)**

**Schönhage-Strassen algorithm:**

**Time:** O(n log n log log n)

**Asymptotically fastest for general multiplication!**

### 📚 **Fürer's Algorithm (2007)**

**Time:** O(n log n · 2^(O(log* n)))

**log* n:** Iterated logarithm (grows incredibly slowly)

**Practically:** FFT-based methods dominate for huge numbers

---

## 11.8 Applications

### 🌍 **1. Cryptography**

**RSA encryption:** Multiply very large primes (2048+ bits)

**Performance critical:** Karatsuba widely used

### 🌍 **2. Computer Algebra Systems**

**Mathematica, Maple, Sage:** Manipulate polynomials with large coefficients

**Polynomial multiplication:** Same as integer multiplication in different base

### 🌍 **3. Arbitrary-Precision Libraries**

**GMP (GNU Multiple Precision):** Uses Karatsuba for medium-sized integers

**Python integers:** Unlimited precision, Karatsuba for multiplication

### 🌍 **4. Scientific Computing**

**High-precision calculations:** π to billions of digits

**Matrix operations:** With high-precision entries

---

## 📋 Summary

### 🎯 **Key Concepts**

1. **Grade-school multiplication:** O(n²) time
2. **Naive divide-and-conquer:** Still O(n²)  
3. **Karatsuba's insight:** Trade multiplication for addition
4. **Three recursive calls:** Instead of four
5. **Time complexity:** O(n^1.585) via Master Theorem

### 🔑 **Algorithm Summary**

```
Karatsuba(x, y):
  Base case: If n ≤ threshold, use grade-school
  
  Split: x = a·10^m + b, y = c·10^m + d
  
  Compute:
    P₁ = Karatsuba(a, c)
    P₂ = Karatsuba(b, d)
    P₃ = Karatsuba(a+b, c+d)
  
  Return: P₁·10^(2m) + (P₃-P₁-P₂)·10^m + P₂
```

### 📊 **Complexity Comparison**

| Algorithm | Time | When to Use |
|-----------|------|-------------|
| **Grade-School** | O(n²) | Small numbers (n < 32) |
| **Karatsuba** | O(n^1.585) | Medium numbers (32 < n < 10,000) |
| **Toom-Cook** | O(n^1.465) | Large numbers |
| **FFT-based** | O(n log n log log n) | Huge numbers (n > 10,000) |

---

## 📚 References

1. **Karatsuba, A., & Ofman, Y. (1962).** "Multiplication of multidigit numbers on automata." *Soviet Physics Doklady*.
   - Original Karatsuba algorithm paper

2. **Cormen, T. H., et al. (2009).** *Introduction to Algorithms* (3rd ed.). MIT Press.
   - Section 30.1: Polynomial multiplication

3. **Knuth, D. E. (1997).** *The Art of Computer Programming, Vol. 2: Seminumerical Algorithms* (3rd ed.). Addison-Wesley.
   - Chapter 4.3: Multiple-precision arithmetic

4. **Schönhage, A., & Strassen, V. (1971).** "Schnelle Multiplikation großer Zahlen." *Computing*.
   - FFT-based multiplication

---

**Congratulations!** You've completed all chapters for Internal 2 preparation! 🎉
