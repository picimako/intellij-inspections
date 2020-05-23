# Mockito

- Mockito website: https://site.mockito.org
- Mockito on GitHub: https://github.com/mockito/mockito

## Table of contents

#### Mocking / Spying
- [Mockito cannot mock/spy final classes](#mockito-cannot-mockspy-final-classes)
- [MockitoAnnotations.initMocks() argument cannot be null](#mockitoannotationsinitmocks-argument-cannot-be-null)
- ['<method_name>' is a *void method* and it *cannot* be stubbed with a *return value*!](#method_name-is-a-void-method-and-it-cannot-be-stubbed-with-a-return-value)
- [@Mock extraInterfaces attribute is provided with at least one non-interface type](#mock-extrainterfaces-attribute-is-provided-with-at-least-one-non-interface-type)
- [Mockito.mock() extraInterfaces() requires at least one interface](#mockitomock-extrainterfaces-requires-at-least-one-interface)
- [Only void methods can doNothing()](#only-void-methods-can-donothing)
- [Cannot call abstract real method on java object! Calling real methods is only possible when mocking non-abstract method.](#cannot-call-abstract-real-method-on-java-object-calling-real-methods-is-only-possible-when-mocking-non-abstract-method)
- [Incorrect use of API detected](#incorrect-use-of-api-detected)

#### Verification
- [Mockito.times/atLeast/atMost: Negative value is not allowed here.](#mockitotimesatleastatmost-negative-value-is-not-allowed-here)
- [InOrder calls: Negative and zero values are not allowed here.](#inorder-calls-negative-and-zero-values-are-not-allowed-here)
- [Mockito.calls() is only intended to work with InOrder](#mockitocalls-is-only-intended-to-work-with-inorder)
- [Missing method call for verify(mock)](#missing-method-call-for-verifymock)
- [verifyNoMoreInteractions() and verifyZeroInteractions() requires at least one argument](#verifynomoreinteractions-and-verifyzerointeractions-requires-at-least-one-argument)
- [Mockito.inOrder() requires at least one argument](#mockitoinorder-requires-at-least-one-argument)
- [Negative value of duration is not allowed as argument of timer methods (after(), timeout())](#negative-value-of-duration-is-not-allowed-as-argument-of-timer-methods-after-timeout)
- [X is not implemented to work with InOrder wrapped inside an Y](#x-is-not-implemented-to-work-with-inorder-wrapped-inside-an-y)
- [This verification method is not implemented to work with InOrder](#this-verification-method-is-not-implemented-to-work-with-inorder)
- [times(1) call may be removed. Mockito.verify(mockObject) behaves the same.](#times1-call-may-be-removed-mockitoverifymockobject-behaves-the-same)

#### ArgumentCaptor
- [@Captor field must be of the type ArgumentCaptor](#captor-field-must-be-of-the-type-argumentcaptor)
- [ArgumentCaptor field should define a generic type](#argumentcaptor-field-should-define-a-generic-type)

#### Mockito annotations
- [You cannot have more than one Mockito annotation on a field!](#you-cannot-have-more-than-one-mockito-annotation-on-a-field)
- [This combination of annotations is not permitted on a single field](#this-combination-of-annotations-is-not-permitted-on-a-single-field)

#### Mockito matchers
- [Mockito matcher is not used with static import](#mockito-matcher-is-not-used-with-static-import)
- [Some (AdditionalMatchers) arguments don't use argument matchers. Either none or all of them must use matchers.](#some-additionalmatchers-arguments-dont-use-argument-matchers-either-none-or-all-of-them-must-use-matchers)

## Licensing
Please let me know if using the same exact exception messages as the descriptions of the inspections is not permitted due to licensing issues.
I am happy to change them, it just made sense and I think it is clearer to use the same messages here too.

## Mockito matcher is not used with static import

In many cases static importing a static method makes the code less cluttered and more readable which some people might
find true in case of JUnit assertions as well.

| Compliant code | Non-compliant code |
|---|---|
| <pre>any()</pre> | <pre>Matchers.any()</pre> |

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

It is mostly valid for versions before 2.1.0 but can be used from 2.1.0 upwards, if inline mocking is disabled. See related articles:
- https://github.com/mockito/mockito/wiki/What's-new-in-Mockito-2#mock-the-unmockable-opt-in-mocking-of-final-classesmethods
- https://www.baeldung.com/mockito-final-->

This inspection supports only the annotation based mocking and spying, not the mock and spy methods called from `org.mockito.Mockito`.

**Script filter ($Field$)**

```groovy
Field.getType().resolve().hasModifierProperty("final")
```

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

Since methods on any object cannot be called negative times, it is not valid to check them whether they are called negative times.

| Compliant code | Non-compliant code |
|---|---|
| <pre>verify(mockObject, times(3)).doSomething()</pre> | <pre>verify(mockObject, times(-1)).doSomething()</pre> |
| <pre>verify(mockObject, atLeast(1)).doSomething()</pre> | <pre>verify(mockObject, atLeast(-1)).doSomething()</pre> |
| <pre>verify(mockObject, atMost(2)).doSomething()</pre> | <pre>verify(mockObject, atMost(-1)).doSomething()</pre> |

It supports the `times`, `atLeast` and `atMost` verification modes.

**Script filter ($value$):**

```groovy
try {
    value?.text?.toInteger() < 0
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="Mockito.times/atLeast/atMost: Negative value is not allowed here." text="$Mockito$.verify($mockObject$, $Mockito$.$times$($value$)).$verifiedMethod$($arguments$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="times" regexp="times|atLeast|atMost" within="" contains="" />
    <constraint name="value" script="&quot;try {&#10;    value?.text?.toInteger() &lt; 0&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
    <constraint name="arguments" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## InOrder calls: Negative and zero values are not allowed here.

This is based on one of the exception handling happening in [`org.mockito.internal.verification.Calls`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/Calls.java)

In order to validate that methods are called in order, at least one call of a method is necessary.

| Compliant code | Non-compliant code |
|---|---|
| <pre>InOrder inOrder = Mockito.inOrder(mockObject);<br>inOrder.verify(mockObject, calls(2)).doSomething()</pre> | <pre>InOrder inOrder = Mockito.inOrder(mockObject);<br>inOrder.verify(mockObject, calls(-1)).doSomething()</pre> |
|  | <pre>InOrder inOrder = Mockito.inOrder(mockObject);<br>inOrder.verify(mockObject, calls(0)).doSomething()</pre> |

This inspection is not prepared for signaling calling `Mockito.calls()` in `Mockito.verify()` because it is designed to work only with `InOrder.verify()`.
But there is a specific inspection for that. See **Mockito.calls() is only intended to work with InOrder** below.

**Script filter ($value$)**

```groovy
try { 
    value?.text?.toInteger() <= 0
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="InOrder calls: Negative and zero values are not allowed here." text="$InOrder$.verify($mockObject$, $Mockito$.$calls$($value$)).$verifiedMethod$($arguments$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="value" script="&quot;try { &#10;    value?.text?.toInteger() &lt;= 0&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
    <constraint name="calls" regexp="calls" within="" contains="" />
    <constraint name="InOrder" nameOfExprType="org\.mockito\.InOrder" expressionTypes="org.mockito.InOrder" within="" contains="" />
    <constraint name="arguments" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## Mockito.calls() is only intended to work with InOrder

This is based on one of the exception handling happening in [`org.mockito.internal.verification.Calls`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/verification/Calls.java)

| Compliant code | Non-compliant code |
|---|---|
| <pre>InOrder inOrder = Mockito.inOrder(mockObject);<br>inOrder.verify(mockObject, calls(2)).doSomething()</pre> | <pre>Mockito.verify(mockObject, calls(5)).doSomething()</pre> |

**Template:**

```xml
<searchConfiguration name="Mockito.calls is only intended to work with InOrder" text="$Mockito$.verify($mockObject$, $Mockito$.$calls$($value$)).$verifiedMethod$($arguments$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="calls" regexp="calls" target="true" within="" contains="" />
    <constraint name="arguments" minCount="0" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## @Captor field must be of the type ArgumentCaptor

These are based on the exception handling happening in [`org.mockito.internal.configuration.CaptorAnnotationProcessor`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/configuration/CaptorAnnotationProcessor.java)

For a bit more details see the [`@Captor` annotation's javadoc](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Captor.html).

This is a collection of Replace templates that are most useful when all of them used, but it is up to everyone's needs which ones are actually used.

### Matching types other than primitive types and ArgumentCaptor

| Compliant code/Replacement | Non-compliant code |
|---|---|
| <pre>@Captor<br>private ArgumentCaptor<String> captor;</pre> | <pre>@Captor<br>private String captor;</pre> |

Since this is a replace template it provides a quick fix to replace the type with an argument captor.
Since the Search target of the template is the field type, the quick fix suggestion is visible only when the Alt+Enter menu is opened having
the cursor on the field type.

This inspection doesn't support ArgumentCaptor without a generic type, and the Java primitives because at least the latter ones need a bit more complex logic to map them to their
boxed type. For the latter ones please check out the inspections in the next section.

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

### Individual templates for each primitive types

To be able to replace the primitive types with their non-primitive variants, each needs its own separate replace template to
be able to apply a proper quick fix.

Each matches a single primitive type for the field type, and nothing else.

NOTE: primitive type arrays are not handled by these inspections.

<details>
  <summary>Expand to see inspections for primitive types...</summary>
  
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
</details>

## ArgumentCaptor field should define a generic type

Though it is not incorrect if a `@Captor` annotated `ArgumentCaptor` type field doesn't have an explicitly defined generic type, some projects might want to enforce
defining it in every use case.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Captor<br>ArgumentCaptor<String> captor;</pre> | <pre>@Captor<br>ArgumentCaptor captor;</pre> |

It won't signal the problem in any case when the generic type is defined, be it a specific type (e.g. String), wildcard (?) or a class level generic type (e.g. T).

**Template:**

```xml
<searchConfiguration name="@Captor field should have a generic type." text="class $Class$ {&#10;    @$CaptorAnnotation$()&#10;    @Modifier(&quot;Instance&quot;) $FieldType$&lt;$CapturedType$&gt; $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" regexp="org\.mockito\.ArgumentCaptor" target="true" within="" contains="" />
    <constraint name="Field" withinHierarchy="true" maxCount="2147483647" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="CaptorAnnotation" regexp="org\.mockito\.Captor" within="" contains="" />
    <constraint name="CapturedType" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## MockitoAnnotations.initMocks() argument cannot be null

This is based on the exception handling happening in [`org.mockito.MockitoAnnotations`](https://github.com/mockito/mockito/blob/28012a49cb1b1aab1b957ac5e7507dd8b807a47e/src/main/java/org/mockito/MockitoAnnotations.java)

The argument `MockitoAnnotations#initMocks()` cannot be null because the argument is meant to be the test class whose field need to be initialized.

| Compliant code | Non-compliant code |
|---|---|
| <pre>MockitoAnnotations.initMocks(this);</pre> | <pre>MockitoAnnotations.initMocks(null);</pre> |

This template can handle only the explicit `null` value since object null value can only be checked runtime.

**Template:**

```xml
<searchConfiguration name="MockitoAnnotations.initMocks() argument cannot be null" text="org.mockito.MockitoAnnotations.initMocks(null);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## '<method_name>' is a *void method* and it *cannot* be stubbed with a *return value*!

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#cannotStubVoidMethodWithAReturnValue(String)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

Since void methods have no return value, trying to mock such a method to return something is incorrect.

| Compliant code | Non-compliant code |
|---|---|
| Don't try to mock a return value to a void method. | <pre>Mockito.doReturn(5).when(mockObject).voidMethod();</pre> |
| You may have wanted to mock a return value to another non-void method. |  |
| You may have wanted to mock something else for the void method. |  |

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

Result of `Mockito.when()` calls are not supposed to be stored in a separate `OngoingStubbing` reference.

| Compliant code | Non-compliant code |
|---|---|
| <pre>when(mockObject.someMethod()).thenReturn("some text").thenThrow(new RuntimeException());</pre> | <pre>OngoingStubbing<String> ongoingStubbing = when(mockObject.someMethod());<br>ongoingStubbing.thenReturn("some text");<br>ongoingStubbing.thenThrow(new RuntimeException());</pre> |

But based on Mockito's behaviour the following construct is correct, so it is not included in the template:

```java
OngoingStubbing<String> ongoingStubbing = when(mockObject.someMethod());
ongoingStubbing.thenReturn("some text").thenThrow(new RuntimeException());
```

The template is prepared to handle additional expressions between the two `then...` calls on the OngoingStubbing object, so they won't interfere with inspecting the code,
and (hopefully) won't result in a false-negative.

The generic type of `OngoingStubbing` has no restrictions.

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

There are two different inspection signaling the two different constructs:

| Compliant code | Non-compliant code |
|---|---|
| There is a missing method call for `verify(mock)` |  |
| <pre>Mockito.verify(mockObject).hasSomeProperty()</pre> | <pre>Mockito.verify(mockObject);</pre> |
| Calls `equals()` or `hashCode()` from verify(mock) |  |
| According to Mockito's error message:<br>*"... this error might show up because you verify either of: final/private/equals()/hashCode() methods.<br>Those methods cannot be stubbed/verified."* | <pre>verify(mockObject).equals(otherObject);<br>verify(mockObject).hashCode();</pre> |

```xml
<searchConfiguration name="Missing method call for verify(mock)." text="org.mockito.Mockito.verify($mockObject$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
</searchConfiguration>
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

Both `verifyNoMoreInteractions()` and `verifyZeroInteractions()` have to passed at least one argument, the mocked/stubbed object on which they are supposed to
validate the number of interactions.

| Compliant code | Non-compliant code |
|---|---|
| <pre>verifyZeroInteractions(mockObject, anotherMockObject);</pre> | <pre>verifyZeroInteractions();</pre> |
| <pre>verifyNoMoreInteractions(mockObject);</pre> | <pre>verifyNoMoreInteractions();</pre> |

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

The `Mockito.inOrder()` method should be passed at least on argument, the mock objects on which you want to do in order validation.

| Compliant code | Non-compliant code |
|---|---|
| <pre>InOrder inOrder = Mockito.inOrder(mockObject, anotherMock);</pre> | <pre>InOrder inOrder = Mockito.inOrder();</pre> |

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

## Some (AdditionalMatchers) arguments don't use argument matchers. Either none or all of them must use matchers.

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#invalidUseOfMatchers(int, List)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)
and [`org.mockito.internal.exceptions.Reporter#incorrectUseOfAdditionalMatchers(String, int, Collection)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java).

When mocking arguments of a mocked method, either all or none of the arguments must use argument matchers, a mixed version is not allowed by Mockito. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>when(mockObject.mockMethod(ArgumentMatchers.<br>    any(SomeClass.class), eq(someExpression)))<br>        .thenReturn(aReturnValue);</pre> | <pre>when(mockObject.mockMethod(ArgumentMatchers<br>    .any(SomeClass.class), someExpression))<br>        .thenReturn(aReturnValue);</pre> |
| <pre>when(mockObject.mockMethod(SomeClass.class, someExpression))<br>    .thenReturn(aReturnValue);</pre> |  |
| <pre>when(mockObject.mockMethod(AdditionalMatchers<br>    .and(someExpression, SomeClass.class)))<br>        .thenReturn(aReturnValue);</pre> | <pre>when(mockObject.mockMethod(AdditionalMatchers.<br>    and(someExpression, any(SomeClass.class)))).<br>        thenReturn(aReturnValue);</pre> |
| <pre>when(mockObject.mockMethod(AdditionalMatchers.<br>    and(eq(someExpression), any(SomeClass.class))))<br>        .thenReturn(aReturnValue);</pre> |  |

For the template it doesn't matter where in the argument list the inconsistent arguments are (be it matcher or non-matcher), it will signal it as a problem.

**Script filter (Complete match):**

```groovy
boolean hasOnlyMatchers = true
boolean hasMatcher = false
arguments.each { it ->
	//If the argument is a method call
    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {
    	//A method call from ArgumentMatchers
        if (it?.resolveMethod()?.getContainingClass()?.getQualifiedName() == "org.mockito.ArgumentMatchers") {
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

**Template (ArgumentMatchers):**

```xml
<searchConfiguration name="Some arguments don't use argument matchers. Either none or all of them should use them." text="org.mockito.Mockito.when($mockObject$.$mockMethod$($arguments$))" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;boolean hasOnlyMatchers = true&#10;boolean hasMatcher = false&#10;arguments.each { it -&gt;&#10;&#9;//If the argument is a method call&#10;    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {&#10;    &#9;//A method call from ArgumentMatchers&#10;        if (it?.resolveMethod()?.getContainingClass()?.getQualifiedName() == &quot;org.mockito.ArgumentMatchers&quot;) {&#10;            hasMatcher = true&#10;        //A method call from a non-ArgumentMatchers class&#10;        } else {&#10;            hasOnlyMatchers = false&#10;        }&#10;    //Not a method call, but an other type of expression&#10;    } else {&#10;    &#9;hasOnlyMatchers = false&#10;    }&#10;}&#10;&#10;//If there is not only matchers, or not only non-matchers, then it will signal a problem.&#10;!((hasMatcher &amp;&amp; hasOnlyMatchers) || (!hasMatcher &amp;&amp; !hasOnlyMatchers))&quot;" within="" contains="" />
    <constraint name="arguments" minCount="2" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="mockMethod" within="" contains="" />
</searchConfiguration>
```

**Template (AdditionalMatchers):**

```xml
<searchConfiguration name="Some AdditionalMatchers arguments don't use argument matchers. Either none or all of them should use a matcher." text="org.mockito.AdditionalMatchers.$additionalMatcher$($arguments$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;boolean hasOnlyMatchers = true&#10;boolean hasMatcher = false&#10;arguments.each { it -&gt;&#10;    //If the argument is a method call&#10;    if (it instanceof com.intellij.psi.PsiMethodCallExpression) {&#10;        //A method call from ArgumentMatchers&#10;        if (it?.resolveMethod()?.getContainingClass()?.getQualifiedName() == &quot;org.mockito.ArgumentMatchers&quot;) {&#10;            hasMatcher = true&#10;        //A method call from a non-ArgumentMatchers class&#10;        } else {&#10;            hasOnlyMatchers = false&#10;        }&#10;    //Not a method call, but an other type of expression&#10;    } else {&#10;        hasOnlyMatchers = false&#10;    }&#10;}&#10;&#10;//If there is not only matchers, or not only non-matchers, then it will signal a problem.&#10;!((hasMatcher &amp;&amp; hasOnlyMatchers) || (!hasMatcher &amp;&amp; !hasOnlyMatchers))&quot;" within="" contains="" />
    <constraint name="additionalMatcher" within="" contains="" />
    <constraint name="arguments" minCount="2" maxCount="2147483647" target="true" within="" contains="" />
</searchConfiguration>
```

## Only void methods can doNothing().

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#onlyVoidMethodsCanBeSetToDoNothing()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

For details when calling `doNothing()` can be useful, please refer to the javadoc of `Mockito.doNothing()`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>doNothing().when(mock).aVoidMethod();</pre> | <pre>doNothing().when(mock).someNotVoidMethod();</pre> |

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

## @Mock extraInterfaces attribute is provided with at least one non-interface type

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#extraInterfacesAcceptsOnlyInterfaces(Class)`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

For more information about the creation of these templates head over to this [IntelliJ support ticket](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360004135280-SSR-Check-whether-an-attribute-value-immediate-class-type-is-an-interface).

The `extraInterfaces` attribute of the `@Mock` takes at least one type that is also an interface, 0 number of values and non-interface types are not allowed.

To make the implementation easier and simpler of this inspection, there are two separate templates.

### Single value

This template matches occurrences where the `extraInterfaces` attribute is provided a single value, without the array initializers, e.g.:

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Mock(extraInterfaces = AnInterfaceType.class)<br>public MockedClass mocked;</pre> | <pre>@Mock(extraInterfaces = NotAnInterface.class)<br>public MockedClass mocked;</pre> |

**Script filter (Complete match):**

```groovy
import com.intellij.psi.*
boolean hasOnlyInterfaces = true
if (attributes instanceof PsiClassObjectAccessExpression) {
  PsiJavaCodeReferenceElement classReference = (PsiJavaCodeReferenceElement)attributes.firstChild?.firstChild;
  if (!classReference?.resolve()?.isInterface()) {
    hasOnlyInterfaces = false
  }
}

!hasOnlyInterfaces
```

**Template:**

```xml
<searchConfiguration name="@Mock annotation: extraInterfaces() accepts only interfaces." text="@org.mockito.Mock($extraInterfaces$ = $attributes$)&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $field$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*;&#10;boolean hasOnlyInterfaces = true&#10;if (attributes instanceof PsiClassObjectAccessExpression) {&#10;  PsiJavaCodeReferenceElement classReference = (PsiJavaCodeReferenceElement)attributes.firstChild?.firstChild;&#10;  if (!classReference?.resolve()?.isInterface()) {&#10;    hasOnlyInterfaces = false&#10;  }&#10;}&#10;&#10;!hasOnlyInterfaces&quot;" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="field" within="" contains="" />
    <constraint name="extraInterfaces" regexp="extraInterfaces" within="" contains="" />
    <constraint name="attributes" target="true" within="" contains="" />
</searchConfiguration>
```

### Array value

This template matches occurrences where the `extraInterfaces` attribute is provided multiple values within an array initializer, e.g.:

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Mock(extraInterfaces = {AnInterface.class, AnotherInterface.class})<br>public MockedClass mocked;</pre> | <pre>@Mock(extraInterfaces = {NotAnInterface.class, AnInterface.class})<br>public MockedClass mocked;</pre> |

In order for this template to match, it is enough that at least one of the provided values is not an interface.

The target of this inspection is set to Complete Match to be consistent with the single value variant but regardless of which values are not
interfaces, all provided values are highlighted.

**Script filter (Complete match):**

```groovy
import com.intellij.psi.*
boolean hasOnlyInterfaces = true
attributes.each { attribute ->
    if (attribute instanceof PsiClassObjectAccessExpression) {
        PsiJavaCodeReferenceElement ref = (PsiJavaCodeReferenceElement)attribute.firstChild?.firstChild;
        if (!ref?.resolve()?.isInterface()) {
            hasOnlyInterfaces = false
        }
    }
}
!hasOnlyInterfaces
```

**Template:**

```xml
<searchConfiguration name="@Mock annotation: extraInterfaces() accepts only interfaces." text="@org.mockito.Mock($extraInterfaces$ = {$attributes$})&#10;@Modifier(&quot;Instance&quot;) $FieldType$ $field$;" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*;&#10;boolean hasOnlyInterfaces = true&#10;attributes.each { attribute -&gt;&#10;    if (attribute instanceof PsiClassObjectAccessExpression) {&#10;      PsiJavaCodeReferenceElement ref = (PsiJavaCodeReferenceElement)attribute.firstChild?.firstChild;&#10;      if (!ref?.resolve()?.isInterface()) {&#10;        hasOnlyInterfaces = false&#10;      }&#10;    }&#10;  }&#10;!hasOnlyInterfaces&quot;" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="field" within="" contains="" />
    <constraint name="extraInterfaces" regexp="extraInterfaces" within="" contains="" />
    <constraint name="attributes" maxCount="2147483647" target="true" within="" contains="" />
</searchConfiguration>
```

## Mockito.mock() extraInterfaces() requires at least one interface

This is based on the exception handling happening in [`org.mockito.internal.exceptions.Reporter#extraInterfacesRequiresAtLeastOneInterface()`](https://github.com/mockito/mockito/blob/53e8a93141e1f8c41d6b6d4fd72c20488826269a/src/main/java/org/mockito/internal/exceptions/Reporter.java)

The `extraInterfaces` attribute of the `@Mock` takes at least one type that is also an interface, 0 number of values and non-interface types are not allowed.

| Compliant code | Non-compliant code |
|---|---|
| <pre>withSettings().defaultAnswer(null).extraInterfaces(AnInterface.class, AnotherInterface.class).name("").lenient()</pre> | <pre>withSettings().defaultAnswer(null).extraInterfaces().name("").lenient()</pre> |

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

Mockito doesn't allow setting to call the real method of a mock object if the called method is abstract. The reason behind it is that abstract methods in Java don't have implementations,
thus there is no real logic to actually call.

| Compliant code | Non-compliant code |
|---|---|
| <pre>when(mockObject.nonAbstractMethod()).thenCallRealMethod();</pre> | <pre>when(mockObject.abstractMethod()).thenCallRealMethod();</pre> |

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

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Captor<br>ArgumentCaptor<MockObject> mockObjectCaptor;</pre> | <pre>@Mock<br>@Captor<br>ArgumentCaptor<MockObject> mockObjectCaptor;</pre> |

The only combination of annotations I've found this exception is thrown for is `@Mock` and `@Captor`.

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

Certain combinations of Mockito annotations are not allowed to be used on the same field:
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

Negative values are invalid to be defined for timeout and wait values, thus Mockito doesn't allow it either.

| Compliant code | Non-compliant code |
|---|---|
| <pre>Runnable runnable = Mockito.mock(Runnable.class);<br>Mockito.verify(runnable, Mockito.after(1000).never()).run();</pre> | <pre>Runnable runnable = Mockito.mock(Runnable.class);<br>Mockito.verify(runnable, Mockito.after(-1000).never()).run();</pre> |
| <pre>Runnable runnable = Mockito.mock(Runnable.class);<br>Mockito.verify(runnable, Mockito.timeout(1000)).run();</pre> | <pre>Runnable runnable = Mockito.mock(Runnable.class);<br>Mockito.verify(runnable, Mockito.timeout(-1000)).run();</pre> |

The example is from the [originally reported Mockito issue](https://github.com/mockito/mockito/issues/197).

**Script filter ($duration$):**

```groovy
try { 
    duration?.text?.toInteger() < 0
//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)
} catch (NumberFormatException nfe) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="Negative value of duration is not allowed as argument of timer methods (after(), timeout())" text="org.mockito.Mockito.$afterOrTimeout$($duration$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="afterOrTimeout" regexp="after|timeout" within="" contains="" />
    <constraint name="duration" script="&quot;try { &#10;    duration?.text?.toInteger() &lt; 0&#10;//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)&#10;} catch (NumberFormatException nfe) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

## X is not implemented to work with InOrder wrapped inside an Y

This is based on the exception handling happening in [`org.mockito.internal.verification.VerificationWrapperInOrderWrapper`](https://github.com/mockito/mockito/blob/d77b2fc8b544b7b0ca772e816fcc2ad560edaa03/src/main/java/org/mockito/internal/verification/VerificationWrapperInOrderWrapper.java)

For this type of exception/error the only example I found so far is `Mockito.after()` that is not designed to work with `InOrder`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>Mockito.verify(mockObject, Mockito.after(1000).atLeastOnce()).someMethodCall();</pre> | <pre>inOrder.verify(mockObject, Mockito.after(1000).atLeastOnce()).someMethodCall();</pre> |

Due to the limitation of the Structural Search and Replace tool, there are two separate templates.

| Calls where only the `after()` method is called | Calls with additional verification mode call after `after()` |
|---|---|
| <pre>inOrder.verify(mockObject, Mockito.after(1000)).someMethodCall();</pre> | <pre>inOrder.verify(mockObject, Mockito.after(1000).atLeastOnce()).someMethodCall();</pre> |

```xml
<searchConfiguration name="X is not implemented to work with InOrder wrapped inside this VerificationMode" text="$InOrder$.verify($mockObject$, $Mockito$.$after$($value$)).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="InOrder" nameOfExprType="org\.mockito\.InOrder" expressionTypes="org.mockito.InOrder" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="after" regexp="after" target="true" within="" contains="" />
</searchConfiguration>
```

```xml
<searchConfiguration name="X is not implemented to work with InOrder wrapped inside this VerificationMode" text="$InOrder$.verify($mockObject$, $Mockito$.$after$($value$).$verificationMode$()).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="InOrder" nameOfExprType="org\.mockito\.InOrder" expressionTypes="org.mockito.InOrder" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="value" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="after" regexp="after" target="true" within="" contains="" />
    <constraint name="verificationMode" within="" contains="" />
</searchConfiguration>
```

## This verification method is not implemented to work with InOrder

This is based on the exception handling happening in [`org.mockito.internal.InOrderImpl`](https://github.com/mockito/mockito/blob/4f72147c464c1a8a642d01fc3334e98e92b464cd/src/main/java/org/mockito/internal/InOrderImpl.java)

For this type of exception/error the only examples I found so far are `Mockito.only()` and `Mockito.atMost()` that are not designed to work with `InOrder`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>Mockito.verify(mockObject, Mockito.only()).someMethodCall();</pre> | <pre>inOrder.verify(mockObject, Mockito.only()).someMethodCall();</pre> |
| <pre>Mockito.verify(mockObject, Mockito.atMost(5)).someMethodCall();</pre> | <pre>inOrder.verify(mockObject, Mockito.atMost(5)).someMethodCall();</pre> |

**Template:**

```xml
<searchConfiguration name="This verification method is not implemented to work with InOrder" text="$InOrder$.verify($mockObject$, $Mockito$.$verificationMode$($value$)).$verifiedMethod$()" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="InOrder" nameOfExprType="org\.mockito\.InOrder" expressionTypes="org.mockito.InOrder" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="Mockito" regexp="org\.mockito\.Mockito" minCount="0" within="" contains="" />
    <constraint name="value" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="verifiedMethod" within="" contains="" />
    <constraint name="verificationMode" regexp="only|atMost" target="true" within="" contains="" />
</searchConfiguration>
```

## times(1) call may be removed. Mockito.verify(mockObject) behaves the same.

This inspection is based on the fact that calling `Mockito.verify(mockObject)` without calling `times()` works the same as `Mockito.verify(mockObject, times(1))`,
thus calling `times(1)` may be removed.

| Compliant code | Non-compliant code |
|---|---|
| <pre>Mockito.verify(mockObject)</pre> | <pre>Mockito.verify(mockObject, times(1))</pre> |

**Script filter ($value$):**

```groovy
try { 
    value?.text?.toInteger() == 1
//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)
} catch (NumberFormatException nfe) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="times(1) call may be removed. Mockito.verify(mockObject) behaves the same." text="org.mockito.Mockito.verify($mockObject$, org.mockito.Mockito.$times$($value$)).$validatedMethod$();" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="mockObject" within="" contains="" />
    <constraint name="value" script="&quot;try { &#10;    value?.text?.toInteger() == 1&#10;//Necessary to handle due to other Mockito method argument types like Mockito.mock(Runnable.class)&#10;} catch (NumberFormatException nfe) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
    <constraint name="validatedMethod" within="" contains="" />
    <constraint name="times" regexp="times" within="" contains="" />
</searchConfiguration>
```
