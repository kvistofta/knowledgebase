
Headings: 
H1 is one `#` followed by the heading
H2 is two `##` followed by the heading
and so on...

**bold** text is surrounded with double `asterixes **` before and after
*italic* text is surrounded with a single `asterixes * ` before and after

Ordered lists uses numbers followed by dot:
1. first item
2. second item

Unordered lists uses dashes `-`
- first item
- second item


code is either surrounded by a single back-tick `` ` ``or double back-ticks ``` `` ``` if you want to include single backticks inside of code.

Code blocks are being
	created
		by indenting
		every level with
	either minimum 4 spaces
or a tab.


A horisontal ruler is created with at least three asterisks, dashes or underscores:
***


A URL link is created with a description between brackets [] directly followed by the url in parenthesis. Like this:
```[This is a link to google](https://google.com)```


More syntaxes:
<https://www.markdownguide.org/basic-syntax/#code>





# Markdown Cheat Sheet

Thanks for visiting [The Markdown Guide](https://www.markdownguide.org)!

This Markdown cheat sheet provides a quick overview of all the Markdown syntax elements. It can’t cover every edge case, so if you need more information about any of these elements, refer to the reference guides for [basic syntax](https://www.markdownguide.org/basic-syntax) and [extended syntax](https://www.markdownguide.org/extended-syntax).

## Basic Syntax

These are the elements outlined in John Gruber’s original design document. All Markdown applications support these elements.

### Heading

# H1
## H2
### H3

### Bold

**bold text**

### Italic

*italicized text*

### Blockquote

> blockquote

### Ordered List

1. First item
2. Second item
3. Third item

### Unordered List

- First item
- Second item
- Third item

### Code

`code`

### Horizontal Rule

---

### Link

[Markdown Guide](https://www.markdownguide.org)

### Image

![alt text](https://www.markdownguide.org/assets/images/tux.png)

## Extended Syntax

These elements extend the basic syntax by adding additional features. Not all Markdown applications support these elements.

### Table

| Syntax | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

### Fenced Code Block

```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### Footnote

Here's a sentence with a footnote. [^1]

[^1]: This is the footnote.

### Heading ID

### My Great Heading {#custom-id}

### Definition List

term
: definition

### Strikethrough

~~The world is flat.~~

### Task List

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

### Emoji

That is so funny! :joy:

(See also [Copying and Pasting Emoji](https://www.markdownguide.org/extended-syntax/#copying-and-pasting-emoji))

### Highlight

I need to highlight these ==very important words==.

### Subscript

H~2~O

### Superscript

X^2^
