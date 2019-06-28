# Targeting static methods

There are a couple of ways one may target static methods. What one selects can be based on personal preferences or
based on the desired end result of the template. The results may vary based on how the template is put together,
what template variables and filters are used, and maybe some other factors as well.

Below you can find a (not comprehensive) list of a couple of examples and their configurations for targeting static methods and what specific code snippets they match or not match.

For this example I'm again using FluentLenium's `FluentLeniumAssertions` class.

### 1. Not referencing the caller class

**Template:**
```java
assertThat($element$).isDisplayed();
```

**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `assertThat(element).isDisplayed();`

It would also match instance method calls if an `assertThat()` method would be called from an object instance.
There might be cases where a class has an instance method with the same name as the targeted method. A good example of this is AssertJ's `SoftAssertions` class:

```java
FluentWebElement element;

SoftAssertions softly = new SoftAssertions();
softly.assertThat(element).isDisplayed();
```
> NOTE: `SoftAssertions` does not have assertions for `FluentWebElement`s. I used it just to demonstrate the example.

### 2. Full reference of the caller class

**Template:**
```java
org.fluentlenium.assertj.FluentLeniumAssertions.assertThat($element$).isDisplayed();
```

**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `assertThat(element).isDisplayed();`

### 3. Caller class is mandatory (without Text filter)

**Template:**
```java
$Assertions$.assertThat($element$).isDisplayed();
```

**Configuration:**
    
    $Assertions$:
        Count filter: default (1-1)

**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`

It would also match instance method calls if an `assertThat()` method would be called from an object instance.

**Doesn't match:**
- `assertThat(element).isDisplayed();`

### 4. Caller class is mandatory (with Text filter)

**Template:**
```java
$Assertions$.assertThat($element$).isDisplayed();
```

**Configuration:**

    $Assertions$:
        Text filter: org\.fluentlenium\.assertj\.FluentLeniumAssertions
        Count filter: default (1-1)

**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`

**Doesn't match:**
- `assertThat(element).isDisplayed();`

### 5. Caller class is optional

**Template:**
```java
$Assertions$.assertThat($element$).isDisplayed();
```

**Configuration:**

    $Assertions$:
        Text filter: org\.fluentlenium\.assertj\.FluentLeniumAssertions
        Count filter: 0-1
    
**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `assertThat(element).isDisplayed();`

### 6. Caller class is not fully referenced

Of course here we can play with the Text filter to match whatever we want it to, so this is just a specific case
that people might use more often.

**Template:**
```java
$Assertions$.assertThat($element$).isDisplayed();
```

**Configuration:**

    $Assertions$:
        Text filter: FluentLeniumAssertions
        Count filter: 0-1
    
**Matches:**
- `org.fluentlenium.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `FluentLeniumAssertions.assertThat(element).isDisplayed();`
- `assertThat(element).isDisplayed();`

Due to the nature of Text filters, this template also matches the same assertion calls from a different class named the same.
So in this case if I would have a class `picimako.assertj.FluentLeniumAssertions` the template above did match the last two examples
from the **Matches** list above and of course `picimako.assertj.FluentLeniumAssertions.assertThat(element).isDisplayed();` as well.

## Final words

This is not at all a comprehensive list of examples. For example there may be inspections where the static method name too is handled
as a template variable for more flexible configuration.

If you stumble upon any other template variation I missed, please let me know by creating a [GitHub issue](https://github.com/picimako/intellij-inspections/issues).
