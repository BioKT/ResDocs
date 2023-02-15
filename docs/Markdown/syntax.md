# Markdown Syntax

The syntax that allows us to write this web site is *Markdown*. You can find more information about it on the internet but here you have some initial tips.

## Block Elements.

- Paragraphs and line breaks.

For a new paragraph, use two _enter_ keys. Double blank space is not supported.
For a line break, you will need two _space bar_ keys before using the _enter_ key.

* * *
      
- Headers.

Headers are created by levels using `#` symbol. Here you have an example:

```
# Header 1
## Header 2
### Header 3
#### Header 4
```
will correspond to:
# Header 1
## Header 2
### Header 3
#### Header 4

* * *

- Quotes.

To quote text you must use the symbol `>` at the start of your text:

```
> Hace falta toda una vida para aprender a vivir. - Séneca
```
will give you:
> Hace falta toda una vida para aprender a vivir. - Séneca

* * *

- Lists.

There are two types of lists: unordered using `*`, `-` or `+` and ordered using numbers.
You can also nest elements of the lists by using four _space bar_ keys before the corresponding symbol of your list.
Here an example:

```
- Unordered List
    + Element 1

        You can include paragraphs or other text inside lists by leaving a blank space after and the corresponding _space bar_ keys for the nested element (8 in this case).
    + Element 2
        + Element 3
* Ordered List
    1.  Ordered element 1
    2.  Ordered element 2
``` 
will give you:

- Unordered List
    + Element 1

        You can include paragraphs or other text inside lists by leaving a blank space after and the corresponding _space bar_ keys for the nested element (8 in this case).
    + Element 2
        + Element 3
* Ordered List
    1.  Ordered element 1
    2.  Ordered element 2

* * *

- Code.

For writing code you can use the ``` symbol before and after to create _code blocks_ that will be visualized inside a code box. For "inline code" see Code in the Line Elements section (below).

```
\```
echo "Hello World"
\```
```
will give you:
```
echo "Hello World"
```

* * *
 
- Horizontal lines.

Horizontal lines are used to enhance the visualization and reading of your documentation. Notice how they have been used to separate the sections in this document.
You can place them by repeating the symbol `*`, `-` or `_` three times. It is always a good idea to separate them with spaces (see the example below) and leave blank spaces before and after.

```
* * *
- - -
_ _ _
```
will give you:

* * *
- - -
_ _ _


## Line Elements.
- Emphasis.

To emphasis text, markdown have options line **bold** text and *italic*. Use one `*` symbol before and after the text to apply italic and two for bold. You can apply both by using the symbol three times (combining bold and italic).
 
* * *

- Links.

For introducing links in the text, use the syntax [text to hyperlink] followed by (URL or .md file path inside repo). Here you have an example where our [BioKT Lab Page](https://sites.google.com/view/biokt?pli=1) is hyperlinked using [BioKT Lab Page](URL).

* * *

- Code.

If you want to introduce some commands "in line", `like this`, you have to use the ` symbol before and after.

* * *

## Others.
- Images.

* * *

- Skip Markdown syntax.

If you want to ignore the characters of the Markdown syntax, use \ before any character.
* * *
