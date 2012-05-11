# The Redbet Ruby Style Guide

Forked from https://github.com/bbatsov/ruby-style-guide

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
* [Annotations](#annotations)
* [Classes](#classes)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)

## Source Code Layout

* Use `UTF-8` as the source file encoding.
* Use two **spaces** per indentation level.
* Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)
    * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

        ```$ git config --global core.autocrlf true```

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    The only exception is when using the exponent operator:

    ```Ruby
    e = M * c**2
    ```

* No spaces after `(`, `[` or before `]`, `)`.
* Indent `when` as deep as `case`. I know that many would disagree
  with this one, but it's the style established in both the "The Ruby
  Programming Language" and "Programming Ruby".

    ```Ruby
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end

    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
    ```

* Use empty lines between `def`s and to break up a method into logical
  paragraphs.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

* Align the parameters of a method call if they span over multiple lines.

    ```Ruby
    # bad starting point (line is too long)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end

    # good
    def send_mail(source)
      Mailer.deliver(
        to:      'bob@example.com',
        from:    'us@example.com',
        subject: 'Important message',
        body:     source.text
      )
    end
    ```

* Use RDoc and its conventions for API documentation.  Don't put an
  empty line between the comment block and the `def`.
* Keep lines fewer than 80 characters.
* Avoid trailing whitespace.

## Syntax

* Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.
* Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.
* Never use `then` for multi-line `if/unless`.
* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.
* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.
* Never use `if x: ...` - it is removed in Ruby 1.9. Use
  the ternary operator instead.
* Never use `if x; ...`. Use the ternary operator instead.
* Use `when x then ...` for one-line cases. The alternative syntax
  `when x: ...` is removed in Ruby 1.9.
* Never use `when x; ...`. See the previous rule.
* Use `and/or` if precedence allows, else fall back to `&&/||`
* Avoid multi-line `?:` (the ternary operator), use `if/unless` instead.
* Favor modifier `if/unless` usage when you have a single-line
  body. Another good alternative is the usage of control flow `and/or`.
* Favor `unless` over `if` for negative conditions (or control
  flow `or`).
* Never use `unless` with `else`. Rewrite these with the positive case first.
* Don't use parentheses around the condition of an `if/unless/while`,
  unless the condition contains an assignment (see "Using the return
  value of `=`" below).
* Favor modifier `while/until` usage when you have a single-line
  body.
* Favor `until` over `while` for negative conditions.
* Omit parentheses around parameters for methods where it makes sense.
* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).  Avoid `do...end`
  when chaining.
* Avoid `return` where not required.
* Do not use spaces around the `=` operator when assigning default values
  to method parameters
* Use line continuation (\\) sparingly
* Use `||=` freely to initialize variables.
* Don't use `||=` to initialize boolean variables. (False values
would be set to true)
* Never put a space between a method name and the opening parenthesis.
* If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example, write
`f((3 + 2) + 1)`.
* Always run the Ruby interpreter with the `-w` option so it will warn
you if you forget either of the rules above!
* Use the new lambda literal syntax.

    ```Ruby
    # bad
    lambda = lambda { |a, b| a + b }
    lambda.call(1, 2)

    # good
    lambda = ->(a, b) { a + b }
    lambda.(1, 2)
    ```

* Use `_` for unused block parameters.

    ```Ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_, v| v + 1 }
    ```

## Naming

* Use `snake_case` for methods and variables.
* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* Use `SCREAMING_SNAKE_CASE` for other constants.
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).
* The names of potentially "dangerous" methods (i.e. methods that modify
 `self` or the arguments, `exit!` (doesn't run the finalizers like `exit` does), etc.)
 should end with an exclamation mark *if there exists a safe version*
 of that *dangerous* method.
* Define the non-bang (safe) method in terms of the bang (dangerous)
  one if possible.
* When using `reduce` with short blocks, name the arguments `|a, e|`
  (accumulator, element).
* When defining binary operators, name the argument `other`.
* Prefer `map` over `collect`, `find` over `detect`, `select` over
  `find_all`, `reduce` over `inject` and `size` over `length`. This is
  not a hard requirement; if the use of the alias enhances
  readability, it's ok to use it. The rhyming methods are inherited from
  Smalltalk and are not common in other programming languages. The
  reason the use of `select` is encouraged over `find_all` is that it
  goes together nicely with `reject` and its name is pretty self-explanatory.

## Comments

* Write self-documenting code and ignore the rest of this section. Seriously!
* Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments.

    ```Ruby
    # bad
    counter += 1 # increments counter by one
    ```

* Keep existing comments up-to-date. An outdated is worse than no comment
at all.

* Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. (Do or do not - there is no try. --Yoda)

* Avoid writing comments that states something is wrong but gives no
  additional information. Specify instead what should have been done,
  or better yet, do it!

    ```Ruby
    # bad
    # TODO: aaah ugly, delete me, I do not belong here
    def generic_method

    # good
    # TODO: Method should be moved to namespace A, this will
    # break lots of tests though and I dont have time now.
    def generic_method
    ```

## Annotations

* Annotations should usually be written on the line immediately above
  the relevant code.
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented two spaces after the `#`.

    ```Ruby
    def bar
      # FIXME: This has crashed occasionally since v3.2.1. It may
      #   be related to the BarBazUtil upgrade.
      baz(:quux)
    end
    ```

* In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.

    ```Ruby
    def bar
      sleep 100 # OPTIMIZE
    end
    ```

* Use `TODO` to note missing features or functionality that should be
  added at a later date.
* Use `FIXME` to note broken code that needs to be fixed.
* Use `OPTIMIZE` to note slow or inefficient code that may cause
  performance problems.
* Use `HACK` to note code smells where questionable coding practices
  were used and should be refactored away.
* Use `REVIEW` to note anything that should be looked at to confirm it
  is working as intended. For example: `REVIEW: Are we sure this is how the
  client does X currently?`
* Use other custom annotation keywords if it feels appropriate, but be
  sure to document them in your project's `README` or similar.

## Classes

* When designing class hierarchies make sure that they conform to the
  [Liskov Substitution Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle).
* Try to make your classes as
  [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design\))
  as possible.
* Always supply a proper `to_s` method for classes that represent
  domain objects.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Use the `attr` family of functions to define trivial accessors or
mutators.

    ```Ruby
    # bad
    class Person
      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def first_name
        @first_name
      end

      def last_name
        @last_name
      end
    end

    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ```
* Consider using `Struct.new`, which defines the trivial accessors,
constructor and comparison operators for you.

    ```Ruby
    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end

    # better
    class Person < Struct.new (:first_name, :last_name)
    end
    ````

* Consider adding factory methods to provide additional sensible ways
to create instances of a particular class.

    ```Ruby
    class Person
      def self.create(options_hash)
        # body omitted
      end
    end
    ```

* Prefer [duck-typing](http://en.wikipedia.org/wiki/Duck_typing) over inheritance.

    ```Ruby
    # bad
    class Animal
      # abstract method
      def speak
      end
    end

    # extend superclass
    class Duck < Animal
      def speak
        puts 'Quack! Quack'
      end
    end

    # extend superclass
    class Dog < Animal
      def speak
        puts 'Bau! Bau!'
      end
    end

    # good
    class Duck
      def speak
        puts 'Quack! Quack'
      end
    end

    class Dog
      def speak
        puts 'Bau! Bau!'
      end
    end
    ```

* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.

    ```Ruby
    class Parent
      @@class_var = 'parent'

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = 'child'
    end

    Parent.print_class_var # => will print "child"
    ```

    As you can see all the classes in a class hierarchy actually share one
    class variable. Class instance variables should usually be preferred
    over class variables.

* Assign proper visibility levels to methods (`private`, `protected`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default).
* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line around them.

    ```Ruby
    class SomeClass
      def public_method
        # ...
      end

      private

      def private_method
        # ...
      end
    end

* Use `def self.method` to define singleton methods instead of using
  the class/module name. This makes the methods more resistant to
  refactoring changes.

## Exceptions

* Signal exceptions using the `fail` keyword. Use `raise` only when
  catching an exception and re-raising it (because here you're not failing, but explicitly and purposefully raising an exception).

    ```Ruby
    begin
      fail 'Oops';
    rescue => error
      raise if error.message != 'Oops'
    end
    ```

* Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

    ```Ruby
    def foo
      begin
        fail
      ensure
        return 'very bad idea'
      end
    end
    ```

* Use *implicit begin blocks* when possible.

    ```Ruby
    # bad
    def foo
      begin
        # main logic goes here
      rescue
        # failure handling goes here
      end
    end

    # good
    def foo
      # main logic goes here
    rescue
      # failure handling goes here
    end
    ```

* Mitigate the proliferation of `begin` blocks via the use of
  *contingency methods* (a term coined by Avdi Grimm).

    ```Ruby
    # bad
    begin
      something_that_might_fail
    rescue IOError
      # handle IOError
    end

    begin
      something_else_that_might_fail
    rescue IOError
      # handle IOError
    end

    # good
    def with_io_error_handling
       yield
    rescue
      # handle IOError
    end

    with_io_error_handling { something_that_might_fail }

    with_io_error_handling { something_else_that_might_fail }
    ```

* Don't suppress exceptions.

    ```Ruby
    # bad
    begin
      # an exception occurs here
    rescue SomeError
      # the rescue clause does absolutely nothing
    end

    # bad
    do_something rescue nil
    ```

* Don't use exceptions for flow of control.

    ```Ruby
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts 'Cannot divide by 0!'
    end

    # good
    if d.zero?
      puts 'Cannot divide by 0!'
    else
      n / d
    end
    ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

    ```Ruby
    # bad
    begin
      # calls to exit and kill signals will be caught (except kill -9)
      exit
    rescue Exception
      puts "you didn't really want to exit, right?"
      # exception handling
    end

    # good
    begin
      # a blind rescue rescues from StandardError, not Exception as many
      # programmers assume.
    rescue => e
      # exception handling
    end

    # also good
    begin
      # an exception occurs here

    rescue StandardError => e
      # exception handling
    end

    ```

* Put more specific exceptions higher up the rescue chain, otherwise
  they'll never be rescued from.

    ```Ruby
    # bad
    begin
      # some code
    rescue Exception => e
      # some handling
    rescue StandardError => e
      # some handling
    end

    # good
    begin
      # some code
    rescue StandardError => e
      # some handling
    rescue Exception => e
      # some handling
    end
    ```

* Release external resources obtained by your program in an ensure
block.

    ```Ruby
    f = File.open('testfile')
    begin
      # .. process
    rescue
      # .. handle error
    ensure
      f.close unless f.nil?
    end
    ```

## Collections

* Prefer literal array and hash creation notation (unless you need to
pass parameters to their constructors, that is).

    ```Ruby
    # bad
    arr = Array.new
    hash = Hash.new

    # good
    arr = []
    hash = {}
    ```

* Prefer `%w` to the literal array syntax when you need an array of
strings.

    ```Ruby
    # bad
    STATES = ['draft', 'open', 'closed']

    # good
    STATES = %w(draft open closed)
    ```

* Avoid the creation of huge gaps in arrays.

    ```Ruby
    arr = []
    arr[100] = 1 # now you have an array with lots of nils
    ```

* Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.
* Use symbols instead of strings as hash keys.
* Avoid the use of mutable object as hash keys.
* Never modify a collection while traversing it.

## Strings

* Prefer string interpolation instead of string concatenation:

    ```Ruby
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

* Prefer single-quoted strings when you don't need string interpolation or
  special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

* Don't use `{}` around instance variables being interpolated into a
  string.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      # bad
      def to_s
        "#{@first_name} #{@last_name}"
      end

      # good
      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```Ruby
    # good and also fast
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

## Regular Expressions

* Don't use regular expressions if you just need plain text search in string:
  `string['text']`
* For simple constructions you can use regexp directly through string index.

    ```Ruby
    match = string[/regexp/]             # get content of matched regexp
    first_group = string[/text(grp)/, 1] # get content of captured group
    string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
    ```

* Use non capturing groups when you don't use captured result of parenthesis.

    ```Ruby
    /(first|second)/   # bad
    /(?:first|second)/ # good
    ```

* Avoid using $1-9 as it can be hard to track what they contain. Named groups
  can be used instead.

    ```Ruby
    # bad
    /(regexp)/ =~ string
    ...
    process $1

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

* Character classes have only few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.

* Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

    ```Ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```Ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

* For complex replacements `sub`/`gsub` can be used with block or hash.

## Percent Literals

* Use `%w` freely.

    ```Ruby
    STATES = %w(draft open closed)
    ```

* Use `%()` for single-line strings which require both interpolation
  and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```Ruby
    # bad (no interpolation needed)
    %(<div class="text">Some text</div>)
    # should be '<div class="text">Some text</div>'

    # bad (no double-quotes)
    %(This is #{quality} style)
    # should be "This is #{quality} style"

    # bad (multiple lines)
    %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    # should be a heredoc.

    # good (requires interpolation, has quotes, single line)
    %(<tr><td class="name">#{name}</td>)
    ```

* Use `%r` only for regular expressions matching *more than* one '/' character.

    ```Ruby
    # bad
    %r(\s+)

    # still bad
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

## Metaprogramming

* Do not mess around in core classes when writing libraries. (Do not monkey
patch them.)

* The block form of `class_eval` is preferable to the string-interpolated form,
  unless dealing with defined constants.
  - when you use the string-interpolated form, always supply `__FILE__` and `__LINE__`, so that your backtraces make sense:

    ```ruby
    class_eval 'def use_relative_model_naming?; true; end', __FILE__, __LINE__
    ```

  - `define_method` is preferable to `class_eval{ def ... }`

* When using `class_eval` (or other `eval`) with string interpolation, add a comment block showing its appearance if interpolated (a practice I learned from the rails code):

    ```ruby
    # from activesupport/lib/active_support/core_ext/string/output_safety.rb
    UNSAFE_STRING_METHODS.each do |unsafe_method|
      if 'String'.respond_to?(unsafe_method)
        class_eval <<-EOT, __FILE__, __LINE__ + 1
          def #{unsafe_method}(*args, &block)       # def capitalize(*args, &block)
            to_str.#{unsafe_method}(*args, &block)  #   to_str.capitalize(*args, &block)
          end                                       # end

          def #{unsafe_method}!(*args)              # def capitalize!(*args)
            @dirty = true                           #   @dirty = true
            super                                   #   super
          end                                       # end
        EOT
      end
    end
    ```

* avoid using `method_missing` for metaprogramming. Backtraces become messy; the behavior is not listed in `#methods`; misspelled method calls might silently work (`nukes.launch_state = false`). Consider using delegation, proxy, or `define_method` instead.  If you must, use `method_missing`,
  - be sure to [also define `respond_to?`](http://devblog.avdi.org/2011/12/07/defining-method_missing-and-respond_to-at-the-same-time/)
  - only catch methods with a well-defined prefix, such as `find_by_*` -- make your code as assertive as possible.
  - call `super` at the end of your statement
  - delegate to assertive, non-magical methods:

    ```ruby
    # bad
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        # ... lots of code to do a find_by
      else
        super
      end
    end

    # good
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        find_by(prop, *args, &block)
      else
        super
      end
    end

    # best of all, though, would to define_method as each findable attribute is declared
    ```

## Misc

* Write `ruby -w` safe code.
* Avoid hashes as optional parameters. Does the method do too much?
* Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be shorter than
  5 LOC. Empty lines do not contribute to the relevant LOC.
* Avoid parameter lists longer than three or four parameters.
* If you really have to, add "global" methods to Kernel and make them private.
* Use class instance variables instead of global variables.

    ```Ruby
    #bad
    $foo_bar = 1

    #good
    class Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Avoid `alias` when `alias_method` will do.
* Use `OptionParser` for parsing complex command line options and
`ruby -s` for trivial command line options.
* Code in a functional way, avoiding mutation when that makes sense.
* Avoid needless metaprogramming.
* Do not mutate arguments unless that is the purpose of the method.
* Avoid more than three levels of block nesting.
* Be consistent. In an ideal world, be consistent with these guidelines.
* Use common sense.
