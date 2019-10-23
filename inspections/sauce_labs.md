# Sauce Labs

## The sauce:options capability is meant to be used with MutableCapabilities and specific driverOptions classes

According to [SauceLabs' documentation](https://wiki.saucelabs.com/display/DOCS/Selenium+W3C+Capabilities+Support)
the `sauce:options` capability was introduced with the support for W3C-compliant Selenium capabilities and protocol.

This inspection would signal a code snippet like the following, as incorrect:

```java
DesiredCapabilities capabilities = new DesiredCapabilities();
MutableCapabilities sauceOptions = getSauceOptions();

capabilities.setCapability("sauce:options", sauceOptions);
```

The validation is in place only for whether the type of the `capabilities` object is `DesiredCapabilities` because that
means you are using the JSON Wire Protocol and not W3C, which handles SauceLabs capabilities in a different way.

One may set the capability in a more general way like the following which won't be signaled by this inspection:

```java
MutableCapabilities capabilities = new DesiredCapabilities();
MutableCapabilities sauceOptions = getSauceOptions();

capabilities.setCapability("sauce:options", sauceOptions);
```

**Template:**

```xml
<searchConfiguration name="sauce:options is meant to be used with MutableCapabilities and specific driverOptions classes instead of DesiredCapabilities." text="$capabilities$.setCapability(&quot;sauce:options&quot;, $sauceOptions$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="capabilities" nameOfExprType="org\.openqa\.selenium\.remote\.DesiredCapabilities" expressionTypes="org.openqa.selenium.remote.DesiredCapabilities" within="" contains="" />
  <constraint name="sauceOptions" within="" contains="" />
</searchConfiguration>
```

## Incorrect SauceLabs capability name. It should be sauce:options.

According to [SauceLabs' documentation](https://wiki.saucelabs.com/display/DOCS/Selenium+W3C+Capabilities+Support)
there is a new `sauce:options` capability introduced in which one can set SauceLabs specific capabilities. Though it may
happen that one doesn't specify the capability name correctly and has a typo in it.

At the moment this template supports only the `sauce:options` capability (since that is only one with the `sauce:` prefix),
in the future it can be extended to any SauceLabs capability names that they may introduce.

This inspection would signal a code snippet like the following, as incorrect:

```java
DesiredCapabilities capabilities = new DesiredCapabilities();
capabilities.setCapability("sauce:opts", sauceOptions);
```

The type of the `capabilities` object may be `MutableCapabilities` and anything that extends it.

**Script filter ($sauceOptionsCapsName$)**

```groovy
sauceOptionsCapsName.value?.startsWith("sauce") && !sauceOptionsCapsName.value?.equals("sauce:options") 
```

**Template:**

```xml
<searchConfiguration name="Incorrect SauceLabs capability name. It should be sauce:options." text="$capabilities$.setCapability(&quot;$sauceOptionsCapsName$&quot;, $sauceOptions$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="capabilities" nameOfExprType="org\.openqa\.selenium\.MutableCapabilities" expressionTypes="org.openqa.selenium.MutableCapabilities" exprTypeWithinHierarchy="true" within="" contains="" />
    <constraint name="sauceOptions" within="" contains="" />
    <constraint name="sauceOptionsCapsName" script="&quot;sauceOptionsCapsName.value?.startsWith(&quot;sauce&quot;) &amp;&amp; !sauceOptionsCapsName.value?.equals(&quot;sauce:options&quot;) &quot;" within="" contains="" />
</searchConfiguration>
```
