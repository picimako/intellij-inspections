# JUnit

#### JUnit assertion is not used with static import
```xml
<searchConfiguration name="JUnit assertion is not used with static import" text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Instance" regexp="org.junit.Assert" within="" contains=""/>
    <constraint name="MethodCall" target="true" within="" contains=""/>
    <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
