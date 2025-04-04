## Duration
`transition-property`
- Specifies the property or properties of an element that a transition effect should apply to.
`transition-duration`
- Specifies how long an elements transition should take to complete.
```
a {  
  transition-property: color;  
  transition-duration: 1s;  
}
```

## Timing Function
`transaction-timing-function`
- Specifies the speed of a transition effect over the course of its duration.
- The default value is `ease`, which starts the transition slowly, speeds up in the middle, and slows down again at the end.
- Other valid values include:
	- `ease-in` — starts slow, accelerates quickly, stops abruptly
	- `ease-out` — begins abruptly, slows down, and ends slowly
	- `ease-in-out` — starts slow, gets fast in the middle, and ends slowly
	- `linear` — constant speed throughout

## Delay
`transition-delay`
- Specifies how long an element should wait before beginning a transition.

## Shorthand
```
transition-property: color;
transition-duration: 1.5s;
transition-timing-function: linear;
transition-delay: 0.5s;
```
into 
```
transition: color 1.5s linear 0.5s;
```


## Combinations
```
transition: color 1s linear,
font-size 750ms ease-in 100ms;
```

## All
`all` 
- means every value that changes will be transitioned in the same way. You can use `all` with the separate transition properties, or the shorthand syntax. 
- This allows you to describe the transition of many properties with a single line:

```
transition: all 1.5s linear 0.5s;
```

In this example, any change will be animated over one and a half seconds after a half-second delay with linear timing.

