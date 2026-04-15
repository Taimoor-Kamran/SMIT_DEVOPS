# HTML Class Notes

## Introduction to HTML
HTML stands for **HyperText Markup Language**. It is used to create the structure of web pages. HTML uses **tags** to define elements like headings, paragraphs, images, links, tables, forms, etc.

## Basic HTML Structure

```html
<html>
<head>
    <title>My First Page</title>
</head>
<body>
    Content goes here
</body>
</html>
```

### <html>
- Root element of an HTML page.
- All HTML code is written inside this tag.
```html
<html> ... </html>
```
<head>
- Contains metadata about the webpage.
- Includes title, styles, links, scripts, etc.
- Not shown directly on page.

```html
<head>
   <title>Website Title</title>
</head>
```

### <title>
- Sets the title of the webpage.
- Appears in browser tab.

```html
<title>My Website</title>
```

### <body>

- Contains all visible content of webpage.

```html
<body>
   <h1>Hello World</h1>
</body>
```

## Heading Tags

HTML provides 6 heading tags.

### <h1> to <h6>
<h1> = Largest heading
<h6> = Smallest heading

```html
<h1>Main Heading</h1>
<h2>Sub Heading</h2>
<h3>Section Heading</h3>
<h4>Small Heading</h4>
<h5>Smaller Heading</h5>
<h6>Smallest Heading</h6>
```

## Paragraph Tag
```html
<p>
```

Used to write paragraphs.

<p>This is a paragraph.</p>

## Language Attribute

```html
lang
```

Defines language of content.

<p lang="en-us">Paragraph in English</p>
<p lang="ur">یہ اردو میں پیراگراف ہے</p>
- Helps search engines and screen readers.

## Text Formatting Tags

```html
<sup>
```

Superscript text (upper text).

```html
x<sup>2</sup>
```

Output: x²

```html
<sub>
```

Subscript text (lower text).

```html
H<sub>2</sub>O
```

Output: H₂O

## White Space in HTML

Extra spaces are ignored by browser.

```html
<p>Hello     World</p>
```

Output: Hello World

To break line or spacing use tags like <br>.

```html
<br>
```

Line break tag.

```html
Hello <br> World
```

Output:
Hello
World

```html
<hr>
```

Horizontal line.

```html
<hr>
```

Used to separate sections.

## Bold / Italic / Important Tags

<strong>

Important text (usually bold).

```html
<strong>Warning!</strong>
```

```html
<b>
```

Bold text only.

```html
<b>Bold Text</b>
```
```html
<i>
```

Italic text only.

```html
<i>Italic Text</i>
```

```html
<em>
```

Emphasized text (usually italic).

```html
<em>Important Note</em>
```

## Quotation Tags

<blockquote>

Used for long quotations.

```html
<blockquote>
Learning never stops.
</blockquote>
```

<q>

Used for short inline quotation.

```html
<p>He said <q>Hello</q></p>
```

Output: He said “Hello”

## Short Forms / Definitions

<abbr>

Abbreviation tag.

```html
<abbr title="HyperText Markup Language">HTML</abbr>
```

Hover on HTML to see full form.

```html
<cite>
```

Used for title of book, movie, article, etc.

```html
<cite>Harry Potter</cite>
```

```html
<dfn>
```

Used when defining a term first time.

```html
<dfn>HTML</dfn> is a markup language.
```

## Contact Information

<address>

Used for contact details.

```html
<address>
John Doe <br>
Karachi, Pakistan <br>
info@email.com
</address>
```

## Difference Between Similar Tags
| Tag | Purpose |
| <strong> |	Important text |
| <b> |	Bold only |
| <em> |	Emphasized text |
| <i> |	Italic only |
| <blockquote> |	Long quote |
| <q> |	Short quote |
| <sup> |	Upper text |
| <sub> |	Lower text |
Example Full Page
<html>
<head>
   <title>HTML Practice</title>
</head>
<body>

<h1>Welcome</h1>

<p lang="en-us">This is English paragraph.</p>

<p>Water Formula = H<sub>2</sub>O</p>
<p>Math = x<sup>2</sup></p>

<strong>Important</strong><br>
<b>Bold</b><br>
<i>Italic</i><br>
<em>Emphasis</em>

<hr>

<blockquote>Success comes with practice.</blockquote>
