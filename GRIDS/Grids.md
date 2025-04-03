## Grid Template
``grid-template-columns``
- based on width
- Ex. width: ``400px;`` ``grid-template-columns: 100px 50% 200px;``
![[Screenshot 2025-03-28 153038.png]]
``grid-template-rows``
- based on height
- Ex. width: ``500px;`` ``grid-template-rows: 40% 50% 50px;``
![[Pasted image 20250328153512.png]]
``grid-template``
- combine `grid-template-rows` and `grid-template-columns`
- `grid-template: grid-template-rows / grid-template-columns`
### Fraction
- `3fr` = 3/4
- `1fr` = 1/4
### Repeat function
- `repeat(3, 1fr)` = `1fr, 1fr, 1fr`
- ex. `grid-template-columns: repeat(2, 20px 50px)`
	- This code will create four columns where the first and third columns will be 20 pixels wide and the second and fourth will be 50 pixels wide.

`min-max()`

### Gap
`column-gap`
-  puts blank space between the columns of the grid
`row-gap`
- puts blank space between the rows of the grid
`gap`
- combination of `row-gap` and `column-gap`
- `gap: row-gap / column-gap`
![[Pasted image 20250402111558.png]]

### Multiple Row Items
`grid-row-start`
- A property that specifies the start of a content span over a set of rows within a grid framework.
`grid-row-end`
- Specifies the end of a content span over a set of rows within a grid framework.
![[Pasted image 20250402102447.png]]
`grid-row`
- grid-row: *start* / *end*

`grid-column-start`
- A property that specifies the start of a content span over a set of columns within a grid framework.
`grid-column-end`
- Specifies the end of a content span over a set of columns within a grid framework.
`grid-column`
- grid-column: *start* / *end*
Ex. grid-column: 2 / 5 == grid-column: 2 / span 3
`grid-area`
- grid-area: `grid-row-start / grid-column-start`/ `grid-row-end` / `grid-column-end`

[[Advanced Grid]]