#Blocks, Procs, and Lambdas

##Blocks

##Procs

##Lambdas

##Procs vs. Lambdas
###Arity
Lambdas care about how many arguments are given. Procs don't.
###Return syntax


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
