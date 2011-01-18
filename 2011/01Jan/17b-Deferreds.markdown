This post`[1]` about `jQuery/dojo.Deferred`. just caught my eye.

> Through all of the conversations I've been part of, it's become really clear that no one cares about deferreds until you show them what they actually mean: the ability to register an interest in the outcome of arbitrary asynchronous behavior, *even if the outcome has already occurred*. Even better, you can register your interest in the outcome of behavior that *may or may not be asynchronous*.

I am somewhat surprised that jQuery did not already have this abstract behaviour built-in, because I took my cues from it when implementing something like this yesterday!

I had an asynchronous event which my code depended on to work. `jQuery()` allows you to defer a call until the DOM is ready; I made a function that allowed you to create events that behaved similarly. Not being fluent in the relevant vocabulary, I fished around for a name until I settled unsatisfied on the name `precondition`.

For a simple usage example, If I were trying to create a `precondition` of the `window`'s `load` event having fired I would do it like this:

    #!javascript
    loaded = precondition();
    window.onload = loaded.trigger;
    loaded(function() {
      console.log("The document is loaded now.");
    });

If the event had not yet fired the function would be queued until it had. If the function had already fired then the function would be executed immediately. It doesn't handle errors or anything, that wasn't yet necessary for what I was using it for. The implementation (in CoffeeScript) was simply this:

    #!coffeescript
    precondition = ->
      buffer = []
      
      condition = (f) ->
        if buffer?
            buffer.push f
          else
            f()
        
        not buffer?
        
      condition.trigger = ->
        f() for f in buffer
        
        n = buffer.length
        delete buffer
        
        n
      
      condition

I like the interface proposed for CommonJS`[2]`. If I do make a standard JavaScript/CoffeeScript library for myself I'll probably include a `Promise` class.

**Meta PS**: ...and immediately this blog has taken a dip in quality. Maybe "explanations" was an ambitious name, "musings" might have been more appropriate.

---

`[1]` [http://blog.rebeccamurphey.com/deferreds-coming-to-jquery](http://blog.rebeccamurphey.com/deferreds-coming-to-jquery)

`[2]` [http://wiki.commonjs.org/index.php?title=Promises/A&oldid=3084](http://wiki.commonjs.org/index.php?title=Promises/A&oldid=3084)
