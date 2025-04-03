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
- 