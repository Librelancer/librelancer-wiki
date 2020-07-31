Material library
================

Single pass materials
---------------------

These are common materials also known as single pass materials.
Two-sided variants (backface not culled) are indicated by Two suffix at
the name.

| Type          | Description                                                                        |
|---------------|------------------------------------------------------------------------------------|
| DcDt          | Diffuse color + Diffuse texture                                                    |
| DcDtEc        | Diffuse color + Diffuse texture + Emission color                                   |
| DcDtOcOt      | Diffuse color + Diffuse texture + Opacity color + Opacity texture                  |
| DcDtEcOcOt    | Diffuse color + Diffuse texture + Emission color + Opacity color + Opacity texture |
| DcDtEt        | Diffuse color + Diffuse texture + Emission texture                                 |
| EcEt          | Emission color + Emission texture                                                  |
| DcDtTwo       | Two-sided variant of DcDt                                                          |
| DcDtEcTwo     | Two-sided variant of DcDtEc                                                        |
| DcDtOcOtTwo   | Two-sided variant of DcDtOcOt                                                      |
| DcDtEcOcOtTwo | Two-sided variant of DcDtEcOcOt                                                    |

Two-pass materials
------------------

Special materials
-----------------

### Nebula materials

Material uses vertex color for texture tint and adds vertex alpha to
transparency of diffuse texture provided it's in DXT5 format. Cannot be
animated.

| Type      | Description                                                               |
|-----------|---------------------------------------------------------------------------|
| Nebula    | Diffuse texture tinted by vertex color with blending (ADD, SRCALPHA, ONE) |
| NebulaTwo | Two-sided variant of Nebula.                                              |

### Nomad materials

Translucent shader effect with environment mapping applied. Cannot be
animated.

| Type                 | Description |
|----------------------|-------------|
| NomadMaterial        |             |
| NomadMaterialNoBendy |             |

### Null material

Game defaults to this material if material referenced cannot be found in
material library.

| Type         | Description        |
|--------------|--------------------|
| NullMaterial | Plain transparent. |
