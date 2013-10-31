# Vienna BEAM Users

What happens when you take languages like ruby, python, java and remove stuff?

- shared / mutable state (The ene)
- locks, mutexes & semaphores
- objects & methods
- classes & mixins

ends up giving:

- very small core language to learn
- a very stable VM

Erlang is a significantly simpler & smaller language to learn than Javascript, Ruby, or Java. And yet  without these features it is capable of some quite amazing things.

All languages implemented on Erlang VM (called BEAM henceforth) are inherently concurrent, dynamic, and yet we have success typing, and property-based testing.

What does that mean?

http://jlouisramblings.blogspot.co.at/2012/12/hacking-brains-of-other-people-with-api.html

## Four Key Examples

1. Thread Safety

If your threads aren't safe, then how can you take advantage of multi-core processors?

http://www.jstorimer.com/pages/ruby-core-classes-arent-thread-safe

2. Enemy of the State

The core of Elixir, Erlang, and all other languages running on the BEAM VM is that there is *no* shared state. Communication takes place between processes (like green threads) only by message passing. The language appears as if messages are always copied, but under the hood, it's still implemented efficiently using a variety of heaps and referenced structures. This makes thread safety something you'll never think about again.

And if everything is implemented using separate lightweight (280 *bytes*) processes, and message passing, then by definition there's no memory corruption or inter-process crash risk.

So instead of storing state externally in a message queue (rabbit/zero/activemq), or a db (redis/couchdb/postgres) you can store it directly in memory. And instead of serialising & reserialising your data between JSON, native in-memory structures, text for redis cache or on-disk/db storage, you can keep it all in a single, native format, within the BEAM Virtual Machine. Say goodbye to impedance mismatch & race conditions?!

http://jlouisramblings.blogspot.co.at/2012/05/more-on-erlang-and-state.html

3. Handling Errors

Most Erlang programmers code defensively only at the outer edge of their programmes - direct user input, HTTP layer & API services, or loading data sets from disk. The rest of the time we just let it crash. This is possible because of 2 key points;

- no shared state between processes
- error handling is implemented separately in a different process to the crashing one

This sounds crazy. But it's true. Let's look at the example of a RESTful API, where you start getting calls from a customer who can't connect. In Erlang, we can trace in real-time on *specific* parameters to a function or modules, and trace the flow of data between processes. Without restarting, re-instrumenting, or causing the VM to explode.

We can see that the customer is using a different HTTP agent than other folk, and that the HTTP parser is not handling a specific parameter. As a result the parser process (which is a dedicated process for that specific request) is crashing, although none of the other active users are impacted by this failure. The fix is as simple as adding an additional clause to the parser, and then hot loading the new code into the API without disrupting other users. 

4. No Event Loop

Event loop based languages like nodejs end up with callback hell, or 

http://ghcmutterings.wordpress.com/2009/10/06/parallelism-concurrency/

# When to use Elixir/Erlang (or not)

- when restarts are no longer an option
- when your in-memory cache can't be efficiently reloaded
- when in-app state is critical
- when you really need concurrency & multicore efficiency
- when you need live debugging in real time
- when you realise you're really just storing everything in redis
- Don't decide to rewrite everything Just For Fun()

## Good Stuff

- Erlang is for Operators
    - supervisors let you sleep in late
    - hot code loading
    - software distribution to clusters
    - versioned libraries by default
    - real-time debugging of state & processes

## Bad Stuff

- everything's a hammer
    - single mailbox queue actor model
    - what, no queues? stdlib
    - and dictionaries / hashes? stdlib
- weird syntax
    - get out of your shell, or find a different career
    - many families - C-like, Lisp, Prolog, Forth ...
    - after syntax comes semantics & design patterns
- not a perfect unix citizen
    - mostly stuff Just Works
    - managing parent processes is yuck (heart vs runit/upstart etc)
    - OOM killer on linux is a PITA
    - managing child processes has some edge cases
- package management
    - basically none, build tools are slowly addressing this
        - mix, rebar, erlang.mk, relx
- community libraries tend to focus around distributed functions, templates, long-lived servers
    - much of the validation & extension libraries you'd expect to find are absent, partially due to tuples & pattern matching, and partly because they're not easily separable
        
    http://people.ku.edu/~nkinners/LangList/Extras/langlist.htm


# Credits

http://youtu.be/rCAYto7Svwo
http://youtu.be/8IpkgBfaZU0
http://thisotplife.tumblr.com/post/58707874670/when-someone-tells-you-you-should-use-node-js-instead
http://www.youtube.com/watch?v=olDVtmTSLIQ
http://2.bp.blogspot.com/-V8eLcbdEtoQ/TZoe1kGj97I/AAAAAAAAChw/eu44dp-B3a8/s1600/CIA%2BSpecial%2BOperator.jpg
http://youtu.be/1EBfxjSFAxQ#t=4
http://www.endlessvideo.com/watch?v=1EBfxjSFAxQ&start=4s&end=14s

debugging:
http://jlouisramblings.blogspot.co.at/2010/11/tracing-erlang-programs-for-fun-and.html
http://jlouisramblings.blogspot.co.at/2013/01/how-erlang-does-scheduling.html
http://ghcmutterings.wordpress.com/2009/10/06/parallelism-concurrency/

threadsafe:

http://yehudakatz.com/2010/08/14/threads-in-ruby-enough-already/
http://merbist.com/2011/02/22/concurrency-in-ruby-explained/

race conditions

http://www.rubyinside.com/does-the-gil-make-your-ruby-code-thread-safe-6051.html
