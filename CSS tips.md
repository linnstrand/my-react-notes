# CSS Tips

## Cascade Layers

- Specificity: which styling takes preference on elements.

### @layer

-sorting css in specification. Last layer take priority.

- un-layered code takes priority.
- Define order of layers at the top of the file in priority order, starting with lowest priority,

## Animation

### Transform

Add multiple properties like rotate, scale, translate.
For hover, add transition on element, add transform on hover.

```scss
.link {
    transition: transform 0.2s ease,
    &:hover {
        Transform: translate: translate(20px, 20px),
        rotate(5deg),
        scale(1.1)
}}
```

For separate properties transforms: translate on hover, and separate with comma.

```scss
.link {
  transition: scale 0.2s ease, rotate 0.2s ease 0.2s;
  rotate: y 15deg;

  :hover {
    scale: 1.2;
    rotate: 5deg;
  }
}
```

Inset: shorthand for top/right, bottom, left.

ch is the width or "0" and useful for text width elements.

Flex:
Flex basis: for setting the initial main size of a flex item. Auto is default. The final size is based on available space, flex-grow and flex-shrink.
Flex-basis overrides width for row, but max-width overrides flex-basis.

Flex Grow calculation:(parent container / total number of flex-grow values) \* each flex-grow value.

Flex-Shrink: the rate of shrinking compared to siblings.

Justify Content for main axis.
Align Items for cross axis.
Align Self is for individual items.

Align Content is for multiple lines.

INput: overflows by default. wrap with div and put width 100% to make it be nice to
