# Newton's Method

### Square Roots, Newton's Method

#### Square Roots

- recall the sqrt(2) problem  
  x<sub>i+1</sub> = (x<sub>i</sub> + a/x<sub>i</sub>) / 2  
  Notice the division in it  
  that's what we'll settle down

##### Error Analysis of Newton's Method

- x<sub>n</sub> = âa (1 + ğ<sub>n</sub>)   ğ<sub>n</sub> may +/-  
  x<sub>n+1</sub> = ( âa ( 1 + ğ<sub>nÂ </sub>) + a / ( âa ( 1 + ğ<sub>nÂ </sub>) ) / 2  
          = ( âa ( 1 + ğ<sub>n</sub> ) + 1 / ( 1 + ğ<sub>n</sub> ) ) / 2  
          = âa (1 + ğ<sub>n</sub><sup>2</sup> / 2( 1 + ğ<sub>n</sub> ))
- Therefore  
  ğ<sub>n+1</sub> = ğ<sub>n</sub><sup>2</sup> / 2( 1 + ğ<sub>n</sub> ) â ğ<sub>n</sub><sup>2</sup> / 2  
  say if we have a 0.01 error  
  the next iteration will make it 0.0001  
  this is why we have a quadratic convergence that the accurate digits doubles every time
- **d-digit of precision => lgd iterations**

##### Multiplication Algo

- recall Karatsuba Algo
- Toom-Cook (kâ¥2   k parts)
  - say we use Toom-3  
    T(n) = 5T(n/3) Î(n)  
    T(n) = Î(n<sup>log<sub>3</sub><sup>5</sup></sup>) = Î(n<sup>1.465</sup>)
- Schonhage-Strassen  
  - Î(n lgn lglgn) time
  - use FFT
  - in gmpy package in Python
- Furer (2007)  
  Î(nlgn 2<sup>2<sup>O( log<sup>*n</sup> )</sup></sup>)  
  nlgn is for sorting  
  log<sup>\*n</sup> is the # of log needs to be applied to get a result that â¤ 1

##### High-precision Division

- we want a high-precision rep of a/b  
  we need a high-precision rep of 1/b first  
  we will compute floor(R/b) where R is a really large value, s.t.(such that) it's easy to divide by R (R = 2<sup>k</sup>) by shift the floating point
- Division  
  Newton's Method for computing R/b  
  f(x) = 1/x - b/R (zero at x = R/b)  
  f '(x) = -1/x<sup>2</sup>  
  **x<sub>i+1</sub> = 2x<sub>i</sub> - bx<sub>i</sub><sup>2</sup>/R**  
- i.e.  R/b = 2<sup>16</sup>/5 = 65536/2 = 13107.2  
  Initial guess: 2<sup>16</sup>/4 = 2<sup>14</sup> -> x<sub>0</sub> = 16384  
  x<sub>1</sub> = 2(16384) - 5(16384)<sup>2</sup>/65536 = <u>1</u>2288  
  x<sub>2</sub> = 2(12288) - 5(12288)<sup>2</sup>/65536 = <u>13</u>056  
  ...  
  lgd times to get the needed accurate digits
- Division: quadratic convergence: # digits doubles at each step  
  d-digit of precision => lgd iterations  
  Multiplication in Î(n<sup>ğ</sup>)   ğ â¥ 1  
  => O(lgd n<sup>ğ</sup>)  
  =>O(lgn n<sup>ğ</sup>)
- BUT! Let's look into it more carefully  
  for d digits precision  
  every time of multiplication we have constant time of division  
  T(n) = C1<sup>ğ</sup> + C2<sup>ğ</sup> + ... + C(d/2)<sup>ğ</sup> + Cd<sup>ğ</sup> < 2Cd<sup>ğ</sup>
- SO!  
  Complexity of division == Complexity of multiplication  
  **O(n<sup>ğ</sup>)**

##### Time Complexity Analysis of Square Root

- âa with d-digit precision
- floor(10<sup>2d</sup>a) => Newton's method: x<sub>i+1</sub> = (x<sub>i</sub> + a/x<sub>i</sub>) / 2 => Division => Newtion's method: x<sub>i+1</sub> = 2x<sub>i</sub> - bx<sub>i</sub><sup>2</sup>/R  
- **Complexity of Square Root â Complexity of division â Complexity of multiplication** 






