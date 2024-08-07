[![License](https://img.shields.io/packagist/l/Higgs/Html.svg?style=flat-square)](https://packagist.org/packages/codehiggs/html)
[![Say Thanks!](https://img.shields.io/badge/Say-thanks-brightgreen.svg?style=flat-square)](https://saythanks.io/to/jalexiscv)
[![Donate!](https://img.shields.io/badge/Donate-Paypal-brightgreen.svg?style=flat-square)](https://paypal.me/jalexiscv)

# HTML

## Descripción

Esta es una biblioteca PHP que maneja la generación de etiquetas HTML, sus atributos y contenido. Es segura, eficiente y
fácil de usar.

## Requisitos

* PHP 8.0 o superior
* Composer 2.6 for regular usage.

## Instalación

```composer require Higgs/Html```

## Uso

```php
<?php

include 'vendor/autoload.php';

// Meta object.
$meta = \Higgs\Html\HtmlTag::tag('meta', ['name' => 'author']);
$meta->attr('content', 'pol dellaiera');

// Title object.
$title = \Higgs\Html\HtmlTag::tag('h1', ['class' => 'title'], 'Welcome to HTMLTag');

// Paragraph object.
$paragraph = \Higgs\Html\HtmlTag::tag('p', ['class' => 'section']);
$paragraph->attr('class')->append('paragraph');
$paragraph->content('This library helps you create HTML.');

// Simple footer
$footer = \Higgs\Html\HtmlTag::tag('footer', [], 'Thanks for using it!');

// Body tag.
// Add content that can be transformed into strings.
$body = \Higgs\Html\HtmlTag::tag('body', [], [$title, $paragraph, $footer]);

// Fix something that was already added.
$paragraph->attr('class')->remove('section')->replace('paragraph', 'description');

// Alter the values of a specific attributes.
$meta->attr('content')->alter(
    function ($values) {
        return array_map('strtoupper', $values);
    }
);

echo $meta . $body;
```

Will print:

```html

<meta content="POL DELLAIERA" name="author"/>
<body>
<h1 class="title">Welcome to HTMLTag</h1>
<p class="description">This library helps you create HTML.</p>
<footer>Thanks for using it!</footer>
</body>
```

# HTML Builder

The library comes with an HTML Builder class that allows you to quickly create HTML content.

```php
<?php 

include 'vendor/autoload.php';

$builder = new \Higgs\Html\HtmlBuilder();

$html = $builder
    ->c(' Comment 1 ') // Add a comment
    ->p(['class' => ['paragraph']], 'some content')
    ->div(['class' => 'container'], 'this is a simple div')
    ->_() // End tag <div>
    ->c(' Comment 2 ')
    ->region([], 'region with <unsafe> tags')
    ->_()
    ->c(' Comment 3 ')
    ->a()
    ->c(' Comment 4 ')
    ->span(['class' => 'link'], 'Link content')
    ->_()
    ->div(['class' => 'Unsecure "classes"'], 'Unsecure <a href="#">content</a>')
    ->_()
    ->c(' Comment 5 ');

echo $html;
```

This will produce:

```html
<!-- Comment 1 -->
<p class="paragraph">
    some content
<div class="container">
    this is a simple div
</div>
</p>
<!-- Comment 2 -->
<region>
    region with &lt;unsafe&gt; tags
</region>
<!-- Comment 3 -->
<a>
    <!-- Comment 4 -->
    <span class="link">
    Link content
  </span>
</a>
<div class="Unsecure &quot;classes&quot;">
    Unsecure &lt;a href=&quot;#&quot;&gt;content&lt;/a&gt;
</div>
<!-- Comment 5 -->
```

## Technical notes

### Tag analysis

```
 The tag name              An attribute                 The content
  |                              |                           |
 ++-+                      +-----+-----+                +----+-----+
 |  |                      |           |                |          |
 
<body class="content node" id="node-123" data-clickable>Hello world!</body>

      |                                               |
      +-----------------------+-----------------------+
                              |
                        The attributes
```

The library is built around 3 objects.

* The Tag object that handles the attributes, the tag name and the content,
* The Attributes object that handles the attributes,
* The Attribute object that handles an attribute which is composed of name and its value(s).

The Tag object uses the Attributes object which is, basically, the storage of Attribute objects.
You may use each of these objects individually.

All methods are documented through interfaces and your IDE should be able to autocomplete when needed.

Most methods parameters are [variadics](http://php.net/manual/en/functions.arguments.php#functions.variable-arg-list)
and
accept unlimited nested values or array of values.
You can also chain most of the methods.

The allowed type of values can be almost anything. If it's an object, it must implements the `__toString()` method.

#### Examples

Method chaining:

```php
<?php 

include 'vendor/autoload.php';

$tag = \Higgs\Html\HtmlTag::tag('body');
$tag
    ->attr('class', ['FRONT', ['NODE', ['sidebra']], 'node', '  a', '  b  ', [' c']])
    ->replace('sidebra', 'sidebar')
    ->alter(
        function ($values) {
            $values = array_map('strtolower', $values);
            $values = array_unique($values);
            $values = array_map('trim', $values);
            natcasesort($values);

            return $values;
        }
    );
$tag->content('Hello world');

echo $tag; // <body class="a b c front node sidebar">Hello world</body>
```

The following examples will all produce the same HTML.

```php
<?php 

include 'vendor/autoload.php';

$button = html()::tag('button');
$button->attr('class', 'tabs-nav-link nav-link' . ($data['active'] ? ' active' : ''));
$button->attr('id', $data['id'] . '-tab');
$button->attr('data-bs-toggle', 'tab');
$button->attr('data-bs-target', '#' . $data['id']);
$button->attr('type', 'button');
$button->attr('role', 'tab');
$button->attr('aria-controls', $data['id']);
$button->attr('aria-selected', $data['active'] ? 'true' : 'false');
$button->content($data['text']);
echo($button->render());































$tag = \Higgs\Html\HtmlTag::tag('body');
$tag->attr('class', ['front', ['node', ['sidebar']]]);
$tag->content('Hello world');

echo $tag; // <body class="front node sidebar">Hello world</body>
```

```php
<?php 

include 'vendor/autoload.php';

$tag = \Higgs\Html\HtmlTag::tag('body');
$tag->attr('class', 'front', 'node', 'sidebar');
$tag->content('Hello world');

echo $tag; // <body class="front node sidebar">Hello world</body>
```

```php
<?php 

include 'vendor/autoload.php';

$tag = \Higgs\Html\HtmlTag::tag('body');
$tag->attr('class', ['front', 'node', 'sidebar']);
$tag->content('Hello world');

echo $tag; // <body class="front node sidebar">Hello world</body>
```

```php
<?php 

include 'vendor/autoload.php';

$tag = \Higgs\Html\HtmlTag::tag('body');
$tag->attr('class', 'front node sidebar');
$tag->content('Hello world');

echo $tag; // <body class="front node sidebar">Hello world</body>
```

### Tag object

```php
<?php 

include 'vendor/autoload.php';

$tag = \Higgs\Html\HtmlTag::tag('body');
$tag->attr('class', 'front');
$tag->content('Hello world');

echo $tag; // <body class="front">Hello world</body>
```

### Attributes object

```php
<?php 

include 'vendor/autoload.php';

$attributes = \Higgs\Html\HtmlTag::attributes();
$attributes->append('class', 'a', 'b', 'c');
$attributes->append('id', 'htmltag');

// Hence the trailing space before the class attribute.
echo $attributes; //  class="a b c" id="htmltag"
```

### Attribute object

```php
<?php 

include 'vendor/autoload.php';

$attribute = \Higgs\Html\HtmlTag::attribute('class', 'section');

echo $attribute; // class="section"
```

## Dependency injection and extensions

Thanks to the factories provided in the library, it is possible to use different classes in place of the default ones.

Ex: You want to have a special handling for the "class" attribute.

```php
<?php 

include 'vendor/autoload.php';

class MyCustomAttributeClass extends \Higgs\Html\Attribute\Attribute {
    /**
     * {@inheritdoc}
     */
    protected function preprocess(array $values, array $context = []) {
        // Remove duplicated values.
        $values = array_unique($values);

        // Trim values.
        $values = array_map('trim', $values);

        // Convert to lower case
        $values = array_map('strtolower', $values);

        // Sort values.
        natcasesort($values);

        return $values;
    }
}

\Higgs\Html\Attribute\AttributeFactory::$registry['class'] = MyCustomAttributeClass::class;

$tag = HtmlTag::tag('p');

// Add a class attribute and some values.
$tag->attr('class', 'E', 'C', ['A', 'B'], 'D', 'A', ' F ');
// Add a random attribute and the same values.
$tag->attr('data-original', 'e', 'c', ['a', 'b'], 'd', 'a', ' f ');

echo $tag; // <p class="a b c d e f" data-original="e c a b d a  f "/>
```

The same mechanism goes for the `Tag` class.

## Security

To avoid security issues, every printed objects are escaped.

If objects are used as input and if they implement the `__toString()` method, they will be converted to string.
It's up to the user to make sure that they print **unsafe** output so they are not escaped twice.

## Code quality, tests and benchmarks

Every time changes are introduced into the library, [Travis CI](https://travis-ci.org/Higgs/Html/builds) run the tests
and the benchmarks.

The library has tests written with [PHPSpec](http://www.phpspec.net/).
Feel free to check them out in the `spec` directory. Run `composer phpspec` to trigger the tests.

[PHPBench](https://github.com/phpbench/phpbench) is used to benchmark the library, to run the
benchmarks: `composer bench`

[PHPInfection](https://github.com/infection/infection) is used to ensure that your code is properly tested,
run `composer infection` to test your code.

## Contributing

Feel free to contribute to this library by sending Github pull requests. I'm quite reactive :-)
