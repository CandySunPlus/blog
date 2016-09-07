title: CSS 揭秘 摘抄
date: 2016-09-07 12:30:09
tags: 
	- CSS 
	- CSS3 
	- 前端
---

### 透明边框
需要配合使用 `background-clip` ，描述如下：

|value|description|
|----|----|
|border-box|The background extends to the outside edge of the border (but underneath the border in z-ordering).|
|padding-box|No background is drawn below the border (background extends to the outside edge of the padding).|
|content-box|The background is painted within (clipped to) the content box.|
|text|The background is painted within (clipped to) the foreground text.|

<!-- more -->
### 背景适应盒子内距
需要配合 `background-origin`， 描述如下：

|value|description|
|----|----|
|border-box|The background extends to the outside edge of the border (but underneath the border in z-ordering).|
|padding-box|No background is drawn below the border (background extends to the outside edge of the padding).|
|content-box|The background is painted within (clipped to) the content box.|

### 蚂蚁行军线
结合 `border` `background` `background-position` 能实现出 `蚂蚁行军线` 的效果

```css
@keyframes ants {
	to {
		background-position: 100%;
	}
}
div {
	padding: 1em;
	border: 1px solid transparent;
	background: linear-gradient(white, white) padding-box, repeating-linear-gradient(-45deg, black 0, black 25%, white 0, white 50%) 0 / .6em .6em;
	animation: ants 12s linear infinite;
	max-width: 20em;
	font: 100%/1.6 Baskerville, Palatino, serif;
}
```

### 元素裁剪

使用 `clip-path`, 描述如下：

|value|description|
|----|----|
|clip-source|Represents a URL referencing a clip path element.|
|basic-shape|A basic shape function as defined in the CSS Shapes specification. A basic shape makes use of the specified reference box to size and position the basic shape. If no reference box is specified, the border-box will be used as reference box.|
|geometry-box|If specified in combination with a <basic-shape>, it provides the reference box for the basic shape. If specified by itself, it uses the edges of the specified box including any corner shaping (e.g. defined by border-radius) as clipping path.|
|fill-box|Uses the object bounding box as reference box.|
|stroke-box|Uses the stroke bounding box as reference box.|
|view-box|Uses the nearest SVG viewport as reference box. If a viewBox attribute is specified for the element creating the SVG viewport, the reference box is positioned at the origin of the coordinate system established by the viewBox attribute and the dimension of the reference box is set to the width and height values of the viewBox attribute.|
|none|There is no clipping path created.|

