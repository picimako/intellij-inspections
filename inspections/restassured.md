# REST Assured

- REST Assured website: http://rest-assured.io
- REST Assured on GitHub: https://github.com/rest-assured/rest-assured

## Table contents

#### Requests
- [RequestSpecification port must be greater than 0](#requestspecification-port-must-be-greater-than-0)
- [Request URI cannot end with ?](#request-uri-cannot-end-with-)

#### Responses
- [ResponseBuilder: Status code must be greater than 100 and less than 600](#responsebuilder-status-code-must-be-greater-than-100-and-less-than-600)

#### RestAssured configuration
- [CloseIdleConnectionConfig: Idle time cannot be less than 0](#closeidleconnectionconfig-idle-time-cannot-be-less-than-0)
- [ProxySpecification: Cannot determine proxy port](#proxyspecification-cannot-determine-proxy-port)

## Candidates

- A few `body()` methods of [io.restassured.module.mockmvc.specification.MockMvcRequestSpecification](https://github.com/rest-assured/rest-assured/blob/ce55c98bd9441724182c18dc1bc9bb7b38e85cef/modules/spring-mock-mvc/src/main/java/io/restassured/module/mockmvc/specification/MockMvcRequestSpecification.java) that throw exception in case of certain unsupported HTTP request methods.

## RequestSpecification port must be greater than 0

According to the exception handling in [`io.restassured.internal.RequestSpecificationImpl#port(int)`](https://github.com/rest-assured/rest-assured/blob/38e0188dcf9bab3ce81c6e787e69e480929cb544/rest-assured/src/main/groovy/io/restassured/internal/RequestSpecificationImpl.groovy)
and network protocols in general, consider port numbers valid only if they are greater than 0. (0 may be a reserved port number not recommended to be used.)

| Compliant code | Non-compliant code |
|---|---|
| <pre>RestAssured.given().port(1000);</pre> | <pre>RestAssured.given().port(0);</pre> |
|  | <pre>RestAssured.given().port(-2);</pre> |

**Script filter ($port$):**

```groovy
try {
    port.text?.toLong() < 1 && port.text?.toLong() != -1
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="RequestSpecification port must be greater than 0" text="$requestSpecification$.port($port$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="port" script="&quot;try {&#10;    port.text?.toLong() &lt; 1 &amp;&amp; port.text?.toLong() != -1&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
    <constraint name="requestSpecification" nameOfExprType="io\.restassured\.specification\.RequestSpecification" expressionTypes="io.restassured.specification.RequestSpecification" within="" contains="" />
</searchConfiguration>
```

## Request URI cannot end with ?

This is based on the exception handling happening in [`io.restassured.internal.RequestSpecificationImpl#newFilterContext(assertionClosure, filters, properties)`](https://github.com/rest-assured/rest-assured/blob/38e0188dcf9bab3ce81c6e787e69e480929cb544/rest-assured/src/main/groovy/io/restassured/internal/RequestSpecificationImpl.groovy).

The reason behind this check might be that a ? character in a request URI signals that URI parameters follow, but if the URI ends with ?, it means that there is no parameter defined,
considering it as an invalid value.

This check is split into two inspections, one targeting methods name as specific request method, the other targeting more general methods named `request()`.

### Specific HTTP request methods

| Compliant code | Non-compliant code |
|---|---|
| <pre>RestAssured.post("some/path/{param1}/{param2}", param1, param2);</pre> | <pre>RestAssured.post("some/param/{param1}/{param2}?", param1, param2);</pre> |
| <pre>RequestSpecification spec = RestAssured.given();<br>spec.post("some/path/{param1}/{param2}", param1, param2);</pre> | <pre>RequestSpecification spec = RestAssured.given();<br>spec.post("some/uri/{param1}/{param2}?", param1, param2);</pre> |

It supports all of the following methods from `RequestSpecification`:
- `get(String, Object...)` / `get(String, Map)`
- `post(String, Object...)` / `post(String, Map)`
- `put(String, Object...)` / `put(String, Map)`
- `delete(String, Object...)` / `delete(String, Map)`
- `head(String, Object...)` / `head(String, Map)`
- `patch(String, Object...)` / `patch(String, Map)`
- `options(String, Object...)` / `options(String, Map)`

**Template:**

Instantiated case:
```xml
<searchConfiguration name="RequestSpecification: Request URI cannot end with ?" text="$RequestSpecification$.$httpRequestMethod$(&quot;$requestUri$&quot;, $parameters$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="RequestSpecification" nameOfExprType="io\.restassured\.specification\.RequestSpecification" expressionTypes="io.restassured.specification.RequestSpecification" within="" contains="" />
    <constraint name="httpRequestMethod" regexp="get|post|put|delete|head|patch|options" within="" contains="" />
    <constraint name="parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="requestUri" script="&quot;requestUri.value?.endsWith(&quot;?&quot;)&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

Static method case:
```xml
<searchConfiguration name="RequestSpecification: Request URI cannot end with ?" text="io.restassured.RestAssured.$httpRequestMethod$(&quot;$requestUri$&quot;, $parameters$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="httpRequestMethod" regexp="get|post|put|delete|head|patch|options" within="" contains="" />
    <constraint name="parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="requestUri" script="&quot;requestUri.value?.endsWith(&quot;?&quot;)&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

**Script template ($requestUri$):**

```groovy
requestUri.value?.endsWith("?")
```

### General HTTP request methods

| Compliant code | Non-compliant code |
|---|---|
| <pre>RequestSpecification spec = RestAssured.given();<br>spec.request("post", "path/{param}", param);<br>spec.request(Method.DELETE, "path");</pre> | <pre>RequestSpecification spec = RestAssured.given();<br>spec.request("post", "path/{param}?", param);<br>spec.request(Method.DELETE, "path?");</pre> |
| <pre>RestAssured.request("post", "path/{param}", new Object());<br>RestAssured.request(Method.DELETE, "path/{param}", new Object());</pre> | <pre>RestAssured.request("post", "path?", new Object());<br>RestAssured.request(Method.DELETE, "path/{param}?", new Object());</pre> |

There is no restriction set for the request method in case of either String or Method type thus supporting all available ones.

**Template:**

Instantiated case:
```xml
<searchConfiguration name="RequestSpecification#request: Request URI cannot end with ?" text="$RequestSpecification$.request($Method$, $requestUri$, $parameters$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="RequestSpecification" nameOfExprType="io\.restassured\.specification\.RequestSpecification" expressionTypes="io.restassured.specification.RequestSpecification" within="" contains="" />
    <constraint name="parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="requestUri" script="&quot;requestUri.value?.endsWith(&quot;?&quot;)&quot;" target="true" within="" contains="" />
    <constraint name="Method" within="" contains="" />
</searchConfiguration>
```

Static method case:
```xml
<searchConfiguration name="RequestSpecification#request: Request URI cannot end with ?" text="io.restassured.RestAssured.$request$($Method$, $requestUri$, $parameters$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="parameters" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="requestUri" script="&quot;requestUri.value?.endsWith(&quot;?&quot;)&quot;" target="true" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="request" within="" contains="" />
</searchConfiguration>
```

## CloseIdleConnectionConfig: Idle time cannot be less than 0

This is based on the exception handling happening in [`io.restassured.config.ConnectionConfig.CloseIdleConnectionConfig`](https://github.com/rest-assured/rest-assured/blob/adea885dd97dd977a6e7b142b560577404b7811f/rest-assured/src/main/java/io/restassured/config/ConnectionConfig.java).

Since the configuration value represents a time amount it wouldn't really make sense to define a negative value, hence the validation. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>RestAssured.config().connectionConfig(new ConnectionConfig(new CloseIdleConnectionConfig(1000, MILLISECONDS)));</pre> | <pre>RestAssured.config().connectionConfig(new ConnectionConfig(new CloseIdleConnectionConfig(-500, MILLISECONDS)));</pre> |

**Script filter ($idleTime$):**

```groovy
try {
    idleTime?.text?.toLong() < 0
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="Idle time cannot be less than 0." text="new $CloseIdleConnectionConfig$($idleTime$, $TimeUnit$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="TimeUnit" within="" contains="" />
    <constraint name="idleTime" script="&quot;try {&#10;    idleTime?.text?.toLong() &lt; 0&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
    <constraint name="CloseIdleConnectionConfig" regexp="io\.restassured\.config\.ConnectionConfig\.CloseIdleConnectionConfig" within="" contains="" />
</searchConfiguration>
```

## ProxySpecification: Cannot determine proxy port

This is based on the validation logic in (indirectly) the constructor of [io.restassured.specification.ProxySpecification](https://github.com/rest-assured/rest-assured/blob/adea885dd97dd977a6e7b142b560577404b7811f/rest-assured/src/main/java/io/restassured/specification/ProxySpecification.java).

It is also called (indirectly) from [io.restassured.RestAssured#proxy(String, int, String)](https://github.com/rest-assured/rest-assured/blob/adea885dd97dd977a6e7b142b560577404b7811f/rest-assured/src/main/java/io/restassured/RestAssured.java),
so there are two inspections created for this validation, one is for calling `RestAssured.proxy()` the other one is calling the constructor of `ProxySpecification` directly.

| Compliant code | Non-compliant code |
|---|---|
| <pre></pre> | <pre>io.restassured.RestAssured.proxy("127.0.0.1", -10, "ftp")</pre> |
| <pre></pre> | <pre>new io.restassured.specification.ProxySpecification("127.0.0.1", -10, "ftp") {<br>}</pre> |

**Script filter (Complete Match)**

```groovy
try {
    port?.text?.toInteger() < 1 && !scheme?.value?.equalsIgnoreCase("HTTP") && !scheme?.value?.equalsIgnoreCase("HTTPS")
} catch (NumberFormatException e) {
    false
}
```

**Template:**

### RestAssured.proxy() 

```xml
<searchConfiguration name="Cannot determine proxy port. Port is less than 1 and scheme doesn't match http or https." text="io.restassured.RestAssured.$proxy$(&quot;$host$&quot;, $port$, &quot;$scheme$&quot;)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;try {&#10;    port?.text?.toInteger() &lt; 1 &amp;&amp; !scheme?.value?.equalsIgnoreCase(&quot;HTTP&quot;) &amp;&amp; !scheme?.value?.equalsIgnoreCase(&quot;HTTPS&quot;)&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" within="" contains="" />
    <constraint name="host" within="" contains="" />
    <constraint name="scheme" within="" contains="" />
    <constraint name="port" within="" contains="" />
    <constraint name="proxy" regexp="proxy" target="true" within="" contains="" />
</searchConfiguration>
```

### ProxySpecification

```xml
<searchConfiguration name="Cannot determine proxy port. Port is less than 1 and scheme doesn't match http or https." text="io.restassured.RestAssured.$proxy$(&quot;$host$&quot;, $port$, &quot;$scheme$&quot;)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;try {&#10;    port?.text?.toInteger() &lt; 1 &amp;&amp; !scheme?.value?.equalsIgnoreCase(&quot;HTTP&quot;) &amp;&amp; !scheme?.value?.equalsIgnoreCase(&quot;HTTPS&quot;)&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" within="" contains="" />
    <constraint name="host" within="" contains="" />
    <constraint name="scheme" within="" contains="" />
    <constraint name="port" within="" contains="" />
    <constraint name="proxy" regexp="proxy" target="true" within="" contains="" />
</searchConfiguration>
```

## ResponseBuilder: Status code must be greater than 100 and less than 600

This is based on the exception handling happening in [`io.restassured.builder.ResponseBuilder#build()`](https://github.com/rest-assured/rest-assured/blob/4d3b484d7160638c69e9b45b0a386ff612fe611c/rest-assured/src/main/java/io/restassured/builder/ResponseBuilder.java)

The reason behind this check is that official (and unofficial) HTTP status codes range from 100 to 599.

| Compliant code | Non-compliant code |
|---|---|
| <pre>new ResponseBuilder().setStatusCode(404).build();</pre> | <pre>new ResponseBuilder().setStatusCode(50).build();</pre> |

For the template it doesn't matter if the `setStatusCode(int)` is called on an inline instantiated `ResponseBuilder` object or an one created beforehand. 

**Script filter ($statusCode$):**

```groovy
try {
    statusCode?.text?.toInteger() < 100 || statusCode?.text?.toInteger() >= 600
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="Status code must be greater than 100 and less than 600" text="$ResponseBuilder$.setStatusCode($statusCode$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" within="" contains="" />
    <constraint name="ResponseBuilder" nameOfExprType="io\.restassured\.builder\.ResponseBuilder" expressionTypes="io.restassured.builder.ResponseBuilder" within="" contains="" />
    <constraint name="statusCode" script="&quot;try {&#10;    statusCode?.text?.toInteger() &lt; 100 || statusCode?.text?.toInteger() &gt;= 600&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
</searchConfiguration>
```
