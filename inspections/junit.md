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
