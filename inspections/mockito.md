# Mockito

## Licensing
Please let me know if using the same exact exception messages as the descriptions of the inspections is not permitted due to licensing issues.
I am happy to change them, it just made sense and I think it is clearer to use the same messages here too.

## Mockito matcher is not used with static import

This inspection would signal code snippets like the following, as incorrect:

```java
Matchers.any()
```

**Template:**

```xml
<searchConfiguration name="Mockito matcher is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains=""/>
    <constraint name="Instance" regexp="org.mockito.Matchers" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```

## Mockito cannot mock/spy final classes
It is mostly valid for versions before 2.1.0 but can be used from 2.1.0 upwards, if that feature is enabled. See related articles:
- https://github.com/mockito/mockito/wiki/What's-new-in-Mockito-2#mock-the-unmockable-opt-in-mocking-of-final-classesmethods
- https://www.baeldung.com/mockito-final-->

This inspection supports only the annotation based mocking and spying, not the mock and spy methods called from Mockito.class.

**Template:**

```xml
<searchConfiguration name="Mockito cannot mock/spy final classes." text="class $Class$ { &#10;    @$MockAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Field" script="&quot;Field.getType().resolve().hasModifierProperty(&quot;final&quot;)&quot;" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="MockAnnotation" regexp="org\.mockito\.(Mock|Spy)" within="" contains="" />
</searchConfiguration>
```

## Mockito.times/atLeast/atMost: Negative value is not allowed here.

This is based on the exception handling happening in:
- [`org.mockito.internal.verification.AtLeast`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/AtLeast.java)
- [`org.mockito.internal.verification.AtMost`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/AtMost.java)
- [`org.mockito.internal.verification.Times`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/Times.java)

This inspection would signal code snippets like the following, as incorrect:

```java
verify(mockObject, times(-1)).doSomething()
verify(mockObject, atLeast(-1)).doSomething()
verify(mockObject, atMost(-1)).doSomething()
```

It supports the `times`, `atLeast` and `atMost` verification modes.

**Template:**

```xml
<searchConfiguration name="Mockito.times/atLeast/atMost: Negative value is not allowed here." text="$Mockito$.verify($mockObject$, $Mockito$.$times$($value$)).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="times" regexp="times|atLeast|atMost" target="true" within="" contains="" />
    <constraint name="value" script="&quot;value.text.toInteger() &lt; 0&quot;" within="" contains="" />
</searchConfiguration>
```

## InOrder calls: Negative and zero values are not allowed here.

This is based on one of the exception handlings happening in [`org.mockito.internal.verification.Calls`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/Calls.java)

This inspection would signal a code snippet like the following, as incorrect:

```java
InOrder inOrder = Mockito.inOrder(mockObject);
inOrder.verify(mockObject, calls(-1)).doSomething()
```

This inspection is not prepared for signaling calling `Mockito.calls()` in `Mockito.verify()` because it is designed to work only with `InOrder.verify()`.
But there is a specific inspection for that. See [TODO]().

**Script filter ($value$)**

```groovy
try { 
    value.text.toInteger() < 0
//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)
} catch (java.lang.NumberFormatException nfe) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="InOrder calls: Negative and zero values are not allowed here." text="$InOrder$.verify($mockObject$, $Mockito$.$calls$($value$)).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="value" script="&quot;try { &#10;    value.text.toInteger() &lt; 0&#10;//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)&#10;} catch (java.lang.NumberFormatException nfe) {&#10;    false&#10;}&quot;" within="" contains="" />
    <constraint name="calls" regexp="calls" target="true" within="" contains="" />
    <constraint name="InOrder" nameOfExprType="org\.mockito\.InOrder" expressionTypes="org.mockito.InOrder" within="" contains="" />
</searchConfiguration>
```

## Mockito.calls() is only intended to work with InOrder

This is based on one of the exception handlings happening in [`org.mockito.internal.verification.Calls`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/Calls.java)

This inspection would signal a code snippet like the following, as incorrect:

```java
Mockito.verify(mockObject, calls(-1)).doSomething()
```

**Template:**

```xml
<searchConfiguration name="Mockito.calls is only intended to work with InOrder" text="$Mockito$.verify($mockObject$, $Mockito$.$calls$($value$)).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="calls" regexp="calls" target="true" within="" contains="" />
</searchConfiguration>
```

## @Captor field must be of the type ArgumentCaptor

This is a collection of Replace templates that are most useful when all of them used, but it is up to everyone which ones are actually used.

These are based on the exception handling happening in [`org.mockito.internal.configuration.CaptorAnnotationProcessor`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/configuration/CaptorAnnotationProcessor.java)

##### Matching types other than primitive types and ArgumentCaptor

This inspection would signal a code snippet like the following, as incorrect:

```java
@Captor
private String captor;
```

Since this is a replace template it provides a quick fix to replace the type with an argument captor.
Since the Search target of the template is the field type, the quick fix suggestion is visible only when the Alt+Enter menu is opened having
the cursor on the field type.

When the quick fix is applied the field would look like:

```java
@Captor
private ArgumentCaptor<String> captor;
```

**Template:**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;$FieldType$&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="(org\.mockito\.ArgumentCaptor|byte|short|int|long|float|double|boolean|char)" target="true" negateName="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

##### Individual templates for each primitive types

To be able to replace the primitive types with their non-primitive variants, each needs its own separate replace template to
be able to apply a proper quick fix.

Each matches a single primitive type for the field type, and nothing else.

NOTE: primitive type arrays are not handled by these inspections.

**byte**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [byte] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Byte&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="byte" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**short**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [short] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Short&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="short" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**int**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [int] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Integer&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="int" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**long**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [long] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Long&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="long" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**float**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [float] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Float&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="float" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**double**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [double] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Double&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="double" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**boolean**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [boolean] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Boolean&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="boolean" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

**char**

```xml
<replaceConfiguration name="@Captor field must be of the type ArgumentCaptor. Apply quick fix to wrap current type [char] in ArgumentCaptor." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" replacement="org.mockito.ArgumentCaptor&lt;java.lang.Character&gt;">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="char" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
</replaceConfiguration>
```

## MockitoAnnotations.initMocks() argument cannot be null

This is based on the exception handling happening in [`org.mockito.MockitoAnnotations`](https://github.com/mockito/mockito/blob/28012a49cb1b1aab1b957ac5e7507dd8b807a47e/src/main/java/org/mockito/MockitoAnnotations.java)

This inspection would signal code snippets like the following, as incorrect:

```java
MockitoAnnotations.initMocks(null);
```

This template can handle only the explicit `null` value since object null value can only be checked runtime.

**Template:**

```xml
<searchConfiguration name="MockitoAnnotations.initMocks() argument cannot be null" text="org.mockito.MockitoAnnotations.initMocks(null);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## '<method_name>' is a *void method* and it *cannot* be stubbed with a *return value*!

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#cannotStubVoidMethodWithAReturnValue(String)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
Mockito.doReturn(5).when(mockObject).voidMethod();
```

NOTE: This template doesn't signal problem in cases when there is at least one other stubbing method (`do...()`) called after `doReturn()`, e.g. `doReturn().doThrow()...`.

**Script filter ($voidMethod$):**

    voidMethod                  //this is a PsiMethodCallExpression 
        .resolveMethod()        //converting to PsiMethod/PsiMethodImpl
        .getReturnType()        //converting to PsiType
        .getPresentableText()   //converting to String
            == "void"

**Template:**

```xml
<searchConfiguration name="The method being stubbed is a *void method* and it *cannot* be stubbed with a *return value*." text="org.mockito.Mockito.doReturn($returnValue$).when($mockObject$).$voidMethod$();" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="returnValue" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="voidMethod" script="&quot;voidMethod.resolveMethod().getReturnType().getPresentableText() == &quot;void&quot;&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

## Incorrect use of API detected

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#incorrectUseOfApi()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
OngoingStubbing<String> ongoingStubbing = when(mockObject.someMethod());
ongoingStubbing.thenReturn("some text");
ongoingStubbing.thenThrow(new RuntimeException());
```

But based on Mockito's behaviour the following construct is correct, so it is not included in the template:

```java
OngoingStubbing<String> ongoingStubbing = when(mockObject.someMethod());
ongoingStubbing.thenReturn("some text").thenThrow(new RuntimeException());
```

The template is prepared to handle additional expressions between the two `then...` calls on the OngoingStubbing object, so they won't interfere with inspecting the code,
and (hopefully) won't result in a false-negative.

The generic type `OngoingStubbing` is not restricted so it is flexible.

The template always highlights the second `then...` method call, which in the case of the example above is the `thenThrow` method name.

**Template:**

```xml
<searchConfiguration name="Incorrect API usage detected: You probably stored a reference to OngoingStubbing returned by when() and called stubbing methods like thenReturn() on this reference more than once. " text="org.mockito.stubbing.OngoingStubbing $ongoingStubbing$ = when($mockObjectCall$);&#10;$intermediateExpression$;&#10;$ongoingStubbing$.$thenMethod$($parameter$);&#10;$otherIntermediateExpression$;&#10;$ongoingStubbing$.$otherThenMethod$($otherParameter$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="ongoingStubbing" within="" contains="" />
    <constraint name="mockObjectCall" within="" contains="" />
    <constraint name="thenMethod" regexp="then.*" within="" contains="" />
    <constraint name="otherThenMethod" regexp="then.*" target="true" within="" contains="" />
    <constraint name="parameter" within="" contains="" />
    <constraint name="otherParameter" within="" contains="" />
    <constraint name="intermediateExpression" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="otherIntermediateExpression" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## Missing method call for verify(mock)

These are based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#unfinishedVerificationException(Location)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

The first inspection would signal code snippets like the following, as incorrect, when there is a missing method call for verify(mock):

```java
verify(mockObject);
```

```xml
<searchConfiguration name="Missing method call for verify(mock)." text="org.mockito.Mockito.verify($mockObject$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
</searchConfiguration>
```

The other would signal code snippets that call `equals()` or `hashCode()` from verify(mock):

```java
verify(mockObject).equals(otherObject);
verify(mockObject).hashCode();
```

```xml
<searchConfiguration name="equals() and hashCode() method cannot be verified by Mockito" text="org.mockito.Mockito.verify($mockObject$).$verifiedMethod$($otherObject$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="verifiedMethod" regexp="equals|hashCode" within="" contains="" />
    <constraint name="otherObject" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## verifyNoMoreInteractions() and verifyZeroInteractions() requires at least one argument

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#mocksHaveToBePassedToVerifyNoMoreInteractions()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
verifyZeroInteractions();
verifyNoMoreInteractions();
```

The template doesn't signal the case when the passed argument(s) is/are not a mock, it only checks if the number of arguments is zero or not.

**Template:**

```xml
<searchConfiguration name="verifyNoMoreInteractions() and verifyZeroInteractions() requires at least one argument" text="org.mockito.Mockito.$verifyMethod$($arguments$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="verifyMethod" regexp="verifyZeroInteractions|verifyNoMoreInteractions" target="true" within="" contains="" />
    <constraint name="arguments" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## Mockito.inOrder() requires at least one argument

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#mocksHaveToBePassedWhenCreatingInOrder()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
InOrder inOrder = Mockito.inOrder();
```

The template doesn't signal the case when the passed argument(s) is/are not a mock, it only checks if the number of arguments is zero or not.

**Template:**

```xml
<searchConfiguration name="Mockito.inOrder() requires at least one argument" text="org.mockito.InOrder $inOrder$ = org.mockito.Mockito.$inOrderMethod$($arguments$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="arguments" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="inOrder" within="" contains="" />
    <constraint name="inOrderMethod" regexp="inOrder" target="true" within="" contains="" />
</searchConfiguration>
```

## Some arguments don't use argument matchers. Either none or all of them should use matchers.

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#invalidUseOfMatchers(int, List)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
when(mockObject.mockMethod(ArgumentMatchers.any(SomeClass.class), someExpression)).thenReturn(aReturnValue);
```

For the template it doesn't matter where in the argument list the inconsistent arguments are (be it matcher or non-matcher), it will signal it as a problem.

**Script filter (Complete match):**

```groovy
boolean hasOnlyMatchers = true
boolean hasMatcher = false
arguments.each { it ->
    //If the argument is a method call
    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {
        //A method call from ArgumentMatchers
        if (it.resolveMethod().getContainingClass().getQualifiedName() == "org.mockito.ArgumentMatchers") {
            hasMatcher = true
        //A method call from a non-ArgumentMatchers class
        } else {
            hasOnlyMatchers = false
        }
    //Not a method call, but an other type of expression
    } else {
        hasOnlyMatchers = false
    }
}

//If there is not only matchers, or not only non-matchers, then it will signal a problem.
!((hasMatcher && hasOnlyMatchers) || (!hasMatcher && !hasOnlyMatchers))
```

**Template:**

```xml
<searchConfiguration name="Some arguments don't use argument matchers. Either none or all of them should use matchers." text="org.mockito.Mockito.when($mockObject$.$mockMethod$($arguments$))" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" script="&quot;boolean hasOnlyMatchers = true&#10;boolean hasMatcher = false&#10;arguments.each { it -&gt;&#10;&#9;//If the argument is a method call&#10;    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {&#10;    &#9;//A method call from ArgumentMatchers&#10;        if (it.resolveMethod().getContainingClass().getQualifiedName() == &quot;org.mockito.ArgumentMatchers&quot;) {&#10;            hasMatcher = true&#10;        //A method call from a non-ArgumentMatchers class&#10;        } else {&#10;            hasOnlyMatchers = false&#10;&#9;        }&#10;    //Not a method call, but an other type of expression&#10;    } else {&#10;    &#9;hasOnlyMatchers = false&#10;    }&#10;}&#10;&#10;//If there is not only matchers, or not only non-matchers, then it will signal a problem.&#10;!((hasMatcher &amp;&amp; hasOnlyMatchers) || (!hasMatcher &amp;&amp; !hasOnlyMatchers))&quot;" within="" contains="" />
    <constraint name="arguments" minCount="2" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="mockMethod" within="" contains="" />
</searchConfiguration>
```

## Some AdditionalMatchers arguments don't use argument matchers. Either none or all of them should use a matcher.

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#incorrectUseOfAdditionalMatchers(String, int, Collection)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
when(mockObject.mockMethod(AdditionalMatchers.and(someExpression, any(SomeClass.class)))).thenReturn(aReturnValue);
```

For the template it doesn't matter where in the AdditionalMatchers' argument list the inconsistent arguments are (be it matcher or non-matcher), it will signal it as a problem.

**Script filter (Complete match):**

```groovy
boolean hasOnlyMatchers = true
boolean hasMatcher = false
arguments.each { it ->
    //If the argument is a method call
    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {
        //A method call from ArgumentMatchers
        if (it.resolveMethod().getContainingClass().getQualifiedName() == "org.mockito.ArgumentMatchers") {
            hasMatcher = true
        //A method call from a non-ArgumentMatchers class
        } else {
            hasOnlyMatchers = false
        }
    //Not a method call, but an other type of expression
    } else {
        hasOnlyMatchers = false
    }
}

//If there is not only matchers, or not only non-matchers, then it will signal a problem.
!((hasMatcher && hasOnlyMatchers) || (!hasMatcher && !hasOnlyMatchers))
```    

**Template:**

```xml
<searchConfiguration name="Some AdditionalMatchers arguments don't use argument matchers. Either none or all of them should use a matcher." text="org.mockito.AdditionalMatchers.$additionalMatcher$($arguments$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" script="&quot;boolean hasOnlyMatchers = true&#10;boolean hasMatcher = false&#10;arguments.each { it -&gt;&#10;    //If the argument is a method call&#10;    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {&#10;        //A method call from ArgumentMatchers&#10;        if (it.resolveMethod().getContainingClass().getQualifiedName() == &quot;org.mockito.ArgumentMatchers&quot;) {&#10;            hasMatcher = true&#10;        //A method call from a non-ArgumentMatchers class&#10;        } else {&#10;            hasOnlyMatchers = false&#10;        }&#10;    //Not a method call, but an other type of expression&#10;    } else {&#10;        hasOnlyMatchers = false&#10;    }&#10;}&#10;&#10;//If there is not only matchers, or not only non-matchers, then it will signal a problem.&#10;!((hasMatcher &amp;&amp; hasOnlyMatchers) || (!hasMatcher &amp;&amp; !hasOnlyMatchers))&quot;" within="" contains="" />
    <constraint name="additionalMatcher" within="" contains="" />
    <constraint name="arguments" minCount="2" maxCount="2147483647" target="true" within="" contains="" />
</seairchConfiguration>
```

## Only void methods can doNothing().

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#onlyVoidMethodsCanBeSetToDoNothing()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
doNothing().when(mock).someNotVoidMethod();
```

NOTE: This template doesn't signal problem in cases when there is at least one other stubbing method `do...()`) called after `doNothing()`, e.g. `doNothing().doThrow()...`.

**Script filter ($mockNotVoidMethod$):**

```groovy
mockNotVoidMethod.resolveMethod().getReturnType().getPresentableText() != "void"
```

**Template:**

```xml
<searchConfiguration name="Only void methods can doNothing()." text="org.mockito.Mockito.doNothing().when($mockObject$).$mockNotVoidMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="mockNotVoidMethod" script="&quot;mockNotVoidMethod.resolveMethod().getReturnType().getPresentableText() != &quot;void&quot;&quot;" within="" contains="" />
</searchConfiguration>
```
    
    
## Mockito.mock() extraInterfaces() requires at least one interface

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#extraInterfacesRequiresAtLeastOneInterface()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
withSettings().defaultAnswer(null).extraInterfaces().name("").lenient()
```

Regardless of the position of the problematic `extraInterfaces()` call the template will signal it.

**Template:**

```xml
<searchConfiguration name="Mockito.mock() extraInterfaces() requires at least one interface" text="$mockSettingsMethod$.$extraInterfaces$($interfaces$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="interfaces" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="mockSettingsMethod" nameOfExprType="org\.mockito\.MockSettings" expressionTypes="org.mockito.MockSettings" within="" contains="" />
    <constraint name="extraInterfaces" regexp="extraInterfaces" target="true" within="" contains="" />
</searchConfiguration>
```

## Cannot call abstract real method on java object! Calling real methods is only possible when mocking non-abstract method.

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#cannotCallAbstractRealMethod()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
when(mockObject.abstractMethod()).thenCallRealMethod();
```

NOTE: This template doesn't signal problem in cases when there is at least one other stubbing method called before `thenCallRealMethod()`
but doesn't have a problem if there are method calls after that.

**Script filter ($abstractMethod$):**

```groovy
abstractMethod.resolveMethod().getModifierList().hasExplicitModifier("abstract")
```

**Template:**

```xml
<searchConfiguration name="Cannot call abstract real method on java object! Calling real methods is only possible when mocking non-abstract method." text="org.mockito.Mockito.when($mockObject$.$abstractMethod$()).thenCallRealMethod()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="abstractMethod" script="&quot;abstractMethod.resolveMethod().getModifierList().hasExplicitModifier(&quot;abstract&quot;)&quot;" within="" contains="" />
</searchConfiguration>
```

## You cannot have more than one Mockito annotation on a field!

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#moreThanOneAnnotationNotAllowed(String)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection seems to only the following signal code snippets, as incorrect, combining the `@Mock` and `@Captor` annotations:

```java
@Mock
@Captor
ArgumentCaptor<MockObject> mockObjectCaptor;
```

Regardless of the order in which the two annotations are present, they are both highlighted as incorrect.

**Template:**

```xml
<searchConfiguration name="You cannot have more than one Mockito annotation on a field: @Mock + @Captor" text="@$MockAnnotation$&#10;@org.mockito.Captor&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $field$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="field" within="" contains="" />
    <constraint name="MockAnnotation" regexp="org\.mockito\.Mock" target="true" within="" contains="" />
</searchConfiguration>
```

## This combination of annotations is not permitted on a single field

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#unsupportedCombinationOfAnnotations(String, String)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
@Mock
@InjectMocks
ArgumentCaptor<MockObject> mockObjectCaptor;
``` 

which seem to apply to the following combinations of Mockito annotations:
- @Mock + @InjectMocks
- @Spy + @Mock
- @Spy + @Captor
- @Captor + @InjectMocks

The order in which the annotations are present is almost irrelevant for the template. In (edge?) cases when there are more than 2 annotations are applied on the field,
their order can interfere with whether the inspection founds a match or not.

**Script filter (Complete Match):**

```groovy
annotationMapping = ['org.mockito.Mock':['org.mockito.InjectMocks'], 'org.mockito.Spy':['org.mockito.Mock', 'org.mockito.Captor'], 'org.mockito.Captor':['org.mockito.InjectMocks']]

boolean matches = false

annotationMapping.keySet().each { key -> 
    annotationMapping.get(key).each { annotation -> 
        matches = matches || (
            (MockitoAnnotation.getQualifiedName() == key && OtherMockitoAnnotation.getQualifiedName() == annotation)
                || (OtherMockitoAnnotation.getQualifiedName() == key && MockitoAnnotation.getQualifiedName() == annotation)
        )
    }
}

matches
```

**Template:**

```xml
<searchConfiguration name="This combination of annotations is not permitted on a single field" text="@$MockitoAnnotation$&#10;@$OtherMockitoAnnotation$&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $field$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" script="&quot;annotationMapping = ['org.mockito.Mock':['org.mockito.InjectMocks'], 'org.mockito.Spy':['org.mockito.Mock', 'org.mockito.Captor'], 'org.mockito.Captor':['org.mockito.InjectMocks']]&#10;&#10;boolean matches = false&#10;&#10;annotationMapping.keySet().each { key -&gt; &#10;    annotationMapping.get(key).each { annotation -&gt; &#10;        matches = matches || (&#10;            (MockitoAnnotation.getQualifiedName() == key &amp;&amp; OtherMockitoAnnotation.getQualifiedName() == annotation)&#10;                || (OtherMockitoAnnotation.getQualifiedName() == key &amp;&amp; MockitoAnnotation.getQualifiedName() == annotation)&#10;        )&#10;    }&#10;}&#10;&#10;matches&quot;" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="field" within="" contains="" />
    <constraint name="MockitoAnnotation" target="true" within="" contains="" />
    <constraint name="OtherMockitoAnnotation" within="" contains="" />
</searchConfiguration>
```

## Negative value of duration is not allowed as argument of timer methods (after(), timeout())

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#cannotCreateTimerWithNegativeDurationTime(long)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

This inspection would signal code snippets like the following, as incorrect:

```java
Runnable runnable = Mockito.mock(Runnable.class);
Mockito.verify(runnable, Mockito.after(-1000).never()).run();
```

The example is from the [originally reported Mockito issue](https://github.com/mockito/mockito/issues/197).

**Script filter ($duration$):**

```groovy
try { 
    duration.text.toInteger() < 0
//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)
} catch (java.lang.NumberFormatException nfe) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="Negative value of duration is not allowed as argument of timer methods (after(), timeout())" text="org.mockito.Mockito.$afterOrTimeout$($duration$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="afterOrTimeout" regexp="after|timeout" within="" contains="" />
    <constraint name="duration" script="&quot;try { &#10;    duration.text.toInteger() &lt; 0&#10;//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)&#10;} catch (java.lang.NumberFormatException nfe) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
</searchConfiguration>
```
