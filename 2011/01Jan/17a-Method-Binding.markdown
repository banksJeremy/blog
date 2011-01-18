> **[@jeremybanks](http://twitter.com/jeremybanks/status/27022654170996736)**  
> A language should have two operations for method lookup; one binding (like Python) and one not (like ECMAScript`[1]`). But no spare characters...

In Python `instance.method` returns a copy of a method **bound** to a given instance`[2]`. That is to say, I can enter

    #!python
    x = [1, 2, 3]
    f = x.pop
    print f()

and it will `pop()` `3` off of the list and print it. Even thought I don't specify the instance `x` in the call the bound method `f` has carried it along.

If I tried the same thing in ECMAScript it would not work. The method is only associated with an instance within the expression `instance.method(...)` and if called anywhere else `this` assumes a default value.

Both of these behaviours are useful. If only one is available I'd prefer ECMAScript, because then the behaviour is more easily predictable: it would take a few minutes to figure out how to even get an unbound method from an object in Python. It would still be nice to have both easily available. EMCAScript 5 and some modern JavaScript frameworks such as [Prototype](http://www.prototypejs.org/) add a `.bind(instance)` method to `Function`s, but it's still not as clean as having an operator.

### Current Behaviour

### Bound Call

- **Both**: `instance.method(arguments...)`


### Unbound Call

- **ECMAScript**: `instance.method.call($this, arguments...)`
- **Python** (version 3): `instance.method.__func__($this, arguments...)`

#### Bound Reference

- **ECMASCript** (where supported): `instance.method.bind(instance)`
- **Python**: `instance.method`

#### Unbound Reference

- **ECMAScript**: `instance.method`
- **Python** (version 3): `instance.method.__func__`

### Possibilities

- Maybe we could use the `->` symbol that C uses for structure pointer lookup, such that `instance->method` is binding and `instance.method` is not. `instance.method()` wouldn't define `this`, you would need to use `instance->method()`. This would make the difference clearly-defined and all behaviour explicit but add one more thing someone using the language need to know.
- Alternately we could adopt Python's behaviour as the default and have a new operator for unbound access. Since neither language permits its use for any other purpose, `!` is a possible choice. `instance.method()` works as it currently does in both, `instance.method` returns a bound method and `instance!method` could be used to access/call a method unbound.
- Ruby's behaviour is like Python's, except that it adds `.bind()` and `.unbind()` methods with the classes `Method`/`UnboundMethod`. This is an improvement from Python where there's no friendly way to manage bindings explicitly, but the inconsistent behaviour based on instance type is still unfortunate.

It's unfortunate that JavaScript lacks the ability to apply chances to a class within a context like Ruby`[3]` and Clojure do. Adding a `.bind() `method to `Function` is an acceptable solution that preserves linguistic simplicity, but until the far-future when that's widely supported the risk of breaking things by messing with global types is unacceptable to people. _De facto_ standardization might allay these fears before too long but I'm not sure if we're there yet.

**PS:** This started as a rambly email elaborating on a tweet, but as it grew I decided I really should put up a simple unpublicized. I was trying posterous
but since it didn't track revisions I'm back in git.

---

`[1]`: ECMAScript is a standardized scripting language of which JavaScript is a dialect. I prefer this term when discussing items that are not JavaScript-specific.

`[2]` This is the case for attribute lookup on instances of classes, which is what we are primarily discussing. Attribute lookup on modules and other types of objects may behave differently.

`[3]` I think Ruby supports it. I haven't tried.
