# Google Guava

#### Guava com.google.common.base.Preconditions.checkNotNull is in use
Replace template to *Apply quick fix to replace it to java.util.Objects.requireNonNull.*
```xml
<replaceConfiguration name="Guava com.google.common.base.Preconditions.checkNotNull is in use. Apply quick fix to replace it to java.util.Objects.requireNonNull." created="1521563590516" text="com.google.common.base.Preconditions.$CHECK_METHOD$($ARGUMENTS$)"
                        recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="java.util.Objects.requireNonNull($ARGUMENTS$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="ARGUMENTS" within="" contains="" />
    <constraint name="CHECK_METHOD" regexp="checkNotNull" within="" contains="" />
</replaceConfiguration>
```
