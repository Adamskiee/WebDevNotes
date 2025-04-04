`grid-template-areas`
- Defines a grid template by referencing the names of the grid areas.
- grid-template-areas: "header header"
                       "nav nav"
                       "left right"
                       "footer footer";
![[Pasted image 20250402113513.png]]
## Justify Items
`justifiy-items`
- a property that positions grid items along the inline, or row, axis. This means that it positions items from left to right across the web page
- it accept this values:
	- `start` — aligns grid items to the left side of the grid area
	- `end` — aligns grid items to the right side of the grid area
	- `center` — aligns grid items to the center of the grid area
	- `stretch` — stretches all items to fill the grid area
before:
![[Pasted image 20250403181017.png]]
after inputting `justify-items:center`
![[Pasted image 20250403181110.png]]
## Justify Content
`justify-content`
- We can use `justify-content` to position the entire grid along the row axis.
- it accepts these values:
	- `start` — aligns the grid to the left side of the grid container
	- ``end` — aligns the grid to the right side of the grid container
	- `center` — centers the grid horizontally in the grid container
	- `stretch` — stretches the grid items to increase the size of the grid to expand horizontally across the container
	- `space-around` — includes an equal amount of space on each side of a grid element, resulting in double the amount of space between elements as there is before the first and after the last element
	- `space-between` — includes an equal amount of space between grid items and no space at either end
	- `space-evenly` — places an even amount of space between grid items and at either end`
## Align Items
`align-items`
- is a property that positions grid items along the block, or column axis. This means that it positions items from top to bottom.
- it accepts these values:
	- `start` — aligns grid items to the top side of the grid area
	- `end` — aligns grid items to the bottom side of the grid area
	- `center` — aligns grid items to the center of the grid area
	- `stretch` — stretches all items to fill the grid area
## Align Content
`align-content`
- positions the rows along the column axis, or from top to bottom, and is declared on grid containers.
- It accepts these positional values:
	- `start` — aligns the grid to the top of the grid container
	- `end` — aligns the grid to the bottom of the grid container
	- `center` — centers the grid vertically in the grid container
	- `stretch` — stretches the grid items to increase the size of the grid to expand vertically across the container
	- `space-around` — includes an equal amount of space on each side of a grid element, resulting in double the amount of space between elements as there is before the first and after the last element
	- `space-between` — includes an equal amount of space between grid items and no space at either end
	- `space-evenly` — places an even amount of space between grid items and at either end
## Justify Self and Align Self
`justify-self` 
- specifies how an individual element should position itself with respect to the row axis. This property will override `justify-items` for any item on which it is declared.
`align-self` 
- specifies how an individual element should position itself with respect to the column axis. This property will override `align-items` for any item on which it is declared.
They both accept these four properties: 
- `start` — positions grid items on the left side/top of the grid area
- `end` — positions grid items on the right side/bottom of the grid area
- `center` — positions grid items on the center of the grid area
- `stretch` — positions grid items to fill the grid area (default)

### Grid Auto Rows and Grid Auto Columns
`grid-auto-rows` 
- specifies the height of implicitly added grid rows. 
`grid-auto-columns`
- specifies the width of implicitly added grid columns.

`grid-auto-rows` and `grid-auto-columns`
- accept the same values as their explicit counterparts, `grid-template-rows` and `grid-template-columns`:
	- pixels (`px`)
	- percentages (`%`)
	- fractions (`fr`)
	- the `repeat()` function

## Grid Auto Flow
`grid-auto-flow` 
- specifies whether new elements should be added to rows or columns, and is declared on grid containers.
	-  it accepts these values:
	- `row` — specifies the new elements should fill rows from left to right and create new rows when there are too many elements (default)
	- `column` — specifies the new elements should fill columns from top to bottom and create new columns when there are too many elements
	- `dense` — this keyword invokes an algorithm that attempts to fill holes earlier in the grid layout if smaller elements are added

You can pair `row` or `column` with `dense`, like this: `grid-auto-flow: row dense;`.

[[]]