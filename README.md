# php-ast-reverter
A tool that reverts an abstract syntax tree (AST) produced by the
[php-ast](https://github.com/nikic/php-ast) extension back into (somewhat)
PSR-compliant code. This enables for code preprocessing to be done.

Requirements:
 - PHP 7.*
 - [php-ast](https://github.com/nikic/php-ast) extension (compatible with
 version 30, 35, and 40)

## Installation

### Composer
```
composer require tpunt/php-ast-reverter
```

## Example

Running the following code snippet:
```php
<?php

$code = <<<'end'
<?php

/**
 * My testing class
 */
class ClassName extends AnotherClass implements AnInterface
{
    /**
     * Some property
     */
    private $prop = 0;

    const TEST = 'string';

    use TraitA, TraitB {
        TraitA::func1 insteadof TraitB;
        TraitB::func1 as protected func2;
    }

    /**
     * Some useless constructor
     */
    function __construct(int $arg = 1)
    {
        $this->prop = $arg;
    }
}
end;

$ast = ast\parse_code($code, $version=30);

echo (new AstReverter\AstReverter)->getCode($ast);
```

Will output:
```php
<?php

/**
 * My testing class
 */
class ClassName extends AnotherClass implements AnInterface
{
    /**
     * Some property
     */
    private $prop = 0;
    const TEST = "string";
    use TraitA, TraitB {
        TraitA::func1 insteadof TraitB;
        TraitB::func1 as protected func2;
    }
    /**
     * Some useless constructor
     */
    public function __construct(int $arg = 1)
    {
        $this->prop = $arg;
    }
}

```
