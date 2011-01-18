I've been thinking today about how to describe mathematical relationships between values, and work out unknowns from constant. I'd previously been thinking about approaching this problem by building up this elaborate model of equations, but a simpler approach has occurred to me.

I'm just going to define operations with a constant values. For example, we could define a constant product of three variables `a`, `b` and `c` with a value of `1`.

    1 = abc

What do we do if we find out that `a = 2`?

    1/2 = bc
    .5 = bc

Now `b = 4`:

    .5/4 = c
    .125 = c

That is to say: When we get a value for a variable we remove the variable and divide our constant by the value. When we have only a single variable remaining we assign it the value of the constant.

This works fine for addition as well. As we find new values in the sum we subtract them from the constant.

How do I put this into code? The first thing I need is an encapsulated variable that the relationships can reference. I've created a simple function `ref``[1]` for producing functions that serve this role:

    #!coffeescript
    x = mug.ref 2
    print x() # prints 2
    x 5
    print x() # prints 5

This simple interface lets us define variables that refer to each other. I'd like to have a function `reciprocal` which will behave as the reciprocal of another variable. If either of them acquires a value so will the other.

    reciprocal = (variable) -&gt;
      f = -&gt;
        if arguments.length
          f.value = arguments[0]
          variable 1 / f.value
        
        if not f.value? and variable()?
          f.value = 1 / variable()
        
        f.value
    
Just like that. Given a `ConstantProduct` class we can now represent _f = ma_:

    #!coffeescript
    f = ref()
    m = ref()
    a = ref()
    
    product = new ConstantProduct 1, [f, reciprocal(m), reciprocal(a)]

The relationship interface is just a nullary method `.relate()` makes all possible conclusions from known values.

---
`[1]` I'm making a module named "mug" containing general functions I may often want to have available.
