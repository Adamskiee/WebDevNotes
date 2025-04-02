``diplay: flex``
![[Pasted image 20250328123303.png]]
``diplay: inline-flex``
![[Pasted image 20250328123411.png]]
### Justify-content
- specify how flex items spread out from left to right, along the _main axis_
``justify-content: flex-start``
![[Pasted image 20250328123551.png]]
``justify-content: flex-end``
![[Pasted image 20250328123621.png]]
``justify-content: center``
![[Pasted image 20250328123721.png]]
``justify-content: space-around``
![[Pasted image 20250328123804.png]]
``justify-content: space-between``
![[Pasted image 20250328123854.png]]
### Align-items
- makes it possible to space flex items vertically.
``align-items: flex-start;``
![[Pasted image 20250328124057.png]]
``align-items: flex-end;``
![[Pasted image 20250328124233.png]]
``align-items: center;``
![[Pasted image 20250328133314.png]]
``align-items: baseline;``
![[Pasted image 20250328133331.png]]
### Flex property
``flex-grow: ``
-  specify if items should grow to fill a container and also which items should grow proportionally more or less than others.
![[Pasted image 20250328124933.png]]
``flex-shrink:``
- can be used to specify which elements will shrink and in what proportions.
- when zooming the browser
![[Pasted image 20250328134112.png]]

``flex-basis``
- used to specify the initial size of an element styled with `flex-grow` and/or `flex-shrink`.
``flex:``
- used to specify `flex-grow`, `flex-shrink`, and `flex-basis` in one declaration.
### flex-wrap
- A property that specifies if elements will occupy multiple lines and the direction in which the items are distributed.
``flex-wrap: wrap``
![[Pasted image 20250328140541.png]]
``flex-wrap: no-wrap``
![[Pasted image 20250328140616.png]]
``flex-wrap: wrap-reverse``
![[Pasted image 20250328140624.png]]

### Align-content
- for aligning elements within a single row. If a flex container has multiple rows of content, we can use `align-content` to space the rows from top to bottom.
``align-content: flex-start``
![[Pasted image 20250328141749.png]]
``align-content: flex-end``
![[Pasted image 20250328141801.png]]
``align-content: center``
![[Pasted image 20250328141838.png]]
``align-content: space-between``
![[Pasted image 20250328141813.png]]
``align-content: space-around``
![[Pasted image 20250328141855.png]]

### Flex-direction
- A property that specifies the direction in which elements are distributed within a container.
``flex-direction:row``
``flex-direction:row-reverse``
``flex-direction:column``
``flex-direction:column-reverse``

### Flex-flow
- A shorthand for specifying the flex-direction and flex-wrap properties. Defining flex-flow provides the parameters for child elements to be organized across the horizontal and vertical axes.

	