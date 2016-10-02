# Backend and context

The backend designates the object that provides the OpenGL context. In glium it is a trait of the
same name.

The context designates the backend plus all the states required by glium. In glium it is a struct
that holds a `Rc<Backend>`.

## Explicit Rc

In glium 1, the `GlutinFacade` type represents "the OpenGL context". It is created from a window
builder. What the users didn't necessarily realize is that a `GlutinFacade` contains in reality
a `Rc`. This object is meant to be cloned everywhere you needed it, but users had trouble grasping
that fact and would often have trouble because they tried to store references to the facade.

The reason why Glium 1 didn't make it explicitly an `Rc` is that `GlutinFacade` has methods that
need to clone that `Rc`. These members can be moved to the `Rc` by putting them on a trait and
implementing that trait on `Rc<Foo>`. I think that Glium 2 should make it explicit that it is in
fact an `Rc`, even if it means that they have to import an additional trait in scope in order to
use these members.

## Checking the context before each call

Before every single group of OpenGL functions, glium 1 makes sure that the OpenGL context is the
right one in the thread by calling a method on the `Backend`.

This should be kept in glium 2.

One small tweak is that we could remove `is_current` and ask the implementation to optimize
`make_current` by first checking whether the context is current. This would remove one virtual
function call.

## Thread safety and shared contexts

One common request with OpenGL is how to perform multithreaded operations.

While it is forbidden to access an OpenGL context from more than one thread, it is allowed to
create multiple contexts that share buffers and textures, thus making it possible for example to
upload to textures in thread and draw using these textures in another thread.

Since glium calls `make_current`/`is_current` every time, it should be possible to write an
implementation of `Backend` that automatically creates shared OpenGL contexts *on demand*.

There are two quirks with that:

1) The context should then be put in an `Arc` instead of an `Rc`.
2) Framebuffers, VAOs and some other similar objects can't be shared between contexts. This means
   that glium needs to be aware of which context is bound, and can't just rely on the fact that
   "a" context is bound.

TODO: finish
