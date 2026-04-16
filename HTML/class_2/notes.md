# HTML Tags Covered in Today's Class

## `<abbr>` - Abbreviation

Used to show the short form of a word.

```html
<abbr title="Hyper Text Markup Language">HTML</abbr>
```
Hovering the mouse shows the full form.

## `<cite>` - Citation

Used for the title of a book, movie, article, or website.

```html
<cite>Harry Potter</cite>
```

Usually displayed in italic text.

## `<dfn>` - Definition Term

Used when defining a term.

```html
<dfn>HTML</dfn> is a markup language.
```
## `<address>` - Contact Information

Used for author or company contact details.

```html
<address>
Karachi, Pakistan <br>
info@example.com
</address>
```

## `<ins>` - Inserted Text

Used for newly added text.

```html
<p>Price <ins>500</ins></p>
```

Usually underlined.

## `<del>` - Deleted Text

Used for removed text.

```html
<p>Price <del>1000</del></p>
```

Shows strike-through text.

## `<ol>` - Ordered List

Used for numbered lists.

```html
<ol>
  <li>Tea</li>
  <li>Coffee</li>
</ol>
```

## `<ol type="">`

Used to change numbering style.

```html
<ol type="A">
  <li>HTML</li>
  <li>CSS</li>
</ol>
```

Types:
```html
1 → 1,2,3
A → A,B,C
a → a,b,c
I → I,II,III
i → i,ii,iii
```

## `<li>` - List Item

Represents one item in a list.

```html
<li>Apple</li>
```

## `<ul>` - Unordered List

Used for bullet lists.

```html
<ul>
  <li>Apple</li>
  <li>Mango</li>
</ul>
```

## `<ul style="list-style-type:;">`

Used to change bullet style.

```html
<ul style="list-style-type:square;">
  <li>Apple</li>
  <li>Mango</li>
</ul>
```
Types:
```html
disc
circle
square
none
```

##  `<dl>` - Description List

Used for terms and descriptions.

```html
<dl>
  <dt>HTML</dt>
  <dd>Markup Language</dd>
</dl>
```

## `<dt>` - Description Term

Represents the title or term.

```html
<dt>HTML</dt>
```

## `<dd>` - Description Data

Represents the description.

```html
<dd>Used for webpage structure</dd>
```

Links

## `<a>` - Anchor Tag

Used to create links.

```html
<a href="https://google.com">Google</a>
```

## `<a>` with Unordered List

Used for navigation menus.

```html
<ul>
  <li><a href="#">Home</a></li>
  <li><a href="#">About</a></li>
</ul>
```

## mailto

Used to open email client.

```html
<a href="mailto:test@gmail.com">Email Us</a>
```

## target

Used to control where the link opens.

```html
<a href="https://google.com" target="_blank">Google</a>
```

Types:
```html
_blank → Opens in new tab
_self → Opens in same tab
```

## tel

Used to make phone calls.

```html
<a href="tel:+923001234567">Call Now</a>
```

Images

## `<img>`

Used to display images.

```html
<img src="pic.jpg">
```

## height and width

Used to set image size.

```html
<img src="pic.jpg" height="200" width="300">
```
## align

Used to align image (old method).

```html
<img src="pic.jpg" align="right">
```

Types:
left
right
top
middle

Note: align is outdated. Use CSS instead.