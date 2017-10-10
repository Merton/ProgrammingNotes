# Front-end Useful Notes on HTML, CSS, Javascript and tools like Sass, grunt, and twig.
## CSS

### Good practice
CSS should always be as generic as posible, starting with a broad idea of what fonts, colours, sizing you want things to look like. Anything that then sits outside of those catagories can be defined more specifically.

1. inline css ( html style attribute ) overrides css rules in style tag and css file
2. a more specific selector takes precedence over a less specific one -ie, id's take priority over Classes
3. rules that appear later in the code override earlier rules if both have the same specificity.
4. A css rule with !important always takes precedence.

### Susy vs Flexbox
Flexbox is CSS3's solution to creating flexible grid layouts, it is relatively new, and because of this, it means it is not perfectly supported. Therefore the alternative, is to use Susy, a grid layout engine for Sass. 
To implement it, you first need to define susy's settings.
You can do this in your css file like so:
```
 $susy: (
  columns: 12,
  container: 80em, //1280px;
  gutters: .5,
  // gutter-position: inside,
  global-box-sizing: border-box,
  math: fluid,
  output: float
 );

```
The important part here is the columns, which is commonly set to 12 but can be any integer (Ideally one with many factors). This specifies how many columns the page is broken into. The container attribute specified the width that it should extend too. 

The next part is to make your susy grid, this should be added to your item that is located inside the container of all the objects:
```
@include gallery(6 of 12);
```
or the more common method
```
@include span(6 of 12);
```
The 6 of 12 creates a 2 column layout, because 6 fits into 12 two times. If you wanted 3 columns, you do (12/3) = 4, so (4 of 12) etc.

Finally add:
```
@include container;
```
to set the container you're in to the grid container.


## Twig

### Logical statements and Variables

**Logical statements** have to be used within {%  %} brackets, for instance:
```
{% if bool %}
  ...
{% elseif bool %}
  ...
{% else %}
  ...
{% endif %}
```

**Variables** can be created by using the `set` function. `{% set test = ... %}`.

They can then be accessed by using double curly brackets {{ test }} in the templates. 

### Ternary Operators (?:)
Ternary operators are useful for defining variables, and condense down if statements.

**example**

```
{{ foo ? 'yes' : 'no' }}
Evaluates:
if foo echo yes else echo no
```
The first statement after the ? will be executed if the bool equates to true, otherwise the statement after the : will be executed.

**Advanced example**

The ?? can be used to check that the variable is defined and not null, otherwise it will fail.

`{{ (mobileHeaderImage) ?? desktopHeaderImage ?? "" }}`

In this example it equates to: if the mobile header exists, use it, else use the desktop header, if that doesnt exist use nothing ("").
