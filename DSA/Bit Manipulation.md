**Convert Binary to Decimal**
to convert to decimal, in reverse order of the binary string, we take elements, and multiply with 2 on power increasing 
```cpp
int convertToDecimal(string& x) {
    int len = x.size();
    int p2 = 1, num = 0;
    for (int i = len - 1; i >= 0; i--) {
        if (x[i] == '1') 
            num += p2;
        p2 *= 2;
    }
    return num;
}
```

**Convert Decimal to Binary**
to convert to binary, we keep dividing by 2 till remainder is either 1 or 0 and take the remainders in reverse order
```cpp
string convertToBinary(int n) {
    string res = "";
    while (n != 1) {
        if(n%2 == 1) res+='1';
        else res+='0';
        n /= 2;
    }
    reverse(res);
    return res;
}
```

**Bit Operations**
1. AND (`&`)
    - All true → true
    - One false → false
2. OR (`|`)
    - All false → false
    - One true → true
3. XOR (`^`)
    - Odd number of 1's → odd
    - Even number of 1's → even
4. Shift (`>>`, `<<`)
    - Shift right (`>>`): Divides by $2^n$, $x>>n = x/2^n$
    - Shift left (`<<`): Multiplies by $2^n$, $x<<n = x*2^n$
---
31, 30, 29 ....  <---... 2,1,0 th bit => this order is in binary numbers
*31st bit is sign bit -> 0 means positive, 1 means negative*
$[-2^{31}, 2^{31} - 1]$ => `[INT_MIN, INT_MAX]`

- `xor of 2 same numbers is 0 `

##### Swap 2 Numbers using XOR
```cpp
a = a^b
b = a^b  //((a^b)^b) = (a^(b^b)) = a
a = a^b  //((a^b)^a) = (b^(a^a)) = b
```

##### Check if the ith bit is Set
Left shift to $i_{th}$ position and perform &
```cpp
((N&(1<<i)) != 0)
```

##### Set the ith bit
```cpp
(N|(1<<i)
```

##### Clear the ith bit
```cpp
(N&(~(1<<i)))
```

##### Toggle the ith bit
xor
```cpp
(N^(1<<i))
```

##### Remove the rightmost set bit 
N=16 -> 10000
N=15 -> 01111
-> 00000 , removed the rightmost set bit
```cpp
N & (N - 1)
```

##### Check if number is a power of 2
in power of 2, only 1 set bit is present, if you remove the rightmost bit in that, the resulting number would be 0
```cpp
if((N&N-1) == 0) //power of 2
```

##### Last Bit of an Odd Number is always 1
```cpp
if(N&1 == 1) //odd
if(N&1 == 0) //even
```

##### Count the Number of Set Bits
**STL Method**
`int count = __builtin_popcount(n);`

**Method 1: Using Bit Manipulation**
```cpp
int countsetBit(int n)
{
    int cnt = 0;
    while (n > 1)
    {
        cnt += n & 1;
        n = n >> 1;
    }
    if (n == 1) cnt += 1;
    return cnt;
}
```

**Method 2: Brian Kernighan's Algorithm**
```cpp
int countSetBits(int n)
{
    int cnt = 0;
    while (n != 0)
    {
        n = n & (n - 1);
        cnt++;
    }
    return cnt;
}
```

##### Divide 2 Numbers
eg: 22/3 can be approached by subtracting 3 manytimes, closest it can reach is 3x3x...7 times, 3x7, or it can be written as 3x($2^3 + 2^2 + 2^0$), so 22 to be subtracted with highest possible number of 3x2^power and kept doing till remainder is less than dividend, for sign, either dividend or divsor has to be positive, so that will be done later

```cpp
int divide(int dividend, int divisor) {
    // If dividend equals divisor, the result is 1
    if (dividend == divisor) {
        return 1;
    }

    bool sign = true;
    if ((dividend >= 0 && divisor < 0) || (dividend < 0 && divisor > 0)) {
        sign = false; // Result is negative
    }

    // Work with absolute values
    int n = abs(dividend);
    int d = abs(divisor);
    int ans = 0;

    while (n >= d) {
        int cnt = 0;
        while (n >= (d << (cnt + 1))) {
            cnt++;
        }
        ans += (1 << cnt); // Add 2^cnt to the answer
        n -= (d << cnt); // Subtract d*2^cnt from the dividend
    }

    if (ans >= INT_MAX && sign == true) {
        return INT_MAX;
    } else if (ans >= INT_MAX && sign == false) {
        return INT_MIN;
    }

    return sign ? ans : -ans;
}

```

##### Maximum Bit Flip to Convert a Number
xor the 2 numbers to find the bits need to be flipped and count them
```cpp
int ans = start^goal;
int cnt = 0;
for(int i = 0;i<32;i++){
	if(ans&(1<<i) == 1) cnt++ 
}
return cnt;
```

##### FInd No. that Appears Odd Number of Times
xor of 2 same numbers is 0 
```cpp
int xorr = 0;
for(int i = 0;i<n;i++){
	xorr = xorr^arr[i];
}
return xorr;
```

##### Power Set
return all the subsets, generally done with recursion.
here take index in a table, and fill the table how its done for solving gates in DSD, 000, 001, 010, etc. and from that table *0 means don't choose, 1 means choose.*, length of array is the number of states, for length 3, $2^3$ states 000,001,etc.

```cpp
vector<vector<int>> findSubsets(vector<int>& nums) {
    int n = nums.size();
    int totalSubsets = 1 << n;  // 2^n
    vector<vector<int>> ans;

    for (int num = 0; num < totalSubsets; num++) { //0->000,1->001,2->010...
        vector<int> subset;
        for (int i = 0; i < n; i++) {
            if (num & (1 << i)) {
                subset.push_back(nums[i]);
            }
        }
        ans.push_back(subset);
    }

    return ans;
}
```

##### Find XOR of Numbers in a Given Range
brute -> for loop O(N)
optimal -> observation
```cpp
funct(N){
	if(N%4 == 1) return 1;
	else if(N%4 == 2) return N+1;
	else if(N%4 == 3) return 0;
	else return N;
}
```

##### 2 Numbers with Odd Occurence and Others with Even Numbers
Compute the XOR of all elements in the array. This will give a number where at least one set bit indicates a difference between the two groups. Identify the rightmost set bit in the XOR result. Separate the numbers into two groups based on whether they have this bit set or not.

```cpp
vector<int> separateNumbers(vector<int>& nums) {
    int xor_all = 0;
    // Step 1: XOR all numbers
    for (int num : nums) {
        xor_all ^= num;
    }

    // Step 2: Find rightmost set bit
    //or directly separate that bit with  (xor_all & -xor_all) no. and its 2' complement in &
    int rightmost_set_bit = (xor_all & (xor_all-1))^xor_all;

    int b1 = 0, b2 = 0;
    // Step 3: Split into two groups based on that bit
    for (int num : nums) {
        if (num & rightmost_set_bit) {
            b1 ^= num;
        } else {
            b2 ^= num;
        }
    }

    return {b1, b2};
}
```

##### Count Prime Till a Number N
**Brute Approach**
```cpp
bool isPrime(int n) {
    int cnt = 0;
    for (int i = 1; i <= sqrt(n); i++) {
        if (n % i == 0){
            cnt++;
            if(n/i != 1){
	            cnt++;
            }
		}
    }
    if(cnt == 2) return true;
    return false;
}


int countPrimesUpTo(int N) {
    int count = 0;
    for (int i = 2; i <= N; i++) {
        if (isPrime(i))
            count++;
    }
    return count;
}
//T.C = O(n*(sqrt(n)))
```

###### Sieve of  Eratosthenes
**Precomputation**
Sieve = "Cross out multiples" starting from 2, then 3, then 5, etc.  
Left unmarked? → They’re primes.
The algorithm only needs to check up to the square root of N because any composite number larger than N​ must have a prime factor less than or equal to N.

```cpp
vector<int> sieveOfEratosthenes(int N) {
    // Create boolean array, initialize all as true
    vector<bool> prime(N + 1, true);
    
    // 0 and 1 are not prime
    prime[0] = prime[1] = false;

    // Start from 2 up to sqrt(N)
    for (int i = 2; i * i <= N; i++) {
        if (prime[i]) {
            // Mark all multiples of i starting from i*i
            for (int j = i * i; j <= N; j += i) {
                prime[j] = false;
            }
        }
    }

    // Collect all primes
    vector<int> primes;
    for (int i = 2; i <= N; i++) {
        if (prime[i]) {
            primes.push_back(i);
        }
    }

    return primes;
}
//T.C = O(N*log(logN))
```

##### Prime Factorization
**Brute Force**
```cpp
vector<int> primeFactors(int N) {
    vector<int> factors;

    for (int i = 2; i <= N; ++i) {
        while (N % i == 0) {
            factors.push_back(i);
            N /= i;
        }
    }

    return factors;
}
```

**Optimal** -> **Sieve of Eratosthenes for Smallest Prime Factors**
```cpp
vector<int> smallestPrimeFactors(int N) {
    vector<int> spf(N + 1);

    for (int i = 2; i <= N; ++i) {
        if (spf[i] == 0) {
            spf[i] = i;
            for (int j = i * i; j <= N; j += i) {
                if (spf[j] == 0) {
                    spf[j] = i;
                }
            }
        }
    }
    return spf;
}
```

##### Power Exponentiation
calculating $a^b$

**Brute**
```cpp
int power(int a, int b) {
    int result = 1;
    for (int i = 0; i < b; i++) {
        result *= a;
    }
    return result;
}
```

**Optimal**
$x^n$
![[Pasted image 20250730005735.png | 600]]
```cpp
double power(double x, int n) {
    double ans = 1;
    int m = n;

    while (n > 0) {
        if (n % 2 == 1) {
            ans = ans * x;
            n = n - 1;
        }
        else{
	        x = x * x;
	        n = n / 2;
        }
        
    }

    if (m < 0) {
        return 1.0 / ans;
    }
	return ans;
}
```

