---
layout: default
title: 
tagline: Modern concurrency tools for Ruby
---
{% include JB/setup %}

Modern concurrency tools for Ruby. Inspired by
[Erlang](http://www.erlang.org/doc/reference_manual/processes.html),
[Clojure](http://clojure.org/concurrent_programming),
[Scala](http://www.scala-lang.org/api/current/index.html#scala.actors.Actor),
[Haskell](http://www.haskell.org/haskellwiki/Applications_and_libraries/Concurrency_and_parallelism#Concurrent_Haskell),
[F#](http://blogs.msdn.com/b/dsyme/archive/2010/02/15/async-and-parallel-design-patterns-in-f-part-3-agents.aspx),
[C#](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx),
[Java](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/package-summary.html),
and classic concurrency patterns.

The design goals of this gem are:

* Stay true to the spirit of the languages providing inspiration
* But implement in a way that makes sense for Ruby
* Keep the semantics as idiomatic Ruby as possible
* Support features that make sense in Ruby
* Exclude features that don't make sense in Ruby
* Be small, lean, and loosely coupled

## Features & Documentation

* Clojure-inspired [Agent](https://github.com/jdantonio/concurrent-ruby/blob/master/md/agent.md)
* Clojure-inspired [Future](https://github.com/jdantonio/concurrent-ruby/blob/master/md/future.md)
* Scala-inspired [Actor](https://github.com/jdantonio/concurrent-ruby/blob/master/md/actor.md)
* JavaScript-inspired [Promise](https://github.com/jdantonio/concurrent-ruby/blob/master/md/promise.md)
* Java-inspired [Thread Pools](https://github.com/jdantonio/concurrent-ruby/blob/master/md/thread_pool.md)
* Old school [events](http://msdn.microsoft.com/en-us/library/windows/desktop/ms682655.aspx) from back in my Visual C++ days
* Repeated task execution with Java-inspired [TimerTask](https://github.com/jdantonio/concurrent-ruby/blob/master/md/timer_task.md) service
* Scheduled task execution with Java-inspired [ScheduledTask](https://github.com/jdantonio/concurrent-ruby/blob/master/md/scheduled_task.md) service
* Erlang-inspired [Supervisor](https://github.com/jdantonio/concurrent-ruby/blob/master/md/supervisor.md) for managing long-running threads
* Actor variant [Channel](https://github.com/jdantonio/concurrent-ruby/blob/master/md/channel.md)
  loosely based on the [MailboxProcessor](http://blogs.msdn.com/b/dsyme/archive/2010/02/15/async-and-parallel-design-patterns-in-f-part-3-agents.aspx)
  agent in [F#](http://msdn.microsoft.com/en-us/library/ee370357.aspx)
  
  ### Semantic Versioning

  *Beginning with v0.4.0 this gem will strictly adhere to the rules of semantic versioning.*

  ### Supported Ruby versions

  MRI 1.9.2, 1.9.3, 2.0, 2.1, and JRuby (1.9 mode). This library is pure Ruby and has no gem dependencies.
  It should be fully compatible with any Ruby interpreter that is 1.9.x compliant.

  ### Example

  Many more code examples can be found in the documentation for each class (linked above).
  This one simple example shows some of the power of this gem.

  ```ruby
  require 'concurrent'
  require 'faker'

  class EchoActor < Concurrent::Actor
    def act(*message)
      puts "#{message} handled by #{self}"
    end
  end

  mailbox, pool = EchoActor.pool(5)

  timer_proc = proc do
    mailbox.post(Faker::Company.bs)
  end

  t1 = Concurrent::TimerTask.new(execution_interval: rand(5)+1, &timer_proc)
  t2 = Concurrent::TimerTask.new(execution_interval: rand(5)+1, &timer_proc)

  overlord = Concurrent::Supervisor.new

  overlord.add_worker(t1)
  overlord.add_worker(t2)
  pool.each{|actor| overlord.add_worker(actor)}

  overlord.run!

  #=> ["mesh proactive platforms"] handled by #<EchoActor:0x007fa5ac18bdf8>
  #=> ["maximize sticky portals"] handled by #<EchoActor:0x007fa5ac18bdd0>
  #=> ["morph bleeding-edge markets"] handled by #<EchoActor:0x007fa5ac18bd80>
  #=> ["engage clicks-and-mortar interfaces"] handled by #<EchoActor:0x007fa5ac18bd58>
  #=> ["monetize transparent infrastructures"] handled by #<EchoActor:0x007fa5ac18bd30>
  #=> ["morph sexy e-tailers"] handled by #<EchoActor:0x007fa5ac18bdf8>
  #=> ["exploit dot-com models"] handled by #<EchoActor:0x007fa5ac18bdd0>
  #=> ["incentivize virtual deliverables"] handled by #<EchoActor:0x007fa5ac18bd80>
  #=> ["enhance B2B models"] handled by #<EchoActor:0x007fa5ac18bd58>
  #=> ["envisioneer real-time architectures"] handled by #<EchoActor:0x007fa5ac18bd30>

  overlord.stop
  ```

  ### Disclaimer

  Remember, *there is no silver bullet in concurrent programming.* Concurrency is hard.
  These tools will help ease the burden, but at the end of the day it is essential that you
  *know what you are doing.*

  * Decouple business logic from concurrency logic
  * Test business logic separate from concurrency logic
  * Keep the intersection of business logic and concurrency and small as possible
  * Don't share mutable data unless absolutely necessary
  * Protect shared data as much as possible (prefer [immutability](https://github.com/harukizaemon/hamster))
  * Don't mix Ruby's [concurrency](http://ruby-doc.org/core-2.0.0/Thread.html)
    [primitives](http://www.ruby-doc.org/core-2.0.0/Mutex.html) with asynchronous concurrency libraries

  ### Conference Presentations

  * ["Advanced Concurrent Programming in Ruby"](http://rubyconf.org/program#jerry-dantonio)
    at [RubyConf 2013](http://rubyconf.org/)
    used [this](https://github.com/jdantonio/concurrent-ruby-presentation) version of the presentation
    and is available for viewing on [Confreaks](http://www.confreaks.com/videos/2872-rubyconf2013-advanced-concurrent-programming-in-ruby)
  * ["Advanced Multithreading in Ruby"](http://cascadiaruby.com/#advanced-multithreading-in-ruby)
    at [Cascadia Ruby 2013](http://cascadiaruby.com/)
    used [this](https://github.com/jdantonio/concurrent-ruby-presentation/tree/cascadia-ruby-2013) version of the presentation
    and is available for viewing on [Confreaks](http://www.confreaks.com/videos/2790-cascadiaruby2013-advanced-multithreading-in-ruby)
  * [Cleveland Ruby Brigade](http://www.meetup.com/ClevelandRuby/events/149981942/) meetup in December of 2013
    used [this](https://github.com/jdantonio/concurrent-ruby-presentation/releases/tag/clerb-dec-2013) version of the presentation
  * I'll be giving ["Advanced Concurrent Programming in Ruby"](http://codemash.org/sessions)
    at [CodeMash 2014](http://codemash.org/)

  ## License and Copyright

  *Concurrent Ruby* is Copyright &copy; 2013 [Jerry D'Antonio](https://twitter.com/jerrydantonio).
  It is free software released under the [MIT License](http://www.opensource.org/licenses/MIT).