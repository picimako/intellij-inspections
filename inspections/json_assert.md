# JSONAssert

- JSONAssert website: http://jsonassert.skyscreamer.org
- JSONAssert on GitHub: https://github.com/skyscreamer/JSONassert

## JSONAssert assertion is not used with static import

In many cases static importing a static method makes the code less cluttered and more readable which some people might find true in case of JSONAssert assertions as well.

This inspection would signal code snippets like the following, as incorrect:

```java
JSONAssert.assertEquals(aJsonString, anotherJsonString)
```

Supported assertion methods:
- `assertEquals`
- `assertNotEquals`

**Template:**

```xml
<searchConfiguration name="JSONAssert assertion is not used with static import" text="$JSON_ASSERT$.$ASSERTION$($PARAMETER$)" recursive="false" caseInsensitive="true"
                       type="JAVA">
    <constraint name="JSON_ASSERT" regexp="org.skyscreamer.jsonassert.JSONAssert" within="" contains=""/>
    <constraint name="ASSERTION" regexp="assertEquals|assertNotEquals" target="true" within="" contains=""/>
    <constraint name="PARAMETER" minCount="2" maxCount="2147483647" within="" contains=""/>
</searchConfiguration>
```
