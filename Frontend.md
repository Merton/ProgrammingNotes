# Front-end Useful Notes on HTML, CSS, Javascript and tools like Sass, grunt, and twig.
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
