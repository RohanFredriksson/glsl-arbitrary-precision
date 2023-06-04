# GLSL Arbitrary Precision Arithmetic
An arbitrary-precision arithmetic library for the OpenGL Shading Language (GLSL)

```glsl
void main() {
    
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
            return vec3(1.0, 1.0, 1.0) * (float(k) / float(iterations));
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
    
    return vec3(0.0, 0.0, 0.0);
}
```
