# Google Truth

- Truth website: https://truth.dev
- Truth on GitHub: https://github.com/google/truth

## Google Truth assertion is not used with static import.

In many cases static importing a static method makes the code less cluttered and more readable which some people might
find true in case of Google Truth assertions as well.

| Compliant code | Non-compliant code |
|---|---|
| <pre>assertThat(someString).isEmpty();</pre> | <pre>Truth.assertThat(someString).isEmpty();</pre> |

Supported classes:
- `com.google.common.truth.Truth`
- `com.google.common.truth.TruthJUnit`

**Template:**

```xml
<searchConfiguration name="Google Truth assertion is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true"
                       type="JAVA">
    <constraint name="Instance" regexp="com.google.common.truth.Truth|com.google.common.truth.TruthJUnit" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
