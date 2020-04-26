# Handling method calls and call chains in Script filters

The reason behind this article is that handling method call chains in a generic way, is not necessarily straightforward
or easy, using structural search and replace templates.

In this article I would like to give you details about how this can be achieved from different perspectives, how different
cases of using method calls and chains may be handled including e.g. different parameter types mainly via Script filters.

For targeting method parameters in the template text in method signatures
you can check out the article called [Targeting method parameters in method signatures](https://ijnspector.wordpress.com/2019/03/22/targeting-method-parameters-in-method-signatures/).

Please note that I haven't yet looked into any IntelliJ plugins that implement inspections, so while these solutions
are valid, there might be more optimal ways of solving them. If you happen to know any better solution or improvement, please point me to that.

## Iterate through in reverse order

The logic how to iterate through a method call chain in reverse order is already detailed in great length in the tutorial
called [Validating ImageComparison setter method default values](https://ijnspector.wordpress.com/2020/04/09/validating-imagecomparison-setter-method-default-values/),
so here I'm only going to detail the essential parts.

A method call chain is made up of `PsiMethodCallExpression` and `PsiReferenceExpression` type objects nested into each other.

So the idea is to iterate through each `PsiMethodCallExpression` in the PSI tree, inspecting each method call. Advancing in the iteration can happen
by calling `methodCall.firstChild?.firstChild` and setting that value for the next cycle of iteration, where `methodCall` is the object representing the currently
inspected call chain.

In this case `methodCall.firstChild` returns a `PsiReferenceExpression`, while `methodCall.firstChild.firstChild` returns a `PsiMethodCallExpression`.

Given the following template text:

```java
$caller$.$methodChain$($params$)
```

this Script filter is a feasible solution:

```groovy
import com.intellij.psi.*;

//Store the currently inspected part of the method chain.
//PsiExpression is the closest common superclass of PsiMethodCallExpression and PsiReferenceExpression
PsiExpression methodCall = methodChain

while (methodCall instanceof PsiMethodCallExpression) {
    //does some magic that you want it to do
    //...

    methodCall = methodCall.firstChild?.firstChild //Move on to the next method in the chain
}
```

## Iterate through in original order

The script for iterating through the method call in their order of call is fairly similar to the one in the previous section. The only thing that needs to
be added is collecting the method calls in their order of call before doing any actual inspection on them.

If you happen to come across any utility class and method that return a collection of the methods in a call chain, please let me know.

```groovy
import com.intellij.psi.*;

def methodCalls = new ArrayList<PsiMethodCallExpression>()
PsiExpression methodCall = methodChain

//Collect method call expressions in the same order they are called
while (methodCall instanceof PsiMethodCallExpression) {
    methodCalls.add(0, methodCall);
    methodCall = methodCall.firstChild?.firstChild //Move on to the next method in the chain
}

for (call in methodCalls) {
    //does some magic that you want it to do
    //...
}
```

If you'd like to validate whether a certain method call is preceded by another one in a chain, you don't necessarily need to iterate through them in their original order,
instead you can use the solution from the previous section. You just need to add additional checks for the method names.

## Retrieve parameters

A method call's parameters are stored in a `PsiExpressionList` as the last child of a `PsiMethodCallExpression`.

They can be accessed by querying its (array type) `expressions` property.

```groovy
PsiExpression[] paramsOf(PsiMethodCallExpression expr) {
    expr.lastChild.expressions //lastChild: PsiExpressionList
}
```

If a method has only one parameter, you can go right with:

```groovy
PsiExpression paramOf(PsiMethodCallExpression expr) {
    expr.lastChild.expressions[0]
}
```

If a particular parameter value happens to be a literal value, you can access it querying the particular `PsiExpression`'s `value` property.

```groovy
Object methodParamValueOf(PsiMethodCallExpression expr) {
    expr
        .lastChild //PsiExpressionList
        .expressions[0] //PsiLiteralExpression
        .value
}
```

## Retrieve method names

A called method's name can be retrieved from the `referenceName` property of a `PsiReferenceExpression` within a `PsiMethodCallExpression`.
It returns method name without any parameters, types, etc.

The following groovy script is a feasible solution for getting that value:

```groovy
String methodNameOf(PsiMethodCallExpression expr) {
    expr.firstChild.referenceName //firstChild: PsiReferenceExpression
}
```

## Generic type parameters

To keep this article as short as possible, there is a separate article that goes into the details of how to retrieve generic type parameters:
[Get generic type parameters from method calls](../41-Get-generic-type-parameter-from-method-calls/41-get-generic-type-parameters-from-method-calls.md).

## Search target

Unfortunately the only option is using the Search target dropdown but there are a few caveats that come with selecting certain template variables.

It is generally true, that for template variables that may represent multiple values (e.g. parameters, type parameters, method calls)
there is no option right now to mark a particular value as incorrect, instead all values (i.e all parameters, all method calls in a call chain) are highlighted.

## IntelliJ utilities

For method calls there are a couple of utility classes that you can use, each covering a different aspect of methods and method calls:
- `com.intellij.psi.util.PsiMethodUtil`
- `com.siyeh.ig.psiutils.MethodUtils`
- `com.siyeh.ig.psiutils.MethodCallUtils`

## Related articles

IJnspector tutorial: [Validating ImageComparison setter method default values](https://ijnspector.wordpress.com/2020/04/09/validating-imagecomparison-setter-method-default-values/)

JetBrains tickets:
- [IDEA-197092 - Allow optional method call in structural search](https://youtrack.jetbrains.com/issue/IDEA-197092)
- [IDEA-194766 - Structural: Support variables within identifiers](https://youtrack.jetbrains.com/issue/IDEA-194766)
- [IDEA-178110 - IntelliJ Structural Search and Replace doesn't work right on "fluent" methods that return](https://youtrack.jetbrains.com/issue/IDEA-178110)
- [IDEA-173253 - Structural search and replace not properly capturing part of method chain](https://youtrack.jetbrains.com/issue/IDEA-173253)
