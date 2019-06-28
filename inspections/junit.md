# JUnit

## JUnit assertion is not used with static import

In many cases static importing a static method makes the code less cluttered and more readable which some people might find true in case of JUnit assertions as well.

This inspection would signal code snippets like the following, as incorrect:

```java
Assert.assertEquals(someString, anotherString)
```

**Template:**

```xml
<searchConfiguration name="JUnit assertion is not used with static import" text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Instance" regexp="org.junit.Assert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
