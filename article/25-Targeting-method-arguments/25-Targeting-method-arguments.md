## Targeting method parameters in method signatures

There are a couple of ways one can target method parameters in inspections. Which one you use may depend on the actual use case for which you are creating the inspection, personal preference, or maybe other things.

In this article I'm going to explain them and provide example templates for each of them. 

## Type and name
In this case the parameter type and parameter name have separate template variables defined. An example of this:

```java
$MethodType$ $Method$($ParameterType$ $Parameter$);
```

Using this construct you can target any number (0 to unlimited) of parameters of a method signature but parameters cannot be handled separately. For instance, to apply an inspection only for a specific signature of
a method that is overloaded with different signatures, you may need to define the parameters more specifically.

## Parameter(s) without separate type and name
This construct is very similar to the previous one but it is slightly more generic. In this case the type and name have no separate template variable but one for targeting both at the same time.
This can be useful in cases where you don't want to separately target the parameter or the name of the parameters, rather e.g. their count, but it still might be achievable by evaluating its text using either
a Text filter or a Script filter for more advanced scenarios. 

```java
$MethodType$ $Method$($Parameters$);
```

## Parameter list
Though targeting multiple parameters in a generic way is possible with the templates in the two sections above, when you want to look for more specific method signatures, you need to define multiple template
variables for each parameter.

A possible scenario for this could be:
```java
$MethodType$ $Method$($ParameterType1$ $Parameter1$, $ParameterType2$ $Parameter2$, $ParameterType3$ $Parameter3$);
$MethodType$ $Method$($Parameter1$, $Parameter2$, $Parameter3$);
```  

Be aware that using the same template variable for multiple parameter names may lead to incorrect inspection behaviour because it will search for multiple parameter names having the same value. 

If more than one of the method parameters has the same type, you don't need to define separate template variables for targeting that, but you can use the same one:
```java
$MethodType$ $Method$($String$ $Parameter1$, $Integer$ $Parameter2$, $String$ $Parameter3$);
```  

## Variable without method declaration 
In this case you can/will be much more generic with the template because with such a template you will target not only method parameters but field and variable declarations and definitions as well.

```java
$ParameterType$ $Parameter$
```

## Applying annotations
Annotations on method parameters can only be used one way, when both the parameter type and name are defined as separate template variables.

So while this one is valid:
```java
$ReturnType$ $Method$(@$Annotation$ () $ParameterType$ $Parameter$);
```

this one will give error and IntelliJ won't allow saving the template:
```java
$ReturnType$ $Method$(@$Annotation$ () $Parameter$);
```

When an annotation is defined on single method parameter template variable like in the example just above, it will mark templates only when all the parameters have the annotation applied.

So this one is marked by the template:
```java
public void somemethod(@SomeAnnotation String first, @SomeAnnotation Integer second, @SomeAnnotation Double third) {
}
```

but this one isn't:
```java
public void somemethod(@SomeAnnotation String first, Integer second, @SomeAnnotation Double third) {
}
```

#### Annotations with attributes
The same rules apply to templates in which attributes are also defined for the annotation. The only difference is the attribute value which you can define in two different ways.

One is to specify the literal value of the attribute like

```java
$ReturnType$ $Method$(@$Label$ ("literal label") $ParameterType1$ $Parameter1$);
```

or define a template variable in which you can assemble as complex filtering as you want: 

```java
$ReturnType$ $Method$(@$Label$ ($LabelValue$) $ParameterType1$ $Parameter1$);
```

### Annotations specific to method parameters
In case you have an annotation that you don't want to use on method parameters but may happen that it is applied on not all parameters of a method, like in the last example, you need to do the following:

1. Create a template defining different template variables for the parameters and applying the annotation on all of them:
```java
$ReturnType$ $Method$(@$Annotation$ () $ParameterType1$ $Parameter1$, @$Annotation$ () $ParameterType2$ $Parameter2$, @$Annotation$ () $ParameterType3$ $Parameter3$);
```

2. Add a Count filter to the annotation to *0-infinite*, marking that there may either be any number of parameters having that annotation applied.

### Multiple annotations
There is no special magic needed to handle multiple annotations on a parameter. You can simply define multiple template variables for those annotations, like:

```java
$ReturnType$ $Method$(@$Retry$ @$Label$ ($LabelValue$) $ParameterType1$ $Parameter1$);
```

Other than that, the same rules apply to such templates as the ones I explained in this article.
