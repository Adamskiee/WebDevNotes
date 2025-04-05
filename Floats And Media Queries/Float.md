The float property in CSS is used to **place an element to the left or right side of its container**, allowing other elements to wrap around it. It's commonly used for **wrapping text around images or creating simple layouts**.

The float property can take the following values:

- `left` – causes the element to float to the left of its containing block, allowing text and inline elements to wrap around its right side.
- `right` – causes the element to float to the right of its containing block, with text and inline elements wrapping around its left side.
- `none` – the default value. It indicates that the element should not float. The element will be displayed in the normal flow of the document.
- `inherit` – specifies that the element should inherit the float value from its parent element.
- `inline-start `– floats the element towards the start of the inline direction. For example, in left-to-right languages (like English), inline-start would float the element to the left, similar to float: left;. In right-to-left languages (like Arabic), it would float the element to the right.
- `inline-end` – floats the element towards the end of the inline direction. In left-to-right languages, it would be equivalent to float: right;, floating the element to the right. In right-to-left languages, it would float the element to the left.

![[Pasted image 20250405101909.png]]

The `clear` property helps reset the floating behavior. By using `clear`, you can control how and where elements are positioned in relation to floated items, allowing for more complex and organized layouts.

The `clear` property can take the following values:
- **none** – the default value. It allows the element to be next to floated elements.
- **left** – the element is moved down to clear past elements floated to the left.
- **right** – the element is moved down to clear past elements floated to the right.
- **both** – the element is moved down to clear past elements floated on both sides.

Clearfix works by **adding a pseudo-element, such as ::after, to the parent container**. This pseudo-element is styled in a way that it clears the float, thus making the container recognize the height of its floated children.

Take a look at the following CSS example:

```
.container::after {
    content: "";
    display: table;
    clear: both;
}
```

In our code:
- we use the `::after` pseudo-element, which is a special type of selector in CSS, that allows us to insert content after the content of an element. In the case of clearfix, we use it to insert an invisible "element" at the end of the container.
- the `content: "";` rule, which is essential for the pseudo-element to work, generates content, but we keep it empty.
- the `display: table;` rule changes the display type, which helps in the clearing process.
- the `clear: both;` rule clears the float from both the left and right sides. This action forces the container to expand and encompass the floated elements, thus solving the collapsing issue.

The "clearfix" resolves our parent container collapse issue so that the container now properly encases its floated children.

