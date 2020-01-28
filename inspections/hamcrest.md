# Hamcrest

- Hamcrest website: http://hamcrest.org
- Hamcrest on GitHub: https://github.com/hamcrest/JavaHamcrest

## Hamcrest matcher is not used with static import.

In many cases static importing a static method makes the code less cluttered and more readable which some people might
find true in case of Hamcrest matchers as well.

| Compliant code | Non-compliant code |
|---|---|
| <pre>isEmpty(someString)</pre> | <pre>Matchers.isEmpty(someString)</pre> |

Supported classes:
- `org.hamcrest.Matchers`
- `org.hamcrest.CoreMatchers`
- `org.hamcrest.MatcherAssert`

**Template:**

```xml
<searchConfiguration name="Hamcrest matcher is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains=""/>
    <constraint name="Instance" regexp="org.hamcrest.Matchers|org.hamcrest.CoreMatchers|org.hamcrest.MatcherAssert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
