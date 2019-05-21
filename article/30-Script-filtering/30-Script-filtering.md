# Script filtering

I've already jumped into some magic about Script filters in the [Mockito cannot Mock/Spy final classes](https://ijnspector.wordpress.com/2019/03/25/mockito-cannot-mock-spy-final-classes/) article.
This time I will give you some general details about how Script filters work and what may be achieved with them (hint: basically anything).

Before you proceed with this article I highly recommend reading [this JetBrains support ticket](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360000045970-Search-and-Replace-Structurally-Script-Constraints-Documentation-)  about Script filters.
It is a well-put-together explanation about that filter and how template variables are handled.

## Basics
The Script filter uses the Groovy language for scripting, so basically any language construct that Groovy supports may probably be used here as well.

## Defining a predicate
As the content of the filter one needs to define a predicate which will be evaluated to true or false. The `return` keyword may be used but not required.
So considering a Script filter as:

```java
AnnotationValue > 5
```

the following is also valid:
```java
return AnnotationValue > 5
```

Variables can also be created in this filter. So considering the template and filter I created in [Annotation attribute value is greater than the max allowed](https://ijnspector.wordpress.com/2019/01/30/annotation-attribute-value-is-greater-than-the-max-allowed/):

```java
Template:
    @picimako.tutorial.annotation.Retry(times = $RetryCount$)
    
Filter:
    RetryCount.text.toInteger() > 5
```

the filter may also be written as:
```java
String retryCount = RetryCount.text;
retryCount.toInteger() > 5
```

## import statements

I will write a separate, more detailed article about import statements later, which I will also link here,
but for now it is enough to know that for scripts that require some specific classes to reference, they may be imported but
so far I've been able to import only IntelliJ specific packages.

There are a few Existing templates that also contain import statements, like the one *called fields/variables read*:

```java
Template:
    $Symbol$
    
Filter:
    import com.intellij.psi.*
    import static com.intellij.psi.util.PsiUtil.*
    Symbol instanceof PsiReferenceExpression && isAccessedForReading(Symbol)
```

Right now it is not important what is happening, it is enough to see that an IntelliJ specific class (`PsiReferenceExpression`) is imported with simple wildcard import,
and a utility method (`isAccessedForReading()`) is imported with static import.

## Referencing template variables

As you may already know, template variables can be defined by specifying a variable name within enclosing `$` characters, e.g. the one in the previous section: `$Symbol$`.

However in Script filters they can be referenced without the `$` characters like:

```java
Symbol instanceof PsiReferenceExpression
```

Also, since these variables are handled as PSI nodes, certain values may be retrieved from them (the quotes are from the aforementioned support ticket):

- `Symbol.text`: returns the text/value of the whole template variable/node
    > Say you have a SSR variable that matches a method, for example a toString() method. Then the variable is in fact a PsiMethod node. ... So naturally variable.text will give you the entire text of the method.
    - For a PsiMethod, it returns the whole text of the method, and for an annotation attribute value, it returns that value as a String.
    - Since the text is returned as a String, type conversion might be necessary for further validation as e.g. an Integer.

- `Symbol.name`: returns the name of the node, if it has one.
    - It is not applicable (for all node types, at least) for nodes whose text is a literal value:
        > ... If we just need the name of the method, variable.name will suffice. In another case the SSR variable may match some expression, for example a reference to a variable, a PsiReferenceExpression. An expression has no name of course, but retrieving the entire text of the expression, will give us the name of the variable it is referring to.

- `Symbol.parent`: returns the parent Psi... node of `Symbol`, which in case of a PsiMethod type node, is a PsiClass node:
    > Say you have a SSR variable that matches a method, for example a toString() method. Then the variable is in fact a PsiMethod node. Retrieving variable.parent will produce a PsiClass node, and so forth.

You can read more about PSI elements in the official [IntelliJ SDK DevGuide](https://www.jetbrains.org/intellij/sdk/docs/basics/architectural_overview/psi_elements.html).

## \_context_ variable
> The **\_context_** variable is an artificial variable. All variables used in a pattern can be accessed from the Script Constraints. **\_context_** corresponds to the Complete Match variable.

There are a few Existing templates that provide examples for using this variable, though I haven't been able to completely wrap my head around them:
- *classes*
    ```java
    Template:
        class $Class$ {}

    $Class$ Script filter:
        !__context__.interface && !__context__.enum
    ```

- *Static fields that are not final*
    ```java
    Template:
        class $Class$ {
            static $Type$ $Variable$ = $Init$;
        }

    $Variable$ Script filter:
        !__context__.hasModifierProperty("final")
    ```

## Complete Match filters
Filters may be written for Complete Match as well, when no template variable is selected in the editor, though "only" Script filter is allowed,
with which basically anything can be achieved.

However I haven't seen a real life practical example of using this.

I will experiment with this and see what happens. I will also update this post with my findings.

## Final words

This article is a live document, and is updated when something changes, or a new feature/capability is introduced, or I just find out that I missed something that this tool is already capable of.

And last but not least, a huge Thank you! goes to **Bas Leijdekkers** who provided that great answer on the [JetBrains support ticket](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360000045970-Search-and-Replace-Structurally-Script-Constraints-Documentation-).
That was the best explanation about script filters that I have so far stumbled upon. 