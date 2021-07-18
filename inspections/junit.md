# JUnit

- JUnit 4 website: https://junit.org/junit4/
- JUnit 5 website: https://junit.org/junit5/
- JUnit on GitHub: https://github.com/junit-team

You can find more JUnit specific inspections in the IntelliJ Inspections settings under **Java > JUnit**.

## JUnit assertion is not used with static import

In many cases static importing a static method makes the code less cluttered and more readable which some people might
find true in case of JUnit assertions as well.

| Compliant code | Non-compliant code |
|---|---|
| <pre>assertEquals(someString, anotherString)</pre> | <pre>Assert.assertEquals(someString, anotherString)</pre> |

**Template:**

```xml
<searchConfiguration name="JUnit assertion is not used with static import" text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Instance" regexp="org.junit.Assert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```

## JUnit 4 @Test method body wrapped in try-catch block

Having a unit test method wrapped in a try-catch block and handling the exceptions only by logging something, covers up any problem that occur during the execution
of the method.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Test<br>public void shouldTestSomething() {<br>  //do stuff<br>  //do another stuff<br>  //do something else<br>}</pre> | <pre>@Test<br>public void shouldTestSomething() {<br>  try {<br>    //do stuff<br>    //do another stuff<br>    //do something else<br>  } catch (Exception e) {<br>    LOG.error("Some not that specific error message", e);<br>  }<br>}</pre> |

**Template:**

```xml
<searchConfiguration name="Unit test method should not be in a try-catch block" text="class $Class$ {&#10;  @$Test$&#10;  void $Method$ ($ParameterType$ $Parameter$) {&#10;      try {&#10;          $statements$;&#10;      } catch ($exception$ $ex$) {&#10;          $LOG$.$level$($parameters$);&#10;      }&#10;  }&#10;}" recursive="false" caseInsensitive="false" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum &amp;&amp; !__context__.record&quot;" regexp=".*Test$" within="" contains="" />
  <constraint name="Method" withinHierarchy="true" maxCount="2147483647" target="true" within="" contains="" />
  <constraint name="ParameterType" within="" contains="" />
  <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains="" />
  <constraint name="ex" maxCount="2147483647" within="" contains="" />
  <constraint name="exception" regexp="java.lang.Exception|java.lang.Throwable" within="" contains="" />
  <constraint name="LOG" nameOfExprType="org\.slf4j\.Logger" within="" contains="" />
  <constraint name="level" regexp="info|error|warn|debug|trace" within="" contains="" />
  <constraint name="parameters" minCount="0" maxCount="2147483647" within="" contains="" />
  <constraint name="statements" maxCount="2147483647" within="" contains="" />
  <constraint name="Test" regexp="org.junit.Test" within="" contains="" />
</searchConfiguration>
```
