# 仅使用 Jade(Pug)、Javascript、jQuery 和 CSS 创建一个旋转木马

> 原文：<https://medium.com/hackernoon/creating-a-carousel-using-only-jade-pug-javascript-jquery-and-css-b69576eca9a2>

步骤 1:在页面中创建 div，它将包含 carousel 元素

```
.gallery
 .gallery-main
   .gallery-main-image
    img(src=”/images/gallery3.jpg”)
   .gallery-filmstrip
    a(href=””)
     img(src=”/images/gallery1.jpg”)
    a(href=””)
     img(src=”/images/gallery2.jpg”)
    a(href=””)
     img(src=”/images/gallery3.jpg”)
    a(href=””)
     img(src=”/images/gallery4.jpg”)
    a(href=””)
     img(src=”/images/gallery5.jpg”)
```

步骤 2:为你的转盘中的所有元素设置样式:

显示的图像库:

*   所选图像显示周围的 div

```
/*set styles for the wrapper around the image that will be displayed on click*/.gallery { 
  background-color: black;
}.gallery > .gallery-main { 
  position: relative; 
  height: 400px; 
  overflow: hidden;
}
```

*   包含图像本身的 div 的 div

```
/*set styles for the inner container for the image that will be displayed on click*/.gallery > .gallery-main > .gallery-main-image { 
  position: absolute; 
  top: 0; bottom: 0; 
  right: 0; left: 0;
}
```

*   图像本身

```
/*set styles for the image itself*/.gallery > .gallery-main > .gallery-main-image > img { 
  display: block; 
  margin: 0 auto; 
  max-height: 100%; 
  max-width: 100%; 
  height: 400px;
}
```

包含要显示的图像缩略图的连续画面:

*   电影胶片的容器

```
/*set styles for the filmstrip container*/.gallery > .gallery-filmstrip { 
  position: relative; 
  height: 280px; 
  margin-top: 10px;
}
```

*   电影里的一切

```
/*set styles for everything inside the filmstrip container*/.gallery > .gallery-filmstrip > * { 
  position: absolute; 
  top: 0; 
  left: 0;
}
```

*   将幻灯片中的所有内容放在中间

```
/*position everything inside the filmstrip in the center*/
.gallery > .gallery-filmstrip > * { 
  position: absolute; 
  top: 0; 
  left: 0;
}
```

*   连续画面中图像的动画

```
/*set animation class to the filmstrip on click*/
.gallery > .gallery-filmstrip-animated > * { 
  transition: transform 1s, opacity 1s;
}
```

*   为容器中的所有图像设置高度和宽度

```
/*set a height and width for the gallery filmstrip images*/
.gallery > .gallery-filmstrip img { 
  height: 200px; 
  width: 200px;
}
```

第三步:使用 [jQuery](https://hackernoon.com/tagged/jquery) 和 [Javascript](https://hackernoon.com/tagged/javascript) 创建函数，这将允许你使用刚刚创建的样式和动画遍历你的图像。

*   初始化图库

```
const initializeGallery = function() {
 positionGalleryFilmstrip(0, false)$(‘.gallery-filmstrip > *’).on(‘click’, function(event) {
  event.preventDefault();
  const cell = $(this);
  const index = cell.index()
  console.log(‘CLICKED’, index)
  positionGalleryFilmstrip(index-2)
  })
 }initializeGallery()
```

*   确定将在电影胶片中可见的索引，并使用偏移量来确保无论我们在哪个索引上，结果都是 0，1，2，3，4。我们如何通过使用 for 循环和模运算来做到这一点

```
const visibleIndicies = function(totalNumberOfCells, numberOfVisibleCells, offset){
 let visible = []
 for (let index = 0; index < numberOfVisibleCells; index++) {
 visible[index] = (index+offset) % totalNumberOfCells
 }
 return visible
}
```

*   根据每个位置在胶片上的位置，确定其 Z 索引、不透明度和变换

```
const zIndexForPosition = function(position){
 return (
   (position === 0 || position === 4) ? 1 :
   (position === 1 || position === 3) ? 2 :
   3
  )
 }

const opacityForPosition = function(position){
 return (
  (position === 0 || position === 4) ? 0.4 :
  (position === 1 || position === 3) ? 0.7 :
  1
  )
 }

const transformForPosition = function(position){
 return ({
  0: ‘translateX(160px) translateY(31px) scale(.8)’,
  1: ‘translateX(300px) translateY(31px) scale(1)’,
  2: ‘translateX(500px) translateY(31px) scale(1.3)’,
  3: ‘translateX(700px) translateY(31px) scale(1)’,
  4: ‘translateX(860px) translateY(31px) scale(.8)’,
  })[position]
 }
```

*   根据图像(单元格)在胶片显示窗格(数组)中的位置确定其索引

```
const visibleIndicies = function(totalNumberOfCells, numberOfVisibleCells, offset) {
 let visible = []
 for (let index = 0; index < numberOfVisibleCells; index++) {
  visible[index] = (index+offset) % totalNumberOfCells
 }
 return visible
}
```

*   该函数最复杂的部分——定位图像，使其将 css 样式应用于包含 5 个图像元素的数组中显示的特定索引

```
const positionGalleryFilmstrip = function(offset, animate=true){
 const filmstrip = $(‘.gallery-filmstrip’)
 const numberOfCells = 5;
 const cells = filmstrip.children()
 if (offset < 0) offset += cells.length
 const visibleCells = visibleIndicies(cells.length, numberOfCells,  
 offset)
 if (animate){
  filmstrip.addClass(‘gallery-filmstrip-animated’)
 }else{
  filmstrip.removeClass(‘gallery-filmstrip-animated’)
 }
 cells.each(function(index){
  const cell = $(this)
  let position = visibleCells.indexOf(index)
  if (isBefore(index, visibleCells, cells.length)){
   cell.css({
    display: ‘block’,
    zIndex: ‘0’,
    opacity: ‘0’,
    transform: ‘translateX(100px) translateY(31px) scale(.6)’,
  })
 }else if (isAfter(index, visibleCells, cells.length)){
   cell.css({
    display: ‘block’,
    zIndex: ‘0’,
    opacity: ‘0’,
    transform: ‘translateX(890px) translateY(31px) scale(.6)’,
  })
 }else if (position === -1){
   cell.css({
    display: ‘none’,
    zIndex: ‘0’,
   opacity: ‘1’,
   transform: ‘’,
  })
 }else{
   cell.css({
    display: ‘block’,
    zIndex: zIndexForPosition(position),
    opacity: opacityForPosition(position),
    transform: transformForPosition(position),
  })
 }
 if (position === 2){
 const src = cell.find(‘> img’).attr(‘src’)
 const image = $(‘<img>’).attr(‘src’, src)
 const gallery = filmstrip.closest(‘.gallery’)
 const galleryMain = gallery.find(‘> .gallery-main’)
 const currentImageWrapper = galleryMain.find(‘.gallery-main-
 image:first’)
 const nextImageWrapper = currentImageWrapper.clone()
 const nextImage = nextImageWrapper.find(‘> img’)
  if (nextImage.attr(‘src’) === src) return;
  nextImage.attr(‘src’, src)
  galleryMain.prepend(nextImageWrapper)
  currentImageWrapper.fadeOut(function(){
  currentImageWrapper.remove()
  })
  }
  })
 }
```

哇哦。！这里发生了什么事？让我们打开它，一步一步地看一遍:

*   创建一个定位图库幻灯片的函数，并将其设置为 animate=true
*   分配一些变量，这些变量将包含连续画面元素、单元格数量以及连续画面的子元素

```
const positionGalleryFilmstrip = function(offset, animate=true){
 const filmstrip = $(‘.gallery-filmstrip’)
 const numberOfCells = 5;
 const cells = filmstrip.children()
}
```

*   如果偏移量小于零，那么我们要将单元格总数加到这个数量上。例如，如果我们的偏移量是-1，并且我们有一个长度为 39 的图像数组，那么我们的新偏移量将是 38。

```
if (offset < 0) offset += cells.length
 const visibleCells = visibleIndicies(cells.length, numberOfCells, offset)
```

*   如果上述条件为真，这意味着单元格可见，则将新的偏移量、长度和单元格数量传递给函数 visible index。然后我们将把这个函数的值存储到 visibleCells 中
*   如果 animate 为真，那么我们会将动画类添加到电影胶片中，否则不添加该类

```
if (animate){
 filmstrip.addClass(‘gallery-filmstrip-animated’)
 }else{
 filmstrip.removeClass(‘gallery-filmstrip-animated’)
 }
```

*   现在将下面的 css 样式应用于每个单元格，这将为每个单元格提供我们需要的不透明度、zIndex 和变换功能，具体取决于它在电影胶片中的位置

```
cells.each(function(index){
 const cell = $(this)
 let position = visibleCells.indexOf(index)
 if (isBefore(index, visibleCells, cells.length)){
  cell.css({
   display: ‘block’,
   zIndex: ‘0’,
   opacity: ‘0’,
   transform: ‘translateX(100px) translateY(31px) scale(.6)’,
 })
 } else if (isAfter(index, visibleCells, cells.length)){
  cell.css({
   display: ‘block’,
   zIndex: ‘0’,
   opacity: ‘0’,
   transform: ‘translateX(890px) translateY(31px) scale(.6)’,
 })
 }else if (position === -1){
   cell.css({
    display: ‘none’,
    zIndex: ‘0’,
    opacity: ‘1’,
    transform: ‘’,
  })
 }else{
   cell.css({
    display: ‘block’,
    zIndex: zIndexForPosition(position),
    opacity: opacityForPosition(position),
    transform: transformForPosition(position),
  })
 }
```

*   现在我们需要给图片分配属性，这样我们就可以淡入淡出了

```
if (position === 2){
 const src = cell.find(‘> img’).attr(‘src’)
 const image = $(‘<img>’).attr(‘src’, src)
 const gallery = filmstrip.closest(‘.gallery’)
 const galleryMain = gallery.find(‘> .gallery-main’)
 const currentImageWrapper = galleryMain.find(‘.gallery-main-image:first’)
 const nextImageWrapper = currentImageWrapper.clone()
 const nextImage = nextImageWrapper.find(‘> img’)
 if (nextImage.attr(‘src’) === src) return;
 nextImage.attr(‘src’, src)
 galleryMain.prepend(nextImageWrapper)
 currentImageWrapper.fadeOut(function(){
 currentImageWrapper.remove()
 })
 }
 })
 }
```

*   最后但同样重要的是，我们需要确定一个单元格是在单元格之前还是之后

```
const isBefore = function(index, visibleCells, cellsLength){
 let beforeFirstIndex = visibleCells[0]-1
 if (beforeFirstIndex < 0) beforeFirstIndex += cellsLength
 return index === beforeFirstIndex
 }const isAfter = function(index, visibleCells, cellsLength){
 let afterLastIndex = visibleCells[visibleCells.length-1]+1
 if (afterLastIndex > cellsLength) afterLastIndex -= cellsLength
 return index === afterLastIndex
 }
```