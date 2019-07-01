# Using imports in script filters

As you may have already known Script filters are basically Groovy scripts, that must return a boolean value to determine whether the filter
passes or fails to match with a certain part of the code.

For that purpose various techniques, classes, methods can be used but some require importing in order to work.

## Native Java and Groovy classes

Since you are writing a Groovy script in this filter, all the options of importing classes and properties, that are defined in the [imports section of the official Groovy documentation](http://groovy-lang.org/structure.html#_imports)
work properly.

I encourage you to go through that list of options if you are interested in or about to work with Script filters requiring additional classes or methods.

## IntelliJ package imports

One of the strengths of Script filters come from the fact that you can import classes from internal IntelliJ packages, and have useful classes
and utility methods at your disposal.

Let's take the Script filter of one of the Mockito inspections called [Some arguments don't use argument matchers](https://github.com/picimako/intellij-inspections/blob/master/inspections/mockito.md#some-arguments-dont-use-argument-matchers-either-none-or-all-of-them-should-use-matchers).
I'll include just the beginning of it:

```groovy
//variable declarations
arguments.each { it ->
    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {
    	...
```

So in this case, just like in any Groovy or Java program, instead of referencing `PsiMethodCallExpression` by its fully qualified name,
I could have imported the class and written the script like this:

```groovy
import com.intellij.psi.*

//variable declarations
arguments.each { it ->
    if (it instanceof PsiMethodCallExpression) {
    	...
```

I think there is not much magic here, but it's good to know that one can leverage the power of IntelliJ's internal classes.

## Importing custom and 3rd party projects

Though so far it may have been straightforward that such language elements may be used in the Script filter,
importing classes from our own projects (that you work on locally), or from 3rd party projects that you just use in your project
(like Mockito or JUnit) cannot be achieved unfortunately, at least based on my investigations until this point.

I tried different ways, even putting classes that I wanted to import into packages named the same as some IntelliJ internal packages,
but that didn't change anything. :)

I have not found any information about this yet, I can only imagine that a feature like this (if it exists now, or will exist in the future)
might need some additional configuration to be able to find our project classpath, Maven local repository or whatever to be able to lookup the classes
we want to import.

I can also imagine that it might have differences between IDEA project specific template configurations and global IDEA configurations.  


Of course I will update this article when something new appears on the horizon.
