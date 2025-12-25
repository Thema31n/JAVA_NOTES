## 🔹 1. Constants

- `Math.PI` → 3.14159…
    
- `Math.E` → 2.71828…
    

---

## 🔹 2. Basic Operations

|Function|Example|Output|
|---|---|---|
|`abs(x)`|`Math.abs(-10)`|`10`|
|`max(a,b)`|`Math.max(3,7)`|`7`|
|`min(a,b)`|`Math.min(3,7)`|`3`|
|`sqrt(x)`|`Math.sqrt(16)`|`4.0`|
|`cbrt(x)`|`Math.cbrt(27)`|`3.0`|
|`pow(a,b)`|`Math.pow(2,3)`|`8.0`|
|`hypot(x,y)`|`Math.hypot(3,4)`|`5.0` (like √(x²+y²))|

---

## 🔹 3. Rounding Functions

|Function|Example|Output|
|---|---|---|
|`round(x)`|`Math.round(4.6)`|`5` (long)|
|`floor(x)`|`Math.floor(4.9)`|`4.0`|
|`ceil(x)`|`Math.ceil(4.1)`|`5.0`|
|`rint(x)`|`Math.rint(5.5)`|`6.0` (ties go to even)|

---

## 🔹 4. Trigonometry

|Function|Example|Output|
|---|---|---|
|`sin(x)`|`Math.sin(Math.PI/2)`|`1.0`|
|`cos(x)`|`Math.cos(0)`|`1.0`|
|`tan(x)`|`Math.tan(Math.PI/4)`|`1.0`|
|`asin(x)`|`Math.asin(1.0)`|`π/2`|
|`acos(x)`|`Math.acos(0.0)`|`π/2`|
|`atan(x)`|`Math.atan(1.0)`|`π/4`|
|`atan2(y,x)`|`Math.atan2(1,1)`|`π/4` (handles quadrants)|

> [!tip]  
> All trig functions expect **radians**, not degrees.  
> Convert degrees → radians with `Math.toRadians(deg)`.

---

## 🔹 5. Exponentials & Logarithms

|Function|Example|Output|
|---|---|---|
|`exp(x)`|`Math.exp(1)`|`e^1 = 2.718`|
|`log(x)`|`Math.log(Math.E)`|`1.0` (natural log)|
|`log10(x)`|`Math.log10(100)`|`2.0`|
|`expm1(x)`|`Math.expm1(1)`|`e^1 - 1`|
|`log1p(x)`|`Math.log1p(0.5)`|`ln(1.5)`|

---

## 🔹 6. Random Numbers

- `Math.random()` → returns double in `[0.0, 1.0)`
    

Example:

```java
int n = (int)(Math.random() * 10); // 0 to 9
```

---

## 🔹 7. Special Functions

|Function|Example|Output|
|---|---|---|
|`signum(x)`|`Math.signum(-10)`|`-1.0`|
|`copySign(mag, sign)`|`Math.copySign(3.0, -2.0)`|`-3.0`|
|`ulp(x)`|`Math.ulp(1.0)`|smallest step near 1.0|
|`nextUp(x)`|`Math.nextUp(1.0f)`|next float bigger than 1|
|`nextDown(x)`|`Math.nextDown(1.0)`|next double smaller than 1|

---

# ✅ Summary

- **Basic**: `abs`, `max`, `min`, `sqrt`, `pow`
    
- **Rounding**: `round`, `floor`, `ceil`, `rint`
    
- **Trig**: `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `atan2`
    
- **Exponential/Log**: `exp`, `log`, `log10`, `expm1`, `log1p`
    
- **Random**: `random()`
    
- **Special**: `signum`, `copySign`, `ulp`, `nextUp`, `nextDown`
    
- **Constants**: `PI`, `E`