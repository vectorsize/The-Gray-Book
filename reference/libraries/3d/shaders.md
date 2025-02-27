# Shaders
Shaders are written in [SDSL](https://doc.stride3d.net/4.0/en/manual/graphics/effects-and-shaders/shading-language/index.html), an advanced high-level shader language that supports OOP concepts and multiple inheritance. This allows to write short and nice looking shader code.

## File Management
### Scope
Any .vl document that has VL.Stride set as a dependency will pick up shader files that are placed next to it in special folder called "shaders". Multiple .vl documents can share the same shaders folder.

> [!NOTE]
> Shader files share a global scope, two files with the same name are not allowed, even if they are referenced by two different .vl documents.

### Special Suffixes
If a shader file ends with one of the pre-defined suffixes, the shader will be converted into a VL node.
#### _ShaderFX
A node that just represents "a piece of code" that can be used to compose larger shaders. This is the most felxible type of node, it can work together all other shader node types.
#### _DrawFX
A node that can be used to draw geometry.
#### _ComputeFX
A node that represents a compute shader to work with arbitrary data on the GPU.
#### _TextureFX
Specialized nodes to process textures. See more in the chapter [TextureFX](texturefx.md).

## Core Concepts
### Includes and Static Calls
You can use the [#include directive](https://docs.microsoft.com/en-us/windows/win32/direct3dhlsl/dx-graphics-hlsl-appendix-pre-include) just as you would in HLSL. But often you'll not need it because you can call a static function of any shader that is in [scope](#scope) (e.g in the same directory, or both in the /shaders folder next to any .vl document that is loaded). Static functions are functions that don't use any stream variables or class variables, like shader inputs. See also [Static Calls](https://doc.stride3d.net/latest/en/manual/graphics/effects-and-shaders/shading-language/shader-classes-mixins-and-inheritance.html#static-calls) in the Stride documentation.

If you have a file `MyUtils.sdsl` like this:
```c
shader MyUtils
{
    float4 Invert(float4 col)
    {
        col.rgb = 1 - col.rgb;
        return col;
    }
};
```

You can call its static functions in another file like so:

```c
shader MyFx_TextureFX : FilterBase
{
    float4 Filter(float4 tex0col)
    {
        return MyUtils.Invert(tex0col);
    }
};
```

### Inheritance
The main purpose of inherance is re-using existing shader code. You can think of it like importing or including the code of another shader into your own shader. 

For examples, see [Inheritance](https://doc.stride3d.net/latest/en/manual/graphics/effects-and-shaders/shading-language/shader-classes-mixins-and-inheritance.html#example-code-inheritance) in the Stride documentation.

To understand the shader inheritance hierachy better, you can use the [Stride.ShaderExplorer](../graphics-3d.md#useful-tools) to get an overview and browse the shaders.

### Composition
Compostion allows a shader A to use other shader B like a variable and call functions of it. The main feature is, that the other shaders C or D can be used as composition if they inhertit from the shader class B, that is expected as composition variable in shader A. The fact that you can use different implementations (shaders that inherit from B) as the composition, allows for polymorphism known as __interfaces__ in OOP languages.

For examples, see [Composition](https://doc.stride3d.net/latest/en/manual/graphics/effects-and-shaders/shading-language/composition.html) in the Stride documentation.

### Streams
SDSL has a convenient way to pass parameters across the different stages of your shader. Simply declare a variable as stream variable and write and read to it in any shader stage. The SDSL compiler will generate the input and output structs for each shader stage.

For examples, see [Automatic shader stage input/output](https://doc.stride3d.net/latest/en/manual/graphics/effects-and-shaders/shading-language/automatic-shader-stage-input-output.html) in the Stride documentation.
