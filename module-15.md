# Flexbox

Flexbox is a css feature that allows us to layout elements, it excels at vertical centering and equal heights, it is very easy to reorder boxes as well. When you display an element as flex it becomes a flex container and the elements inside become flex items with that you can set properties to interact with the flexbox model. As a flexbox container you can set the direction, you can wrap the content, you can distribute the space, you can align the items and the content. As flexbox item you can change the order, you can expand the elements, you can set how the box can grow


```css
.my-flex-container {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  justify-content: flex-start;
  align-items: center;
}

.my-flex-items {
  order: 1;
  flex-basis: 20%;
  flex-grow: 2;
  flex-shrink: 2;
}
```

# Responsiveness

Responsive web design makes your web page look good on all devices. Responsive web design uses only HTML and CSS.

```html
<picture>
    <source src="balloons.jpg" media="(min-width: 800)">
    <img src="balloons-portrait.jpg">
</picture>

<img 
    src ="small.jpg"
    srcset="large.jpg 1024w, medium.jpg 640w small.jpg 320w"
    sizes="(min-width: 36m) 33.3vw, 100vw"
>
```

Responsiveness with images can be handled with the picture and source tags it optimizes the loading times of our site, but HTML provides us with the srcset attribute. The srcset attribute specifies the URL of the image to use in different situations and optimizes our site as well.

# CSS Grid

Grid system is built into CSS specification, grid is designed to work in 2 dimension as oppose to flexbox. With grid you need to define a wrapper and then you can start working with the columns

```css
.wrapper {
    width: 600px;
    height: 600px;
    display: grid;
    gap: 30px 20px;
    grid-template-columns: 124px 376px 42px;
    grid-template-rows: 170px 210px 61px 61px;
}

.grid-item-1 {
    grid-row: 1/2;
    grid-column: 1/2;
}
```

CSS provides us with features that allows us to make dynamic layouts on our site, pages and understanding them is key when making a web site web app responsive.
