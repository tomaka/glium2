# Textures

## Strong typing

Glium 1 had this weird idea to generate one texture type for each possible combination of
data type and dimensions.

There were two reasons for this API:

- Eventually it was planned to examine shaders at compile-time and use the strong typing to enforce
  the fact that for example an `isampler3D` must be of type `IntegerTexture3D`.
- Coordinates, colors, etc. are adjusted depending on the type of texture. For example if you
  wanted to upload to a texture 2D, the coordinates would be two-dimensional. 

In the end, I think this API had more disadvantages (it was confusing) than advantages. I think
that a single `Texture` type, similar to the unified `Buffer` type proposed in another file, would
be better.

## Implement everything immediately 

One mistake of glium 1 is that things were half-implemented when it comes to textures. This is
because it's a complex topic with lots of weird things to check.

Glium 2 should finish the whole API of textures quickly if possible.

## Bindless textures

Should not be supported in glium 2. Too recent.

## The Layer/Mipmap/LayerMipmap/Image API

For example, when you want to upload data to a texture, instead of calling `texture.upload(0, data)`
where `0` is the mipmap level, you would do `texture.mipmap(0).unwrap().upload(data)`. Or, for
mipmap level 0 specifically, you would do `texture.main_level().upload(data)`.

I think this is a good design, especially when it comes to attaching a texture to a framebuffer.
For example to attach a specific face of a cubemap, you can do something like
`Framebuffer::new(texture.first_layer().main_level().cube_front())`.

Because all texture types are unified in one, some more quick functions should be added. For
example we could add `texture.cube_front()` as a shortcut for
`texture.first_layer().main_level().cube_front()`.
