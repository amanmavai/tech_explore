# rem vs em
> **rem**: relative to the document's root element **html**     
> **em**:  relative to the **immediate parent** of the targeted element.

# specificity
1. A universal selector (*) has no specificity and gets = **0** points.
```css
* {
  color: red;
}
```

2. An element (type) or pseudo-element selector gets = **1** point of specificity.
```css
div {
  color: red;
}

::selection {
  color: red;
}
```

3. A class, pseudo-class or attribute selector gets = **10** points of specificity.
```css
.my-class {
  color: red;
}

:hover {
  color: red;
}

[href='#'] {
  color: red;
}
```

4. An ID selector gets = **100** points of specificity.
```css
#myID {
  color: red;
}
```

5. CSS applied directly to the style attribute of the HTML element, gets a = specificity score of **1000** points.
```html
<div style="color: red"></div>
```

6. Lastly, an !important at the end of a CSS value gets = a specificity score of **10,000** points.
```css
.my-class {
  color: red !important; /* 10,000 points */
  background: white; /* 10 points */
}
```

> # box-sizing
The default box sizing is content-box, which means padding and borders are added to the overall box.
```css
.my-box {
  width: 200px;
  border: 10px solid;
  padding: 20px;
}

// How wide do you think .my-box will be?
// 260px (200px width + 20px border + 40px padding)

```

This alternative box model tells CSS to apply the width to the border box instead of the content box.
This means that our border and padding get pushed in, and as a result, when you set .my-box to be 200px wide: it actually renders at 200px wide.
```css
.my-box {
    box-sizing: border-box;
    width: 200px;
    border: 10px solid;
    padding: 20px;
}

// generally used in resets
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

> # pseudo class vs pseudo element

```css
// pseudo classes
p:first-child {
  font-size: 120%;
  font-weight: bold;
}
a:link,
a:visited {
  color: rebeccapurple;
  font-weight: bold;
}

a:hover {
  color:hotpink;
}

// other examples
:last-child
:only-child
:invalid

// pseudo elements
// Pseudo-elements behave in a similar way. However, they act as if you had added
// a whole new HTML element into the markup, rather than applying a class to existing elements.

// Pseudo-elements start with a double colon ::. ::before is an example of a pseudo-element.

article p::first-line {
  font-size: 120%;
  font-weight: bold;
}

.box::before {
  content: "This should show before the other content. ";
}

```
