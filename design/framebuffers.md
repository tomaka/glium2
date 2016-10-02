# Framebuffers

## Underlying implementation

Framebuffers are stored in a hashmap in the context object. The key of the hashmap is the list
of the attachments.

Whenever the user creates a framebuffer, glium tries to find an already existing framebuffer in
that hashmap. If it finds one, it can return early. Otherwise glium performs the validation,
creates a framebuffer object, and adds it to the hashmap.

Therefore the cost of "creating" a framebuffer in glium is very low, provided that you have already
built the same framebuffer in the past. In other words, users should feel free to create and
destroy framebuffers whenever they want.

## Framebuffers lifetime

A framebuffer should be destroyed before any of its attachment is destroyed. There are basically
two ways to do that: either use Rust's lifetime system, or use reference-counted pointers to store
the attachments in the framebuffer.

The first solution is overall better, but has the drawback that it's hard to keep the framebuffer
alive. However since framebuffers have a low creation cost, the user should simply destroy and
recreate framebuffers whenever they are needed, instead of trying to keep a framebuffer alive for
more than one frame for example. 

## Framebuffer types

In glium 1 there are three types that are actually framebuffers: `SimpleFramebuffer` (for zero or
one color attachment), `MultiOutputFramebuffer` (for two or more color attachments) and
`EmptyFramebuffer` (for no attachment at all). They should be merged in just one type (with
multiple different constructors) in order to be less confusing for the user.

The biggest challenge will be to make the API convenient enough to use despite all the different
possible ways to create a framebuffer.

## As_surface()

Glium 1 has a `as_surface()` method on all textures that creates a framebuffer with the texture 
as a color attachment. This method was well-received by the users as it makes it very easy to
quickly draw to a texture (for example, clearing it). This method should be kept in glium 2, but
renamed to `as_framebuffer()` to make it clearer that it is just a shortcut for a framebuffer
creation. 

## The default framebuffer

## Blitting
