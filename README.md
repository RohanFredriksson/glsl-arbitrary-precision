# GLSL Arbitrary Precision Arithmetic
An arbitrary-precision arithmetic library for the OpenGL Shading Language (GLSL)

```glsl
float mandelbrot() {
    
    uint c_r[ARRAY_SIZE];
    uint c_i[ARRAY_SIZE];
    uint z_r[ARRAY_SIZE];
    uint z_i[ARRAY_SIZE];
    
    load(c_r, -0.75);
    load(c_i, 0.1);
    zero(z_r);
    zero(z_i);
    
    uint nz_r[ARRAY_SIZE];
    uint nz_i[ARRAY_SIZE];
    
    int iterations = 64;
    for (int k = 0; k < iterations; k++) {
        
        uint tmp[ARRAY_SIZE];
        uint radius[ARRAY_SIZE];
        mul(z_r, z_r, tmp);
        mul(z_i, z_i, radius);
        add(radius, tmp, radius);
        
        if (radius[1] > 4) {
            return float(k) / float(iterations);
        }
        
        mul(z_i, z_i, nz_r);
        negate(nz_r);
        mul(z_r, z_r, tmp);
        add(nz_r, tmp, nz_r);
        add(nz_r, c_r, nz_r);
        
        load(nz_i, 2.0);
        mul(nz_i, z_r, nz_i);
        mul(nz_i, z_i, nz_i);
        add(nz_i, c_i, nz_i);
        
        assign(z_r, nz_r);
        assign(z_i, nz_i);
        
    }
    
    return 0.0;
}
```

### Encoding
Numbers are encoded using an array of unsigned integers (uint) in the following format:
#### Sign (index 0):
 - 0: The number is **non-negative**
 - 1: The number is **negative**
#### Whole Value (index 1):
 - Represents the whole (integer) part of the number
#### Fractional Values (indices 2, 3, ...):
 - Each subsequent element represents fractional values that are 2^32 times smaller than the previous one.

### Examples
#### Positive Whole Number (e.g., 42):
```csharp
[0, 42]
```
#### Negative Decimal Number (e.g., -42.5):
```csharp
[1, 42, 2147483648] // 2147483648 represents 0.5 as 2^31 = 2147483648
```
#### Really Small Fractional Number (e.g., 5.42101086243e-20)
```csharp
[0, 0, 0, 1]
```
