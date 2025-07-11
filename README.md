# excel-problem-solving

This hobby project uses **spreadsheets** to solve numeric puzzles as those proposed by Project Euler.

This is a personal, exploratory project with no fixed roadmap. Its aim is essentially to improve my skills. Development happens irregularly as time permits.

Currently, it uses Microsoft Excel. Even if VBA solutions may be proposed, the purpose of this project is to find solutions *without VBA*.

The objective is to move to open source alternatives as Libre Office or Only Office.

When possible, several such solutions are presented:  
- spreadsheet capabilities (use of rows and columns)  
- one-liner based on recursion  
- one-liner based on array formulas based on `SEQUENCE`, etc.

## Table of contents

**Tools:** [recursion](#tools-recursion), [array formulas](#tools-array-formulas), [useful functions](#tools-useful-functions), [VBA functions on primality](#tools-vba-functions-on-primality)

**Project Euler problems:**
[1](#project-euler-001-multiples-of-3-or-5), [2](#project-euler-002-even-fibonacci-numbers), ...,  [4](#project-euler-004-largest-palindrome-product), [5](#project-euler-005-smallest-multiple), [6](#project-euler-006-sum-square-difference), [7](#project-euler-007-10001st-prime), ...,  [8](#project-euler-008-largest-product-in-a-series), 
[9](#project-euler-009-special-pythagorean-triplet), ...,  [11](#project-euler-011-largest-product-in-a-grid), ..., [13](#project-euler-013-large-sum)

## Tools: recursion

Recursion can be achieved by the use of `LET` and `LAMBDA`.

For instance, this formula calculates 10!:
``` excel
= LET(
SUB; LAMBDA(ME;N; IF(N=0; 1; N*ME(ME; N-1)));
SUB(SUB; 10))
```
Value: 3628800

The same with tail-call recursion:
``` excel
=LET(
SUB; LAMBDA(ME;ACC;N; IF(N=0; ACC; ME(ME;ACC*N; N-1)));
SUB(SUB; 1; 10))
```
Value: 3628800

## Tools: array formulas

Create a vertical array:  
- `{1;2;3;4;5}` creates a vertical array 1...5  
- `SEQUENCE(10)` creates a vertical array 1...10  
- `SEQUENCE(1,10)` creates a vertical array 1...10  
- `SEQUENCE(10)*SEQUENCE(1,10)` creates a 2D array 1...10 x 1...10 containing products  
- trick: `IF({1;0}; 4; 5)` creates a vertical array with two elements: 4 and 5

Return specific element of an array:  
- `INDEX(array; row)`  
- `INDEX(array;; column)`

Operations on arrays:  
- `MAP`  
- `SUM`  
- `REDUCE` (`REDUCE([initial_value], array, lambda(accumulateur, value, body))`)  
- `MAX`

Some usual functions accept array and return array.  
For instance:  
- `MID` (substring) with a sequence of position in string; returns a horizontal array

## Tools: useful functions

**Operations on range:** 
`OFFSET(cell; 0; 0; 1; 4)` refers to the range spanning from 'cell' with height 1 and width 4  
`OFFSET(cell; 1; 2)` refers to the cell located 1 row below and 2 columns on the right of 'cell' 

**Operations on strings:**  
substring: `MID`

**String to number:**  
convert string to number: `VALUE`

**Digits of a number:**  
- number of digits: `LEN`  
- M-th digit of N (counting from right): `MOD(QUOTIENT(N; 10^(M-1)); 10)`  
- M-th digit of N (couting from left): `MOD(QUOTIENT(N; 10^(LEN(N)-M)); 10)`  
- sum of digits of N (with separate identification of each digits):  
`SUM(MAP(SEQUENCE(LEN(N)); LAMBDA(M; MOD(QUOTIENT(N; 10^(M-1)); 10))))`   
- sum of digits of N (via 2-cell accumulator):  
`INDEX(REDUCE(IF({1;0}; 0; N); SEQUENCE(LEN(N)); LAMBDA(ACC;N; IF({1;0}; INDEX(ACC; 1) + MOD(INDEX(ACC; 2); 10); QUOTIENT(INDEX(ACC; 2); 10)))); 1)`
- product of digits of N (via 2-cell accumulator):  
`INDEX(REDUCE(IF({1;0}; 1; N); SEQUENCE(LEN(N)); LAMBDA(ACC;N; IF({1;0}; INDEX(ACC; 1) * MOD(INDEX(ACC; 2); 10); QUOTIENT(INDEX(ACC; 2); 10)))); 1)`

**Digits of a number expressed as a string:**  
- convert string S to horizontal array of digits: `VALUE(MID(S; SEQUENCE(1; LEN(S)); 1))`  
- multiply digits of a number expressed as a string S: `PRODUCT(VALUE(MID(S; SEQUENCE(1; LEN(S)); 1)))`

## Tools: VBA functions on primality

Return reversed number:
``` VBA
Function ReversedNumber(ByVal n As Long) As Long
    Dim LastDigit As Long
    ReversedNumber = 0
    Do While n > 0
        LastDigit = n Mod 10
        n = n \ 10
        ReversedNumber = 10 * ReversedNumber + LastDigit
    Loop
End Function
```

Test if a number is prime:

``` VBA
Function IsPrime(n As Long) As Boolean
    If (n = 2) Or (n = 3) Or (n = 5) Or (n = 7) Then
        IsPrime = True
        Exit Function
    End If
    If (n <= 1) Or (n Mod 2 = 0) Or (n Mod 3 = 0) Then
        IsPrime = False
        Exit Function
    End If
    Dim squareRoot As Double
    Dim squareRootInt As Long
    Dim i As Long
    squareRoot = Sqr(n)
    squareRootInt = Fix(squareRoot)
    For i = 5 To squareRootInt Step 6
        If (n Mod i = 0) Or (n Mod (i + 2) = 0) Then
            IsPrime = False
            Exit Function
        End If
    Next i
    IsPrime = True
End Function
```

## Project Euler 001: Multiples of 3 or 5

_If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23. Find the sum of all the multiples of 3 or 5 below 1000._
[(source)](https://projecteuler.net/problem=1)

VBA solution (and a variant with LongLong):
``` VBA
Public Function ProjectEuler1(n As Long) As Long
   Dim sum, i As Long
   sum = 0
   For i = 1 To (n - 1)
      If ((i Mod 3) = 0) Or ((i Mod 5) = 0) Then sum = sum + i
    Next i
   ProjectEuler1 = sum
End Function
```

Several solutions without VBA are proposed:

_(i)_ spreadsheet capabilities (use of rows and columns),

_(ii)_ one-liner based on recursion

Recursion is implemented in the same way as factorial above :

``` excel
=LET(
MULTIPLE_OF_3_OR_5; LAMBDA(N;OR(MOD(N;3)=0;MOD(N;5)=0));
SUB; LAMBDA(ME;N; IF(N=0; 0; IF(MULTIPLE_OF_3_OR_5(N); N+ME(ME;N-1); ME(ME;N-1))));
SUB(SUB; D11))
```

_(iii)_ one-liner converting recursion into `SEQUENCE` adn `REDUCE`

``` excel
= REDUCE(0; SEQUENCE(999); LAMBDA(ACC;N; IF(OR(MOD(N;3)=0;MOD(N;5)=0); ACC+N; ACC)))
```

_(iv)_ one-liner based on array formulas

This one-liner is essentially the following. It creates a sequence 1...999, filters it, and sums it.
``` excel
= LET(
MULTIPLE_OF_3_OR_5; LAMBDA(N; OR(MOD(N;3)=0; MOD(N;5)=0));
FILTER_MULTIPLE_OF_3_OR_5; LAMBDA(N; IF(MULTIPLE_OF_3_OR_5(N); N; 0));
SUM( MAP( SEQUENCE(999); FILTER_MULTIPLE_OF_3_OR_5)))
```

`LET` is used to improve readibility, but is not necessary:
``` excel
= SUM( MAP( SEQUENCE(B21); LAMBDA(N; IF(OR(MOD(N;3)=0; MOD(N;5)=0); N; 0))))
```

## Project Euler 002: Even Fibonacci Numbers

_Each new term in the Fibonacci sequence is generated by adding the previous two terms. 
By starting with 1 and 2, the first 10 terms will be: 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...
By considering the terms in the Fibonacci sequence whose values do not exceed four million, find the sum of the even-valued terms._ [(source)](https://projecteuler.net/problem=2) 

**TODO: to be improved**

Two solutions are proposed:  
_(i)_ spreadsheet capabilities (use of rows and columns),  
_(ii)_ one-liner based on array formulas.

N-th Fibonacci number :
``` excel
LAMBDA(n; IF(n=1; 0; INDEX( REDUCE({0;1}; SEQUENCE(n-1); LAMBDA(x;y; IF({1;0}; INDEX(x;2); SUM(x)))); 2 )))
```

Make Fibonacci sequence:
``` excel
LAMBDA(m
   REDUCE(0;
      SEQUENCE(m);
      LAMBDA(current_array; n;
         IF(n=0; 0;
         IF(n=1; VSTACK(0;1);
            LET(new_fibo; HSTACK(INDEX(current_array;n;1) + INDEX(current_array;n-1;1));
                VSTACK(current_array;new_fibo)))))))
```

## Project Euler 004: Largest Palindrome Product

_A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit number is 9009 = 91 x 99. Find the largest palindrome made from the product of two 3-digit numbers._ [(source)](https://projecteuler.net/problem=4)

VBA solution using `ReversedNumber` function defined above:
``` VBA
Function ProjectEuler4(min As Long, max As Long) As Long
    Dim Product As Long
    Dim n As Long
    Dim m As Long
    ProjectEuler4 = 0
    For n = min To max
        For m = n To max
            Product = n * m
            If (Product > ProjectEuler4) And (Product = ReversedNumber(Product)) Then
                ProjectEuler4 = Product
            End If
        Next m
    Next n
End Function
```

Three solutions without VBA are proposed.

In file A:

_(i)_ one-liner based on double recursion:

``` excel
= LET(
NMAX; 999;
SUB_REVERSED; LAMBDA(ACC;N;ME; IF(N<10; 10*ACC+N; ME(10*ACC+MOD(N;10); QUOTIENT(N;10); ME)));
REVERSED_NUMBER; LAMBDA(N; SUB_REVERSED(0; N; SUB_REVERSED));
IS_PALINDROMIC; LAMBDA(N; N = REVERSED_NUMBER(N));
SUBSUB; LAMBDA(N;NN;CURRENT_MAX;ME; IF(NN=0; CURRENT_MAX; IF(IS_PALINDROMIC(N*NN); ME(N; NN-1; MAX(CURRENT_MAX; N*NN); ME); ME(N; NN-1; CURRENT_MAX; ME))));
SUB; LAMBDA(N;CURRENT_MAX;ME; IF(N=0; CURRENT_MAX; ME(N-1; MAX(CURRENT_MAX; SUBSUB(N; N; 0; SUBSUB)); ME)));
SUB(NMAX; 0; SUB))
```

_(ii)_ one-liner based on array formulas, creating a 1...999 x 1...999 2D array (containing products):

``` excel
= LET(
NMAX; 999;
SUB_REVERSED; LAMBDA(ACC;N;ME; IF(N<10; 10*ACC+N; ME(10*ACC+MOD(N;10); QUOTIENT(N;10); ME)));
REVERSED_NUMBER; LAMBDA(N; SUB_REVERSED(0; N; SUB_REVERSED));
IS_PALINDROMIC; LAMBDA(N; N = REVERSED_NUMBER(N));
MAX(MAP(SEQUENCE(NMAX)*SEQUENCE(1;NMAX); LAMBDA(N; IF(IS_PALINDROMIC(N); N; 0)))))

```

In file B (around 19 Mo):  
_(iii)_ in the form of a spreadsheet

In O26 cell:
``` excel
=IF(O$15>$D26; 0; LET(
SUB_REVERSED; LAMBDA(ACC;N;ME; IF(N<10; 10*ACC+N; ME(10*ACC+MOD(N;10); QUOTIENT(N;10); ME)));
REVERSED_NUMBER; LAMBDA(N; SUB_REVERSED(0; N; SUB_REVERSED));
PRODUCT; O$15*$D26;
REVERSED_PRODUCT; REVERSED_NUMBER(PRODUCT);
IF(PRODUCT = REVERSED_PRODUCT; PRODUCT; 0)))
```

## Project Euler 005: Smallest Multiple

_2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder. What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?_ [(source)](https://projecteuler.net/problem=5)

VBA solution :

``` VBA
Function ProjectEuler5(n As Long) As Long
    Dim i As Long
    ProjectEuler5 = 1
    For i = 1 To n
        ProjectEuler5 = WorksheetFunction.Lcm(ProjectEuler5, i)
    Next i
End Function
```

Three solutions without VBA are proposed:

_(i)_ one-liner based on recursion

The recursion solution is similar to factorial above.
``` excel
=LET(
SUB; LAMBDA(ME;N; IF(N=1; 1; LCM(N; ME(ME; N-1))));
SUB(SUB; D10))
```

_(ii)_ one-liner converting recursion into SEQUENCE+REDUCE

```
= REDUCE( 1; SEQUENCE(B20); LAMBDA(ACC;N; LCM(ACC;N)))
```

_(iii)_ one-liner based on array formulas

This one-liner is essentially the following. It creates a 1...20 sequence, then reduces it with the 'leas common multiple' (LCM) function:
```
=REDUCE(1; SEQUENCE(20); LAMBDA(a;v; LCM(a;v)))
```

## Project Euler 006: Sum Square Difference

_The sum of the squares of the first ten natural numbers is  
1^2 + 2^2 + ... + 10^2 = 385  
The square of the sum of the first ten natural numbers is  
(1 + 2 + ... + 10)^2 = 552 = 3025  
Hence the difference between the sum of the squares of the first ten natural numbers and the square of the sum is 3025 − 385 = 2640.  
Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum._  
[(source)](https://projecteuler.net/problem=6)

VBA solution :
``` VBA
Function ProjectEuler6(n As Long) As Long
    Dim i As Long
    ProjectEuler6 = 0
    For i = 1 To n
        ProjectEuler6 = ProjectEuler6 + i
    Next i
    ProjectEuler6 = ProjectEuler6 * ProjectEuler6
    For i = 1 To n
        ProjectEuler6 = ProjectEuler6 - i * i
    Next i
End Function
```

Two solutions without VBA are proposed:  
_(i)_ spreadsheet capabilities (use of rows and columns),  
_(ii)_ one-liner based on array formulas.

The one-liner based on array formulas is essentially the following. It creates a 1...100 sequence, then uses `MAP` to square it number by number, and concludes.
```
=SUM( SEQUENCE(100) )^2
      - SUM( MAP(SEQUENCE(100); LAMBDA(a;a^2)) )
```

## Project Euler 007: 10001st prime

_By listing the first six prime numbers: 2, 3, 5, 7, 11 and 13, we can see that the 6th prime is 13.  
What is the 10001st prime number?_  
[(source)](https://projecteuler.net/problem=7)

VBA solution taking advantage of `IsPrime` function defined above:
``` VBA
Function ProjectEuler7(targetRank As Long) As Long
   Dim n As Long
   Dim Rank As Long
   n = 3
   Rank = 2
   Do
      n = n + 2
      If IsPrime(n) Then
        Rank = Rank + 1
      End If
   Loop Until Rank = targetRank
   ProjectEuler7 = n
End Function
```

TODO : non-VBA solutions


## Project Euler 008: Largest Product in a Series

_The four adjacent digits in the 1000-digit number that have the greatest product are 9x9x8x9 = 5832.  
73167176531330624919225119674426574742355349194934  
96983520312774506326239578318016984801869478851843  
85861560789112949495459501737958331952853208805511  
12540698747158523863050715693290963295227443043557  
66896648950445244523161731856403098711121722383113  
62229893423380308135336276614282806444486645238749  
30358907296290491560440772390713810515859307960866  
70172427121883998797908792274921901699720888093776  
65727333001053367881220235421809751254540594752243  
52584907711670556013604839586446706324415722155397  
53697817977846174064955149290862569321978468622482  
83972241375657056057490261407972968652414535100474  
82166370484403199890008895243450658541227588666881  
16427171479924442928230863465674813919123162824586  
17866458359124566529476545682848912883142607690042  
24219022671055626321111109370544217506941658960408  
07198403850962455444362981230987879927244284909188  
84580156166097919133875499200524063689912560717606  
05886116467109405077541002256983155200055935729725  
71636269561882670428252483600823257530420752963450  
Find the thirteen adjacent digits in the 1000-digit number that have the greatest product.   What is the value of this product?_  
[(source)](https://projecteuler.net/problem=8)

Two solutions are proposed :

_(i)_ spreadsheet capabilities (use of rows and columns)

_(ii)_ one-liner based on array formulas (with two variants).

The first calculates the product of the digits of a number:
``` excel
=LET(
input_string; $B$19;
thirteen; $B22;
PRODUCT_OF_DIGITS; LAMBDA(N; INDEX(REDUCE(IF({1;0}; 1; N); SEQUENCE(LEN(N)); LAMBDA(ACC;N; IF({1;0}; INDEX(ACC; 1) * MOD(INDEX(ACC; 2); 10); QUOTIENT(INDEX(ACC; 2); 10)))); 1));
MAX(MAP(SEQUENCE(LEN(input_string)-thirteen+1); LAMBDA(POS; PRODUCT_OF_DIGITS(VALUE(MID(input_string; POS; thirteen)))))))
```

The second does the same when the number is presented as a string:
```excel
=LET(
input_string; $B$19;
thirteen; $B30;
STRING_TO_PRODUCT_OF_DIGITS; LAMBDA(S; PRODUCT(VALUE(MID(S; SEQUENCE(1; LEN(S)); 1))));
MAX(MAP(SEQUENCE(LEN(input_string)-thirteen+1); LAMBDA(POS; STRING_TO_PRODUCT_OF_DIGITS(MID(input_string; POS; thirteen))))))
```

## Project Euler 009: Special Pythagorean Triplet
    
_A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,
a2 + b2 = c2  
For example, 32 + 42 = 9 + 16 = 25 = 52.  
There exists exactly one Pythagorean triplet for which a + b + c = 1000.  
Find the product abc._  
[(source)](https://projecteuler.net/problem=9)

VBA solution :

``` VBA
Function ProjectEuler9() As Long
    Dim n As Long, a As Long, b As Long, bmin As Long, bmax As Long, c As Long
    ProjectEuler9 = -1
    n = 1000
    For c = 3 To n
        ' upper limit for b:
        '   (i) b < c
        '   (ii) b = 1000-c-a with a >= 1 thus b <= 1000-c-1
        ' lower limit for b:
        '   (i) 1 <= a < b thus b > 1
        '   (ii) 1000-c = a+b with a < b thus 1000-c < 2b
        bmax = WorksheetFunction.Min(c - 1, n - c - 1)
        bmin = WorksheetFunction.Max(2, (n - c) \ 2)
        For b = bmin To bmax
            a = n - b - c
            If c * c = a * a + b * b Then
                ProjectEuler9 = a * b * c
                Exit Function
            End If
        Next b
    Next c
End Function
```

Two solutions without VBA are proposed:

_(i)_ within file A: one-liner based on array formulas


``` excel
=LET(N;1000;
   MAX(MAP(SEQUENCE(N-2;1;3);
      LAMBDA(C;
         LET(
            BMIN;MAX(2;INT((N-C)/2));
            BMAX;MIN(C-1;N-C-1);
            IF(BMAX >= BMIN;
               MAX(MAP(SEQUENCE(BMAX-BMIN+1;1;BMIN);LAMBDA(B;
                  LET(A; N-B-C;
                     IF(C*C=A*A+B*B;A*B*C;0))));
               0)))))))
```

_(ii)_ within file B: spreadsheet capabilities (use of rows and columns)

In each cell:
``` excel
=LET(
   b; $C18;
   c; R$10;
   a; 1000-b-c;
   IF(AND(b >= MAX(2;INT((1000-c)/2)); b <= MIN(c-1;1000-c-1); c*c = b*b + a*a); a*b*c; 0 ))
```

## Project Euler 011: Largest Product in a Grid

_In the 20 x 20 grid below, four numbers along a diagonal line have been marked in red.  
08 02 22 97 38 15 00 40 00 75 04 05 07 78 52 12 50 77 91 08  
[...]  
01 70 54 71 83 51 54 69 16 92 33 48 61 43 52 01 89 19 67 48  
The product of these numbers is 26 x 63 x 78 x 14 = 1788696.  
What is the greatest product of four adjacent numbers in the same direction (up, down, left, right, or diagonally) in the 20 x 20 grid?_  
[(source)](https://projecteuler.net/problem=11)

Two solutions are proposed:  
_(i)_ spreadsheet capabilities (use of rows and columns),  
_(ii)_ one-liner based on array formulas.

The one-liner is the following:

```
=LET(
MAX_HORIZONTAL; MAX(MAP(D6:T25;LAMBDA(CELL; PRODUCT(OFFSET(CELL;0;0;1;4)))));
MAX_VERTICAL; MAX(MAP(D6:W22;LAMBDA(CELL; PRODUCT(OFFSET(CELL;0;0;4;1)))));
MAX_DIAGONAL_1; MAX(MAP(D6:T22;LAMBDA(CELL;PRODUCT(CELL;OFFSET(CELL;1;1);OFFSET(CELL;2;2);OFFSET(CELL;3;3)))));
MAX_DIAGONAL_2; MAX(MAP(D9:T25;LAMBDA(CELL;PRODUCT(CELL;OFFSET(CELL;-1;1);OFFSET(CELL;-2;2);OFFSET(CELL;-3;3)))));
MAX(MAX_HORIZONTAL; MAX_VERTICAL;MAX_DIAGONAL_1;MAX_DIAGONAL_2))
```
## Project Euler 013: Large Sum

_Work out the first ten digits of the sum of the following one-hundred 50-digit numbers.  
37107287533902102798797998220837590246510135740250  
[...]  
53503534226472524250874054075591789781264330331690_  
[(source)](https://projecteuler.net/problem=13)

Two solutions are proposed:  
_(i)_ spreadsheet capabilities (use of rows and columns),  
_(ii)_ one-liner. **_TODO_**

Excel cannot handle numbers with so many significant digits. But they can be stored in strings, and be manipulated under that form.  
Solution _(i)_ breaks down each number into digits, and adds all digits from the right-most to the left-most, while keeping track of the carry.

(end of README)
