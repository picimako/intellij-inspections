# Hamcrest

#### Hamcrest matcher is not used with static import.
```xml
<searchConfiguration name="Hamcrest matcher is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains=""/>
    <constraint name="Instance" regexp="org.hamcrest.Matchers|org.hamcrest.CoreMatchers|org.hamcrest.MatcherAssert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
