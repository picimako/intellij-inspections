# Inspections from all tutorials
The purpose of this document is to collect all inspection that are created during the tutorials on [IJnspector](https://ijnspector.wordpress.com/)
mainly because WordPress keeps resolving unicode entities that shouldn't be resolved in order to users be able to copy and paste these inspections
into their project files.

## 2018.10.05. - [Class name doesn't end with a certain keyword](https://ijnspector.wordpress.com/2018/10/15/class-name-doesnt-end-with-a-certain-keyword/)
```xml
<searchConfiguration name="Class name doesn't end with the Feature keyword" text="class $Class$ {}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" regexp=".*(?&lt;!Feature)$" target="true" within="" contains="" />
</searchConfiguration>
```

## 2018.10.15. - [Annotated class name doesn't end with a keyword](https://ijnspector.wordpress.com/2018/10/15/annotated-class-name-doesnt-end-with-a-keyword/)
```xml
<searchConfiguration name="Annotated class name doesn't end with a keyword" text="@$Annotation$(  )&#10;class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Annotation" regexp="picimako\.tutorial\.example\.annotation\.Feature" within="" contains="" />
    <constraint name="Class" regexp=".*(?&lt;!Feature)$" target="true" within="" contains="" />
</searchConfiguration>
```

## 2018.10.17. - [Class having name ending with Feature is not annotated as Feature](https://ijnspector.wordpress.com/2018/10/17/class-having-name-ending-with-feature-is-not-annotated-as-feature/)
```xml
<searchConfiguration name="Class, having name ending with Feature, is not annotated as Feature." text="@$Annotation$( )&#10;class $Class$ {}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Annotation" regexp="picimako\.tutorial\.example\.annotation\.Feature" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" regexp=".*Feature$" target="true" within="" contains="" />
</searchConfiguration>
```

## 2018.10.19. - [Class, having name ending with Feature, does not extend a certain class](https://ijnspector.wordpress.com/2018/10/19/class-having-name-ending-with-feature-does-not-extend-a-certain-class/)
```xml
<searchConfiguration name="Class, having name ending with Feature, does not extend a certain class" text="class $Class$ extends $Parent$ {}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" regexp=".*Feature$" target="true" within="" contains="" />
    <constraint name="Parent" regexp="^picimako\.tutorial\.example\.BaseFeature$" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## 2018.10.22. - [A static method is not used with static import](https://ijnspector.wordpress.com/2018/10/22/a-static-method-is-not-used-with-static-import/)
```xml
<searchConfiguration name="Mockito method is not used with static import." text="$Instance$.$MethodCall$($Parameter$)" recursive="false" caseInsensitive="true" type="JAVA">
  <constraint name="__context__" within="" contains=""/>
  <constraint name="Instance" regexp="org\.mockito\.Mockito" within="" contains=""/>
  <constraint name="MethodCall" target="true" within="" contains=""/>
  <constraint name="Parameter" minCount="0" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```

## 2018.10.26. - [BDD step definition method has more than X arguments](https://ijnspector.wordpress.com/2018/10/26/bdd-step-definition-method-has-more-than-x-arguments/)
```xml
<searchConfiguration name="Step definition method has more than 3 parameters. Consider reorganizing or splitting steps." text="@$Annotation$( )&#10;$MethodType$ $Method$($ParameterType$ $Parameter$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="Annotation" regexp="cucumber\.api\.java\.en\.(Given|When|Then)" within="" contains="" />
    <constraint name="MethodType" within="" contains="" />
    <constraint name="Method" target="true" within="" contains="" />
    <constraint name="ParameterType" minCount="4" maxCount="2147483647" within="" contains="" />
    <constraint name="Parameter" minCount="4" maxCount="2147483647" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## 2018.10.29 - [There is no actual assertion called from AssertJ](https://ijnspector.wordpress.com/2018/10/29/there-is-no-actual-assertion-called-from-assertj/)
```xml
<searchConfiguration name="There is no actual assertion called from AssertJ." text="org.assertj.core.api.Assertions.$ASSERTION_METHOD$($PARAMETER$);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="ASSERTION_METHOD" regexp="assertThat(|ThrownBy|Code|ExceptionOfType|NullPointerException|IllegalArgumentException|IOException|IllegalStateException)" within="" contains="" />
    <constraint name="PARAMETER" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## 2018.11.02. - [There is no actual assertion called from AssertJ SoftAssertions](https://ijnspector.wordpress.com/2018/11/02/there-is-no-actual-assertion-called-from-assertj-softassertions/)
```xml
<searchConfiguration name="There is no actual assertion called from AssertJ SoftAssertions." text="$INSTANCE$.$ASSERTION_METHOD$($PARAMETER$);" recursive="false"
                       caseInsensitive="true" type="JAVA">
    <constraint name="ASSERTION_METHOD" regexp="assertThat|assertThatCode|assertThatThrownBy" within="" contains=""/>
    <constraint name="PARAMETER" within="" contains=""/>
    <constraint name="INSTANCE" nameOfExprType="org\.assertj\.core\.api\.SoftAssertions" within="" contains=""/>
</searchConfiguration>
```

## 2018.11.05. - [Annotation with a certain attribute value is invalid](https://ijnspector.wordpress.com/2018/11/05/annotation-with-a-certain-attribute-value-is-invalid/)
Retry annotation with 0 retry counts is invalid. Apply quick fix to remove the annotation:
```xml
<replaceConfiguration name="Retry annotation with 0 retry counts is invalid. Apply quick fix to remove the annotation." text="@picimako.tutorial.example.annotation.Retry(times = 0)"
    recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="" />
```

Retry annotation with 0 retry counts is invalid. Apply quick fix to remove the times attribute:
```xml
<replaceConfiguration name="Retry annotation with 0 retry counts is invalid. Apply quick fix to remove the times attribute." text="@picimako.tutorial.example.annotation.Retry(times = 0)"
    recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="@picimako.tutorial.example.annotation.Retry" />
```

## 2018.11.09 - [Annotation is used with default attribute value](https://ijnspector.wordpress.com/2018/11/09/annotation-is-used-with-default-attribute-value/)
```xml
<replaceConfiguration name="Retry annotation is used with default times value. Apply quick fix to remove the attribute." text="@picimako.tutorial.example.annotation.Retry(times = 1)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="@picimako.tutorial.example.annotation.Retry">
    <constraint name="__context__" target="true" within="" contains="" />
</replaceConfiguration>
```

## 2018.11.12. - [Use native Java Objects.requireNonNull instead of Guava Preconditions.checkNotNull](https://ijnspector.wordpress.com/2018/11/12/use-native-java-objects-requirenonnull-instead-of-guava-preconditions-checknotnull/)
```xml
<replaceConfiguration name="Guava com.google.common.base.Preconditions.checkNotNull is in use. Apply quick fix to replace it to java.util.Objects.requireNonNull." created="1521563590516" text="com.google.common.base.Preconditions.$CHECK_METHOD$($ARGUMENTS$)"
    recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="java.util.Objects.requireNonNull($ARGUMENTS$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="ARGUMENTS" within="" contains="" />
    <constraint name="CHECK_METHOD" regexp="checkNotNull" within="" contains="" />
</replaceConfiguration>
```

## 2018.11.16. - ["".equals() call can be replaced with String.isEmpty()](https://ijnspector.wordpress.com/2018/11/16/equals-call-can-be-replaced-with-string-isempty/)
```xml
<replaceConfiguration name="&quot;&quot;.equals() call can be replaced by String.isEmpty()" text="&quot;&quot;.equals($STRING_ARGUMENT$)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="$STRING_ARGUMENT$.isEmpty()">
    <constraint name="STRING_ARGUMENT" regexp="^&quot;.*&quot;$" nameOfExprType="java\.lang\.String" expressionTypes="java.lang.String" negateName="true" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</replaceConfiguration>
```

## 2018.11.19. - [Default JSON comparison mode is LENIENT, no need to specify explicitly](https://ijnspector.wordpress.com/2018/11/19/default-json-comparison-mode-is-lenient-no-need-to-specify-explicitly/)
```xml
<replaceConfiguration name="Default JSON comparison mode is LENIENT, no need to specify explicitly." text="$ASSERTION_FLOW$.matchesJson($MODEL$, org.skyscreamer.jsonassert.JSONCompareMode.LENIENT);" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="$ASSERTION_FLOW$.matchesJson($MODEL$);">
    <constraint name="__context__" target="true" within="" contains="" />
    <constraint name="ASSERTION_FLOW" within="" contains="" />
    <constraint name="MODEL" within="" contains="" />
</replaceConfiguration>
```

## 2018.11.23. - [Class containing FindBy/FindBys/FindAll annotated field(s) is not annotated with page object annotation](https://ijnspector.wordpress.com/2018/11/23/class-containing-findby-findbys-findall-annotated-fields-is-not-annotated-with-page-object-annotation/)
```xml
<searchConfiguration name="Class containing FindBy/FindBys/FindAll annotated field(s) is not annotated as Page." text="@$PageAnnotation$( )&#10;class $Class$ {&#10;    @$FindAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ &lt;$GenericArgument$&gt; $Field$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="PageAnnotation" regexp="picimako\.tutorial\.annotation\.Page" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" target="true" within="" contains="" />
    <constraint name="FindAnnotation" regexp="org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAll)" within="" contains="" />
    <constraint name="FieldType" regexp="org\.openqa\.selenium\.WebElement|java\.util\.List" within="" contains="" />
    <constraint name="Field" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
    <constraint name="GenericArgument" regexp="org\.openqa\.selenium\.WebElement" minCount="0" within="" contains="" />
</searchConfiguration>
```

## 2018.11.26 - [WebElement type fields in page object classes are not annotated with FindBy/FindBys/FindAll](https://ijnspector.wordpress.com/2018/11/26/webelement-type-fields-in-page-object-classes-are-not-annotated-with-findby-findbys-findall/)
```xml
<searchConfiguration name="WebElement/List&lt;WebElement&gt; type field is not annotated as FindBy, FindBys or FindAll" text="@$PageAnnotation$&#10;class $Class$ {&#10;    @$FindAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ &lt;$GenericArgument$&gt; $Field$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="PageAnnotation" regexp="picimako\.tutorial\.example\.annotation\.Page" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FindAnnotation" regexp="org\.openqa\.selenium\.support\.(FindBy|FindBys|FindAll)" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="FieldType" regexp="org\.openqa\.selenium\.WebElement|java\.util\.List" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="GenericArgument" regexp="org\.openqa\.selenium\.WebElement" minCount="0" within="" contains="" />
</searchConfiguration>
```

## 2018.11.30. - [Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead.](https://ijnspector.wordpress.com/2018/11/30/slf4j-logger-is-defined-explicitly-use-lombok-slf4j-annotation-instead/)
```xml
<searchConfiguration name="Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead." text="private static final $Logger$ $LOG$ = $LoggerFactory$.getLogger($Class$.class);" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Logger" regexp="org\.slf4j\.Logger" within="" contains="" />
    <constraint name="LoggerFactory" regexp="org\.slf4j\.LoggerFactory" within="" contains="" />
    <constraint name="LOG" target="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

## 2018.12.03 - [Lombok @Builder.Default field is not initialized](https://ijnspector.wordpress.com/2018/12/03/lombok-builder-default-field-is-not-initialized/)
```xml
<searchConfiguration name="@Builder.Default field is not initialized. Either remove the annotation or initialize the field explicitly." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    @$SingularAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```

## 2018.12.07. - [Static field in a @Builder class is annotated as Lombok @Builder.Default](https://ijnspector.wordpress.com/2018/12/07/static-field-in-a-builder-class-is-annotated-as-lombok-builder-default/)
```xml
<searchConfiguration name="Static field in a @Builder class is annotated as @Builder.Default, which has no effect. It can be removed." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    static $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## 2018.12.10 - [Field in a non-@Builder class is annotated as @Builder.Default](https://ijnspector.wordpress.com/2018/12/10/field-in-a-non-builder-class-is-annotated-as-builder-default/)
```xml
<searchConfiguration name="Field in a non-@Builder class is annotated as @Builder.Default. It has no effect, thus can be removed." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
</searchConfiguration>
```

## 2018.12.14. - [A @Singular field is explicitly initialized as an empty collection](https://ijnspector.wordpress.com/2018/12/14/a-singular-field-is-explicitly-initialized-as-an-empty-collection/)
```xml
<searchConfiguration name="Explicit @Singular field initialization as empty collection can be removed, @Singular itself initializes it as an empty collection." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$SingularAnnotation$( )&#10;    @$BuilderDefaultAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" regexp="(java\.util\.(List|ArrayList|Iterable|Collection|((Sorted|Navigable|Hash|Tree|)(Set|Map))))|(com\.google\.common\.collect\.Immutable(Collection|List|Set|SortedSet|Map|BiMap|SortedMap|Table))" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" regexp="(new (ArrayList|HashSet|TreeSet|HashMap|TreeMap)&lt;&gt;\(\))|((ImmutableList|ImmutableMap|ImmutableBiMap|ImmutableSortedMap|ImmutableSet|ImmutableSortedSet|ImmutableTable)\.of\(\))" within="" contains="" />
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

## 2018.12.18. - [Lombok @NonNull annotation is superfluous on a @Singular field](https://ijnspector.wordpress.com/2018/12/18/lombok-nonnull-annotation-is-superfluous-on-a-singular-field/)
```xml
<searchConfiguration name="@NonNull annotation is applied to a @Singular field. @Singular fields have no way to be set as null." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$SingularAnnotation$( )&#10;    @$NonNullAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" within="" contains="" />
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" within="" contains="" />
</searchConfiguration>
```

## 2018.12.22. - [Primitive type field, method parameter or local variable is annotated as Lombok @NonNull](https://ijnspector.wordpress.com/2018/12/22/primitive-type-field-method-parameter-or-local-variable-is-annotated-as-lombok-nonnull/)
```xml
<searchConfiguration name="Primitive type field, method parameter or local variable is annotated as Lombok @NonNull." text="@$NonNullAnnotation$ ( )&#10;$Type$ $Name$ = $Init$;" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="Type" regexp="byte|short|int|long|float|double|boolean|char" within="" contains="" />
    <constraint name="Name" target="true" within="" contains="" />
</searchConfiguration>
```

## 2018.12.28. - [Lombok @NonNull annotation is used on a method parameter](https://ijnspector.wordpress.com/2018/12/28/lombok-nonnull-annotation-is-used-on-a-method-parameter/)
```xml
<searchConfiguration name="Lombok @NonNull is used on a method parameter. You may replace it with an explicit check e.g. java.util.Objects.requireNonNull." text="$ReturnType$ $Method$(@$NonNullAnnotation$ () $ParameterType$ $Parameter$);" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="ReturnType" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" maxCount="2147483647" within="" contains="" />
    <constraint name="Parameter" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## 2019.01.30. - [Annotation attribute value is greater than the max allowed](https://ijnspector.wordpress.com/2019/01/30/annotation-attribute-value-is-greater-than-the-max-allowed/)
```xml
<replaceConfiguration name="Retry count is greater than the max allowed (5). Apply quick fix to use the max allowed value." text="@picimako.tutorial.annotation.Retry(times = $RetryCount$)" recursive="true" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="@picimako.tutorial.annotation.Retry(times = 5)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="RetryCount" script="&quot;RetryCount.text.toInteger() &gt; 5&quot;" within="" contains="" />
</replaceConfiguration>
```

```xml
<searchConfiguration name="Retry count is greater than the max allowed (5)." text="@picimako.tutorial.annotation.Retry(times = $RetryCount$)" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="RetryCount" script="&quot;RetryCount.text.toInteger() &gt; 5&quot;" within="" contains="" />
</searchConfiguration>
```

## 2018.03.25 - [Mockito cannot mock/spy final classes]()
```xml
<searchConfiguration name="Mockito cannot mock/spy final classes." text="class $Class$ { &#10;    @$MockAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" within="" contains="" />
    <constraint name="Field" script="&quot;Field.getType().resolve().hasModifierProperty(&quot;final&quot;)&quot;" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="MockAnnotation" regexp="org\.mockito\.(Mock|Spy)" within="" contains="" />
</searchConfiguration>
```
