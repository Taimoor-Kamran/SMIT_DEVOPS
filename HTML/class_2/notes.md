# HTML Tags Covered in Today's Class

## `<abbr>` - Abbreviation

Used to show the short form of a word.

```html
<abbr title="Hyper Text Markup Language">HTML</abbr>
```
Hovering the mouse shows the full form.

## `<cite>` - Citation

Used for the title of a book, movie, article, or website.

<cite>Harry Potter</cite>

Usually displayed in italic text.

3. <dfn> - Definition Term

Used when defining a term.

<dfn>HTML</dfn> is a markup language.
4. <address> - Contact Information

Used for author or company contact details.

<address>
Karachi, Pakistan <br>
info@example.com
</address>
5. <ins> - Inserted Text

Used for newly added text.

<p>Price <ins>500</ins></p>

Usually underlined.

6. <del> - Deleted Text

Used for removed text.

<p>Price <del>1000</del></p>

Shows strike-through text.

7. <s> - No Longer Correct Text

Used for outdated or incorrect text.

<p><s>1000</s> 500</p>
Lists
8. <ol> - Ordered List

Used for numbered lists.

<ol>
  <li>Tea</li>
  <li>Coffee</li>
</ol>
9. <ol type="">

Used to change numbering style.

<ol type="A">
  <li>HTML</li>
  <li>CSS</li>
</ol>
Types:
1 → 1,2,3
A → A,B,C
a → a,b,c
I → I,II,III
i → i,ii,iii
10. <li> - List Item

Represents one item in a list.

<li>Apple</li>
11. <ul> - Unordered List

Used for bullet lists.

<ul>
  <li>Apple</li>
  <li>Mango</li>
</ul>
12. <ul style="list-style-type:;">

Used to change bullet style.

<ul style="list-style-type:square;">
  <li>Apple</li>
  <li>Mango</li>
</ul>
Types:
disc
circle
square
none
13. <dl> - Description List

Used for terms and descriptions.

<dl>
  <dt>HTML</dt>
  <dd>Markup Language</dd>
</dl>
14. <dt> - Description Term

Represents the title or term.

<dt>HTML</dt>
15. <dd> - Description Data

Represents the description.

<dd>Used for webpage structure</dd>
Links
16. <a> - Anchor Tag

Used to create links.

<a href="https://google.com">Google</a>
17. <a> with Unordered List

Used for navigation menus.

<ul>
  <li><a href="#">Home</a></li>
  <li><a href="#">About</a></li>
</ul>
18. mailto

Used to open email client.

<a href="mailto:test@gmail.com">Email Us</a>
19. target

Used to control where the link opens.

<a href="https://google.com" target="_blank">Google</a>
Types:
_blank → Opens in new tab
_self → Opens in same tab
20. tel

Used to make phone calls.

<a href="tel:+923001234567">Call Now</a>
Images
21. <img>

Used to display images.

<img src="pic.jpg">
22. height and width

Used to set image size.

<img src="pic.jpg" height="200" width="300">
23. align

Used to align image (old method).

<img src="pic.jpg" align="right">
Types:
left
right
top
middle

Note: align is outdated. Use CSS instead.