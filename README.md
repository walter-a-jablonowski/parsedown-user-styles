# Parsedown User Styles

**User defined syntax for ParsedownExtra**

Define whatever syntax you want... using a minimalistic PHP class. Also capable of running PHP embedded in md. Use it for calculations, dynamic content, ...

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> If you like visit my personal homepage: [walter-a-jablonowski.github.io](https://walter-a-jablonowski.github.io)


## Usage

See also running demo in /demo

```
composer require walter-a-jablonowski/parsedown-user-styles
```

Include your stylesheets if you have class names in replace html.

Define whatever syntax you want, these are just for demo. Using [Symfony yaml](https://symfony.com/doc/current/components/yaml) here.

```php
$md = new ParsedownUserStyles();  // pass $userdata if needed (see third style below)

// use parsedown's methods as needed

$md->setStyles( Yaml::parse( file_get_contents( ... )) );  // get your styles.yml
$mdString = $md->text( ... );
```

**Define styles**

styles.yml

```yaml
Unique - name:        # Each style can have a name and multiple replacements

  -                   # Simple str replace
    find:    "[["
    replace: "<span class="header">"
  -
    find:    "]]"
    replace: "</span>"

Unique - name 2:     # regex version

  -
    regFind: "\\{Img:\\s*(.*)\\}"
    replace: "<img src=\"$1\" style=\"max-width: 100%;\" />"

Unique - name 5:     # Use of anonymous function
                     # $found is result of preg_match_all() with SET ORDER (no OFFSET CAPTURE)
  -
    regFind: "\\{Url:\\s*([^\\s]*)\\s*(.*)\\}"
    do: |
    
      function( $text, $found, $userdata = [] ) {

        // do something with $found
        // replace in $text

        foreach( $found as $f )
        {
          $fullStr = $f[0];
          $label   = $f[1];
          $url     = $f[2];

          $s = str_replace( "[URL]",   "http://" . $url, "<a href=\"[URL]\">[LABEL]</a>" );
          $s = str_replace( "[LABEL]", $label, $s );

          $text = str_replace( $fullStr, $s, $text );
        }
        
        return $text;
      }
```


### Embed PHP in md

Just include PHP code inside normal PHP tags

```markdown
# My markdown

<?= 'Hi' ?>

Some content
```

The code will be executed if you call `textPHP($file, $args = [])` instead of `text($text)`. Use $args for vars.


### Pure code style

All style samples see above.

```php
$md = new ParsedownUserStyles();

// use parsedown's methods as needed

$md->addStyle( 'Unique - name', [
  [
    'find'    => '[[',
    'replace' => '<span class="header">'
  ],
  [
    'find'    => ']]',
    'replace' => '</span>'
  ]
]);

$mdString = $md->text( ... );
```


## LICENSE

Copyright (C) Walter A. Jablonowski 2020, MIT [License](LICENSE)

Licenses of third party software and stuff see [credits](credits.md).


[Privacy](https://walter-a-jablonowski.github.io/privacy.html) | [Legal](https://walter-a-jablonowski.github.io/imprint.html)
