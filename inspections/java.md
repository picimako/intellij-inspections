# Java

#### "".equals() call can be replaced by String.isEmpty()
```xml
<replaceConfiguration name="&quot;&quot;.equals() call can be replaced by String.isEmpty()" text="&quot;&quot;.equals($STRING_ARGUMENT$)" recursive="false" caseInsensitive="true" type="JAVA" reformatAccordingToStyle="true" shortenFQN="true" useStaticImport="true" replacement="$STRING_ARGUMENT$.isEmpty()">
    <constraint name="STRING_ARGUMENT" regexp="^&quot;&quot;$" nameOfExprType="java\.lang\.String" negateName="true" within="" contains="" />
</replaceConfiguration>
```
