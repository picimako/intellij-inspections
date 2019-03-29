# AssertJ

#### AssertJ assertion or assumption is not used with static import.
```xml
<searchConfiguration name="AssertJ assertion or assumption is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false"
                   caseInsensitive="true" type="JAVA">
<constraint name="Instance"
            regexp="org\.assertj\.core\.api\.(Assertions|Assumptions|AssertionsForClassTypes|AssertionsForInterfaceTypes|BDDAssertions|Java6Assertions|Java6BDDAssertions"
            within="" contains=""/>
<constraint name="MethodCall" target="true" within="" contains=""/>
<constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```

#### There is no actual assertion called from AssertJ.
```xml
<searchConfiguration name="There is no actual assertion called from AssertJ." text="org.assertj.core.api.Assertions.$ASSERTION_METHOD$($PARAMETER$);" recursive="true" caseInsensitive="true" type="JAVA">
<constraint name="ASSERTION_METHOD" regexp="assertThat(|ThrownBy|Code|ExceptionOfType|NullPointerException|IllegalArgumentException|IOException|IllegalStateException)" within="" contains="" />
<constraint name="PARAMETER" within="" contains="" />
<constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

#### There is no actual assertion called from AssertJ SoftAssertions.
```xml
<searchConfiguration name="There is no actual assertion called from AssertJ SoftAssertions." text="$INSTANCE$.$ASSERTION_METHOD$($PARAMETER$);" recursive="false"
                   caseInsensitive="true" type="JAVA">
  <constraint name="ASSERTION_METHOD" regexp="assertThat|assertThatCode|assertThatThrownBy" within="" contains=""/>
  <constraint name="PARAMETER" within="" contains=""/>
  <constraint name="INSTANCE" nameOfExprType="org\.assertj\.core\.api\.SoftAssertions" within="" contains=""/>
</searchConfiguration>
```
