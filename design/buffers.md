# Buffers

Glium 1 had an interesting way to handle buffers, as each kind of buffer had its own type:

- Uniform buffers.
- Vertex buffers.
- Index buffers.
- and so on...

Glium 2 should make this simpler by having just one buffer type, named `Buffer`, whose template
parameter corresponds to the content of the buffer. If the buffer contains an array of elements
(like it's usually the case for vertex buffers for example), then the template parameter would be
`[T]`.

The problem of this approach is that glium 1 used to store the type of indices in the index buffer.
TODO find a nice API for this

## Strong typing vs weak typing

Glium 1 proposed a `BufferAny` type whose advantage is that it didn't have any template parameter
representing the content. This means that you could store multiple different buffers with multiple
different contents in the same Vec for example.

In practice I don't think it's a big advantage to be able to do that, and it makes the internals
of glium harder to understand. I suggest that this gets dropped entirely.

## Buffer slices

I think the `BufferSlice` system worked very well in glium 1.

Basically every single function that takes a `Buffer` takes a `T where T: Into<BufferSlice>`.
This means that you can pass only a subpart of a buffer to a function.

The drawback is things are slightly more complex, and the error message is not great if you happen
to pass a buffer by value instead of by reference.

## Resizable vs non-resizable

A frequently asked question of glium 1 is how to resize a buffer.

Since resizing a buffer requires invalidating its old content, the only advantage of resizing an
old buffer compared to creating a new one is slightly less overhead.

To fix that, we could add a constructor for `Buffer` that takes an old buffer by value and re-uses
it.

Other than that, I don't think it makes sense to allow resizing a buffer in glium.

## VAOs

:-/
