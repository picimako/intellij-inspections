# Image-comparison

Image comparison on GitHub: https://github.com/romankh3/image-comparison

## Set ImageComparison pixel tolerance level will have no effect

In the setter of `ImageComparison.setPixelToleranceLevel(double)` there is a condition that the value should be 0.0 <= level < 1, otherwise nothing is set.

| Compliant code | Non-compliant code |
|---|---|
| <pre></pre> | <pre>new ImageComparison(expected, actual).setPixelToleranceLevel(0.0)</pre> |
| <pre></pre> | <pre>new ImageComparison(expected, actual).setPixelToleranceLevel(1)</pre> |

**Script filter ($pixelTolerance$)**

```groovy
try {
    def pixelTol = pixelTolerance?.text?.toDouble()
    return pixelTol < 0.0 || pixelTol >= 1.0
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="pixelToleranceLevel should be 0.0 &lt;= level &lt; 1, otherwise nothing is set." text="$ImageComparison$.setPixelToleranceLevel($pixelTolerance$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="ImageComparison" nameOfExprType="com\.github\.romankh3\.image\.comparison\.ImageComparison" expressionTypes="com.github.romankh3.image.comparison.ImageComparison" within="" contains="" />
  <constraint name="pixelTolerance" script="&quot;try {&#10;    def pixelTol = pixelTolerance?.text?.toDouble()&#10;    return pixelTol &lt; 0.0 || pixelTol &gt;= 1.0&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

## ImageComparison instantiation may be simplified

These are only when being created inline with the instantiation, otherwise it can't really be made sure that the inspection will show correct results.

| Compliant code | Non-compliant code |
|---|---|
| <pre>ImageComparison(expectedImage, actualImage)</pre> | <pre>ImageComparison(expectedImage, actualImage, null)</pre> |

**Template:**

```xml
<replaceConfiguration name="Instantiation can be simplified. Apply quick fix to use a simpler constructor." text="new com.github.romankh3.image.comparison.ImageComparison($bufferedImage1$, $bufferedImage2$, null)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="new com.github.romankh3.image.comparison.ImageComparison($bufferedImage1$, $bufferedImage2$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="bufferedImage1" within="" contains="" />
    <constraint name="bufferedImage2" within="" contains="" />
</replaceConfiguration>
```
