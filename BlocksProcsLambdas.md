#Blocks, Procs, and Lambdas

##Blocks

##Procs

##Lambdas

##Procs vs. Lambdas
###Arity
Lambdas care about how many arguments they are given. Procs don't.

###Return syntax
In short, one can return from a proc, but one shouldn't return from a lambda.

The examples below use the following method to run a block:
````ruby
def runproc(proc)
    puts "Starting to run..."
	proc.call
	puts "Finished!"
end
````

This method is then used to run two lines of code. The first is a lambda and the second is a proc.
````ruby
runproc(lambda {puts "I'm a lambda."; return})
runproc(proc {puts "I'm a proc."; return})
````
producing this output:
````
Starting to run...
I'm a lambda.
Finished!
Starting to run...
I'm a proc.
bplexamples.rb:8:in `block in <main>': unexpected return (LocalJumpError)
    from bplexamples.rb:3:in `call'
	from bplexamples.rb:3:in `runproc'
	from bplexamples.rb:8:in `<main>'
````

The error is produced because calling `return` from a proc will make it attempt to return from the main context, which we cannot do. This produces an error.

To make this clear, consider what happens when we run the same two statements from within the program. We can exit from the context of a program, so no error is produced.

````ruby
def our_program
    run_a_proc lambda {puts "I'm a lambda"; return}
	run_a_proc proc {puts "I'm a proc"; return}
end
````
````
Starting to run...
I'm a lambda
Finished!
Starting to run...
I'm a proc
````

##Closures

###Closures maintain references to enclosed variables, rather than their values
When variables are saved in a closure, Ruby only saves references to them, rather than their values. This means that when the objects they reference change, a closure will use the changed reference.

Example:

````ruby
def run_proc(proc)
    p.call
end

name = "Fred"
print_a_name = proc { puts name }

name = "John"
run_proc(print_a_name)
````    

This produces

````ruby
    # => "John"
````

rather than "Fred".
