`Media queries `are a CSS technique used in responsive web design. They allow you to apply CSS rules based on certain conditions, like the width of the browser window. This means you can create different styles for different screen sizes, making your website look good on both desktops and mobile devices.

Here's an example of a basic media query:
```
@media screen and (max-width: 600px) {
    /* CSS rules for screens smaller than or equal to 600px */
}
```
1. **@media** – the key term that starts a media query. It tells the browser to apply the following CSS rules only under certain conditions (which you'll need to define next).
2. **screen** – the media type. It specifies the type of device you're targeting. While screen is the most common media type for web designs, targeting computer screens, tablets, and smartphones, there are other types too, like print (for printers) and all (for all media types).
3. **and** – a logical operator used to combine multiple media features. In this case, it's combining the media type screen with a feature query (max-width: 600px).
4. **(max-width: 600px)** – the media feature and value. It specifies the condition that needs to be met for the CSS rules to apply. Here, max-width: 600px means that the enclosed CSS rules will only be applied to screens that are 600 pixels wide or narrower. In general, **max-width** is a feature that refers to the maximum width of the display area, such as a browser window.
5. **Enclosed CSS Rules** – inside the curly braces { ... }, you place your CSS rules. These rules will be applied only when the media query condition is true (in this case, when the screen width is less than or equal to 600 pixels).
6. 
Media queries are used in responsive design to create a single website that looks good on all devices. For example, you might have a three-column layout on a desktop (wider screens) and want it to change to a single-column layout on mobile phones (narrower screens). Media queries enable you to write CSS that adapts to these varying screen widths.

## Single Column Layout on Mobile

``` html
<div class="container">
    <div class="column">Column 1</div>
    <div class="column">Column 2</div>
</div>
```

``` css
.column {
    float: left;
    width: 50%;
}

@media screen and (max-width: 600px) {
    .column {
        width: 100%;
        /*float: none;*/
    }
}
```
Result:
![[Pasted image 20250405103520.png]]
- **Basic Two-Column Layout** – each .column takes up 50% of the .container's width, creating a side-by-side layout.
- **Media Query for Mobile** – the @media rule targets screens smaller than or equal to 600px.
- **Changing to Single Column** – inside the media query, .column's width is changed to 100%, and float is set to none. This stacks the columns vertically on smaller screens.
## Adjusting Column Layout for Tablet and Mobile
``` html
<div class="container">
    <div class="column">Column 1</div>
    <div class="column">Column 2</div>
    <div class="column">Column 3</div>
</div>
```

``` css
.column {
    float: left;
    width: 33.33%;
}

@media screen and (max-width: 768px) {
    .column {
        width: 50%;
    }
}

@media screen and (max-width: 480px) {
    .column {
        width: 100%;
        float: none;
    }
}
```

![[Pasted image 20250405103845.png]]
- **Three-Column Layout** – by default, each column takes up one-third of the width.
- **Tablet Adjustment** – the first media query targets screens up to 768px wide. Columns change to 50% width, resulting in a two-column layout.
- **Mobile Adjustment** – the second media query targets screens up to 480px wide. As a result, the columns stack vertically.

