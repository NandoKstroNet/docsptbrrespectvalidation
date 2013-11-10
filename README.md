Docs PT-BR Respect\Validation
=========================

Trandução da Documentação do Respect\Validation

==========================
Respect\Validation [![Build Status](https://secure.travis-ci.org/Respect/Validation.png)](http://travis-ci.org/Respect/Validation)
==================

O mecanismo de validação mais impressionante criado em PHP.

- Fluent/Chained builders like `v::numeric()->positive()->between(1, 256)->validate($myNumber)` (more samples below)
- Exceptions informativas, impressionantes.
- Mais de 30 validadores totalmente testados.

Instalação
------------

O pacotes estão disponiveis no [PEAR](http://respect.li/pear) e [Composer](http://packagist.org/packages/Respect/Validation). Autoloading é compativel com [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md).

Recursos
-------------

### Namespace Import

Respect\Validation é namespace, mas você pode tornar sua vida mais fácil importando apenas a classe em seu contexto:

    <?php
    use Respect\Validation\Validator as v;

### Simple Validation

O Hello World do validador é somente isto:

    $number = 123;
    v::numeric()->validate($number); //true

### Chained Validation

É possivel utilizar os validadores em cadeias(chain). O exemplo abaixo valida um conjunto de caracteres que contém números e letras, sem espaços e está entre 1 e 15:

    $usernameValidator = v::alnum()->noWhitespace()->length(1,15);
    $usernameValidator->validate('alganet'); //true

### Validating Object Attributes

Dados o simples Objeto a seguir:

    $user = new stdClass;
    $user->name = 'Alexandre';
    $user->birthdate = '1987-07-01';

É possivel validar seu atributos em uma única chain:

    $userValidator = v::attribute('name', v::string()->length(1,32))
                      ->attribute('birthdate', v::date()->minimumAge(18));

    $userValidator->validate($user); //true

Validar chaves de array também é possivel utilizando `v::key()`

Note que usamos `v::string()` e `v::date()` no inicio do validador. Embora não seja obrigatório, é uma boa prática usar o tipo do objeto validado como o primeiro nós na cadeia(chain).

### Input optional

Todos os validadores tratam a entrada como opcional e aceitarão cadeia de caracteres válidos vazia, salvo alguma indicação contrária na documentação.

Nós usamos o validador `v:notEmpty()` prefixado para não permitir a entrada vazia e efetivamente definimos o campo como obrigatório, os inputs serão requeridos ou a validação falhará.

    v::string()->notEmpty()->validate(''); //false input required

### Negating Rules

Você pode usar  `v::not()` para negar qualquer regra:

    v::not(v::int())->validate(10); //false, input must not be integer

### Validator Reuse

Uma vez criado, você pode reutilizar seu validador em qualquer lugar. Lembra  $usernameValidator?

    $usernameValidator->validate('respect');            //true
    $usernameValidator->validate('alexandre gaigalas'); //false
    $usernameValidator->validate('#$%');                //false

### Informative Exceptions

Quando algo está errado, o Validation pode dizer o que esta acontecendo exatamente. Para isso usamos o método `assert()` em vez de `validate()`:

    try {
        $usernameValidator->assert('really messed up screen#name');
    } catch(\InvalidArgumentException $e) {
       echo $e->getFullMessage();
    }

A mensagem impressa é exatamente esta, como uma árvore de texto:

    \-All of the 3 required rules must pass
      |-"really messed up screen#name" must contain only letters (a-z) and digits (0-9)
      |-"really messed up screen#name" must not contain whitespace
      \-"really messed up screen#name" must have a length between 1 and 15

### Getting Messages

A árvore de texto lançada pela exception é bom, mas inútilizável em um formulário html ou algo mais personalizado. Você pode usar o
`findMessages()` para isso:

    try {
        $usernameValidator->assert('really messed up screen#name');
    } catch(\InvalidArgumentException $e) {
       var_dump($e->findMessages(array('alnum', 'length', 'noWhitespace')));
    }

`findMessages()` retorna um array com as mensagens do validadores solicitados.

### Custom Messages

Pegar as mensagens como array é muito legal, mas as vezes você precisa customiza-las para poder apresentar aos usuários. Isto é possivel utilizando o método `findMessages()` assim:

       $errors = $e->findMessages(array(
            'alnum'        => '{{name}} must contain only letters and digits',
            'length'       => '{{name}} must not have more than 15 chars',
            'noWhitespace' => '{{name}} cannot contain spaces'
        ));

Para todas as mensagens, a variável `{{name}}` e `{{input}}` estão disponiveis para o template.
### Validator Name
--
Em  `v::attribute()` e `v::key()`, `{{name}}`  é o nome do attribute/key. Você pode customizar um validador usando um nome:

    v::date('Y-m-d')->between('1980-02-02', 'now')->setName('Member Since');


### Zend/Symfony Validators

É possivel utilizar também validadores de outros frameworks, se eles estiverem devidamente instalados:

    $hostnameValidator = v::zend('Hostname')->assert('google.com');
    $timeValidator     = v::sf('Time')->assert('22:00:01');

### Validation Methods

Já vimos o  `validate()` que retorna true ou false e `assert()` que lança um relatório de validação completa. Há também um método chamado `check()` que retorna uma exceção com o primeiro erro encontrado apenas:

    try {
        $usernameValidator->check('really messed up screen#name');
    } catch(\InvalidArgumentException $e) {
       echo $e->getMainMessage();
    }

Mensagem:

    "really messed up screen#name" must contain only letters (a-z) and digits (0-9)

Referência
---------

### Tipos

  * v::arr()
  * v::bool()
  * v::date()
  * v::float()
  * v::hexa() *(deprecated)*
  * v::instance()
  * v::int()
  * v::nullValue()
  * v::numeric()
  * v::object()
  * v::string()
  * v::xdigit()

### Genéricos

  * v::call()
  * v::callback()
  * v::not()
  * v::when()
  * v::alwaysValid()
  * v::alwaysInvalid()

### Comparando valores

  * v::between()
  * v::equals()
  * v::max()
  * v::min()

### Numéricos

  * v::between()
  * v::bool()
  * v::even()
  * v::float()
  * v::hexa() *(deprecated)*
  * v::int()
  * v::multiple()
  * v::negative()
  * v::notEmpty()
  * v::numeric()
  * v::odd()
  * v::perfectSquare()
  * v::positive()
  * v::primeNumber()
  * v::roman()
  * v::xdigit()

### String

  * v::alnum()
  * v::alpha()
  * v::between()
  * v::charset()
  * v::consonants() *(deprecated)*
  * v::consonant()
  * v::contains()
  * v::cntrl()
  * v::digits() *(deprecated)*
  * v::digit()
  * v::endsWith()
  * v::in()
  * v::graph()
  * v::length()
  * v::lowercase()
  * v::notEmpty()
  * v::noWhitespace()
  * v::prnt()
  * v::punct()
  * v::regex()
  * v::slug()
  * v::space()
  * v::startsWith()
  * v::uppercase()
  * v::uppercase()
  * v::version()
  * v::vowels() *(deprecated)*
  * v::vowel()
  * v::xdigit()

### Arrays

  * v::arr()
  * v::contains()
  * v::each()
  * v::endsWith()
  * v::in()
  * v::key()
  * v::length()
  * v::notEmpty()
  * v::startsWith()

### Objetos

  * v::attribute()
  * v::instance()
  * v::length()

### Data e hora

  * v::between()
  * v::date()
  * v::leapDate()
  * v::leapYear()

### Grupo de validadores

  * v::allOf()
  * v::noneOf()
  * v::oneOf()

### Regional

  * v::tld()
  * v::countryCode()

### Arquivos

  * v::directory()
  * v::exists()
  * v::file()
  * v::readable()
  * v::symbolicLink()
  * v::uploaded()
  * v::writable()

### Outros

  * v::cnh()
  * v::cnpj()
  * v::cpf()
  * v::domain()
  * v::email()
  * v::ip()
  * v::json()
  * v::macAddress()
  * v::phone()
  * v::sf()
  * v::zend()

### Alfabético

#### v::allOf($v1, $v2, $v3...)

Irá validar se todos os validadores internos forem válidos:

    v::allOf(
        v::int(),
        v::positive()
    )->validate(15); //true

Isto é semelhante a cadéia, porém sua sintaxe permite definir nomes personalizados para cada nó:

    v::allOf(
        v::int()->setName('Account Number'),
        v::positive()->setName('Higher Than Zero')
    )->setName('Positive integer')
     ->validate(15); //true

Veja também:

  * v::oneOf()  - Validates if at least one inner rule pass
  * v::noneOf() - Validates if no inner rules pass
  * v::when()   - A Ternary validator

#### v::alnum()
#### v::alnum(string $additionalChars)

Validadores de caracteres alfanumérico de a-Z e 0-9.

    v::alnum()->validate('foo 123'); //true

Um parâmetro para caracteres extras, também pode ser adicionado:

    v::alnum('-')->validate('foo - 123'); //true

Este validador permite espaços em branco, caso você queira removê-los, acrescente o método `->noWhitespace()` a cadeia:

    v::alnum()->noWhitespace->validate('foo 123'); //false

Por default os valores vazios(empty) são aceitos, caso você queira invalidá-los, adicione o método `->notEmpty()` à cadeia:

    v::alnum()->notEmpty()->validate(''); //false

Você pode restringir os casos usando os validadores `->lowercase()` e
`->uppercase()`:

    v::alnum()->uppercase()->validate('aaa'); //false

Template message para este validador inclui `{{additionalChars}}` com uma cadéia de caracteres passadas como parâmetros.

Veja também:

  * v::alpha()  - a-Z, empty or whitespace only
  * v::digit() - 0-9, empty or whitespace only
  * v::consonant()
  * v::vowel()
{{::Continua::}}