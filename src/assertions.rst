

.. _appendixes.assertions:

=========
Asserções
=========

Esse apêndice lista os vários métodos de asserções  que estão disponíveis.

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

Uso Estático vs. Não-Estático de Métodos de Asserção
####################################################

Asserções do PHPUnit são implementadas in PHPUnit\\Framework\\Assert.
PHPUnit\\Framework\\TestCase herda de PHPUnit\\Framework\\Assert.

Os métodos de asserção são declarados static e podem ser invocados
de qualquer contexto usando PHPUnit\\Framework\\Assert::assertTrue(),
por exemplo, ou usando $this->assertTrue() ou self::assertTrue(),
por exemplo, em uma classe que extende PHPUnit\\Framework\\TestCase.

Na verdade, você pode usar invólucros de funções globais tal como assertTrue() em
qualquer contexto (incluindo classes que extendem PHPUnit\\Framework\\TestCase)
quando você (manualmente) inclui o arquivo de código-fonte :file:`src/Framework/Assert/Functions.php`
que vem com PHPUnit.

Uma questão comum, especialmente de desenvolvedores novos em PHPUnit, é se
usar $this->assertTrue() ou self::assertTrue(),
por exemplo, é "a forma certa" de invocar uma asserção. A resposta rápida
é: não existe forma certa. E também não existe forma errada. Isso é mais uma
questão de preferência.

Para a maioria das pessoas "se sente melhor" usar $this->assertTrue()
porque o método de teste é invocado em um objeto de teste. O fato de que os
métodos de asserção são declarados static permite (re)usá-los
de fora do escopo de um objeto de teste. Por último, os invólucros de funções globais
permitem desenvolvedores digitarem menos caracteres (assertTrue() ao invés
de $this->assertTrue() ou self::assertTrue()).

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o ``$array`` não contém a ``$key``.

``assertArrayNotHasKey()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertArrayHasKey()
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayHasKeyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$className::attributeName`` não existir.

``assertClassNotHasAttribute()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertClassHasAttribute()
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ClassHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertArraySubset:

assertArraySubset()
###################

``assertArraySubset(array $subset, array $array[, bool $strict = '', string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$array`` não contém o ``$subset``.

``$strict`` é uma flag usada para comparar a identidade de objetos dentro dos arrays.

.. code-block:: php
    :caption: Utilização de assertArraySubset()
    :name: appendixes.assertions.assertArraySubset.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArraySubsetTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o ``$className::attributeName`` não existir.

``assertClassNotHasStaticAttribute()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertClassHasStaticAttribute()
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o ``$needle`` não é um elemento de ``$haystack``.

``assertNotContains()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeContains()`` e ``assertAttributeNotContains()`` são invólucros convenientes que usam um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como a haystack.

.. code-block:: php
    :caption: Utilização de assertContains()
    :name: appendixes.assertions.assertContains.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = FALSE])``

Reporta um erro identificado pela ``$message`` se ``$needle`` não é uma substring de ``$haystack``.

Se ``$ignoreCase`` é ``true``, o teste irá ser case insensitive.

.. code-block:: php
    :caption: Utilização de assertContains()
    :name: appendixes.assertions.assertContains.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. code-block:: php
    :caption: Utilização de assertContains() com $ignoreCase
    :name: appendixes.assertions.assertContains.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = null, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$haystack`` não contém somente variáveis do tipo ``$type``.

``$isNativeType`` é uma flag usada para indicar se ``$type`` é um tipo nativo do PHP ou não.

``assertNotContainsOnly()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeContainsOnly()`` e ``assertAttributeNotContainsOnly()`` são invólucros convenientes que usam um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como a haystack.

.. code-block:: php
    :caption: Utilização de assertContainsOnly()
    :name: appendixes.assertions.assertContainsOnly.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsOnlyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$haystack`` não contém somente instâncias da classe ``$classname``.

.. code-block:: php
    :caption: Utilização de assertContainsOnlyInstancesOf()
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o número de elementos no ``$haystack`` não for ``$expectedCount``.

``assertNotCount()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertCount()
    :name: appendixes.assertions.assertCount.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CountTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, ['foo']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit CountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o diretório especificado por ``$directory`` não existir.

``assertDirectoryNotExists()`` é o inverso dessa asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertDirectoryExists()
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o diretório especificado por ``$directory`` não é um diretório ou não é legível.

``assertDirectoryNotIsReadable()`` é o inverso dessa asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertDirectoryIsReadable()
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o diretório especificado por ``$directory`` não é um diretório ou não é gravável.

``assertDirectoryNotIsWritable()`` é o inverso dessa asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertDirectoryIsWritable()
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DirectoryIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$actual`` não está vazio.

``assertNotEmpty()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeEmpty()`` e ``assertAttributeNotEmpty()`` são invólucros convenientes que podem ser aplicados para um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto.

.. code-block:: php
    :caption: Utilização de assertEmpty()
    :name: appendixes.assertions.assertEmpty.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EmptyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(['foo']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EmptyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEqualXMLStructure:

assertEqualXMLStructure()
#########################

``assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = false, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a estrutura XML do DOMElement no ``$actualElement`` não é igual a estrutura XML do DOMElement no ``$expectedElement``.

.. code-block:: php
    :caption: Utilização de assertEqualXMLStructure()
    :name: appendixes.assertions.assertEqualXMLStructure.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualXMLStructureTest extends TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, true
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualXMLStructureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se as variáveis ``$expected`` e ``$actual`` não são iguais.

``assertNotEquals()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeEquals()`` e ``assertAttributeNotEquals()`` são invólucros convenientes que usam um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertEquals()
    :name: appendixes.assertions.assertEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

Comparações mais especializadas são usadas para tipos de argumentos específicos para ``$expected`` e ``$actual``, veja abaixo.

``assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])``

Reporta um erro identificado pela ``$message`` se os dois floats ``$expected`` e ``$actual`` não estão dentro do ``$delta`` um do outro.

Por favor, leia "`O Que Cada Cientista da Computação Deve Saber Sobre Aritmética de Ponto Flutuante <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_" para entender por que ``$delta`` é necessário.

.. code-block:: php
    :caption: Utilização de assertEquals() with floats
    :name: appendixes.assertions.assertEquals.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.2);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a forma canonical não-comentada dos documentos XML representada pelos objetos DOMDocument ``$expected`` e ``$actual`` não são iguais.

.. code-block:: php
    :caption: Utilização de assertEquals() com objetos DOMDocument
    :name: appendixes.assertions.assertEquals.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se os objetos ``$expected`` e ``$actual`` não tem valores de atributos iguais.

.. code-block:: php
    :caption: Utilização de assertEquals() com objetos
    :name: appendixes.assertions.assertEquals.example4

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se os arrays ``$expected`` e ``$actual`` não são iguais.

.. code-block:: php
    :caption: Utilização de assertEquals() com arrays
    :name: appendixes.assertions.assertEquals.example5

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$condition`` é ``true``.

``assertNotFalse()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertFalse()
    :name: appendixes.assertions.assertFalse.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FalseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(true);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FalseTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo especificado pelo  ``$expected`` não tem o mesmo conteúdo que o arquivo especificado pelo ``$actual``.

``assertFileNotEquals()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertFileEquals()
    :name: appendixes.assertions.assertFileEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileEqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileEqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo especificado pelo ``$filename`` não existir.

``assertFileNotExists()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertFileExists()
    :name: appendixes.assertions.assertFileExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo especificado por ``$filename`` não é um arquivo ou não é legível.

``assertFileNotIsReadable()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Usage of assertFileIsReadable()
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo especificado por ``$filename`` não é um arquivo ou não é gravável.

``assertFileNotIsWritable()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Usage of assertFileIsWritable()
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actual`` não é maior que o valor de ``$expected``.

``assertAttributeGreaterThan()`` é um invólucro conveniente que usa um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertGreaterThan()
    :name: appendixes.assertions.assertGreaterThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreaterThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actual`` não é maior ou igual ao valor de ``$expected``.

``assertAttributeGreaterThanOrEqual()`` é um invólucro conveniente que usa um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertGreaterThanOrEqual()
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$variable`` não é ``INF``.

``assertFinite()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertInfinite()
    :name: appendixes.assertions.assertInfinite.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InfiniteTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InfiniteTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$actual`` não é uma instância de ``$expected``.

``assertNotInstanceOf()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeInstanceOf()`` e ``assertAttributeNotInstanceOf()`` são invólucros convenientes que podem ser aplicados a um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto.

.. code-block:: php
    :caption: Utilização de assertInstanceOf()
    :name: appendixes.assertions.assertInstanceOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InstanceOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InstanceOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInternalType:

assertInternalType()
####################

``assertInternalType($expected, $actual[, $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$actual`` não é do tipo ``$expected``.

``assertNotInternalType()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeInternalType()`` e ``assertAttributeNotInternalType()`` são invólucros convenientes que podem aplicados a um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto.

.. code-block:: php
    :caption: Utilização de assertInternalType()
    :name: appendixes.assertions.assertInternalType.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InternalTypeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InternalTypeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo ou diretório  especificado por ``$filename`` não é legível.

``assertNotIsReadable()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertIsReadable()
    :name: appendixes.assertions.assertIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

Reporta um erro especificado pela ``$message`` se o arquivo ou diretório especificado por ``$filename`` não é gravável.

``assertNotIsWritable()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertIsWritable()
    :name: appendixes.assertions.assertIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actualFile`` não combina com o valor de
``$expectedFile``.

.. code-block:: php
    :caption: Utilização de assertJsonFileEqualsJsonFile()
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actualJson`` não combina com o valor de
``$expectedFile``.

.. code-block:: php
    :caption: Utilização de assertJsonStringEqualsJsonFile()
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actualJson`` não combina com o valor de
``$expectedJson``.

.. code-block:: php
    :caption: Utilização de assertJsonStringEqualsJsonString()
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actual`` não é menor que o valor de ``$expected``.

``assertAttributeLessThan()`` é um invólucro conveniente que usa um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertLessThan()
    :name: appendixes.assertions.assertLessThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o valor de ``$actual`` não é menor ou igual ao valor de ``$expected``.

``assertAttributeLessThanOrEqual()`` é um invólucro conveniente que usa um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertLessThanOrEqual()
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$variable`` não é ``NAN``.

.. code-block:: php
    :caption: Usage of assertNan()
    :name: appendixes.assertions.assertNan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o ``$variable`` não é ``null``.

``assertNotNull()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertNull()
    :name: appendixes.assertions.assertNull.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NullTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NotNullTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$object->attributeName`` não existir.

``assertObjectNotHasAttribute()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertObjectHasAttribute()
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ObjectHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertRegExp:

assertRegExp()
##############

``assertRegExp(string $pattern, string $string[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se ``$string`` não combina com a expressão regular ``$pattern``.

``assertNotRegExp()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertRegExp()
    :name: appendixes.assertions.assertRegExp.example

    <?php
    use PHPUnit\Framework\TestCase;

    class RegExpTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit RegExpTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$string`` não combina com a string ``$format``.

``assertStringNotMatchesFormat()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertStringMatchesFormat()
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

A string de formato pode conter os seguintes substitutos (placeholders):

-

  ``%e``: Representa um separador de diretório, por exemplo ``/`` no Linux.

-

  ``%s``: Um ou mais de qualquer coisa (caractere ou espaço em branco) exceto o caractere de fim de linha.

-

  ``%S``: Zero ou mais de qualquer coisa (caractere ou espaço em branco) exceto o caractere de fim de linha.

-

  ``%a``: Um ou mais de qualquer coisa (caractere ou espaço em branco) incluindo o caractere de fim de linha.

-

  ``%A``: Zero ou mais de qualquer coisa (caractere ou espaço em branco) incluindo o caractere de fim de linha.

-

  ``%w``: Zero ou mais caracteres de espaços em branco.

-

  ``%i``: Um valor inteiro assinado, por exemplo ``+3142``, ``-3142``.

-

  ``%d``: Um valor inteiro não-assinado, por exemplo ``123456``.

-

  ``%x``: Um ou mais caracteres hexadecimais. Isto é, caracteres na classe ``0-9``, ``a-f``, ``A-F``.

-

  ``%f``: Um número de ponto flutuante, por exemplo: ``3.142``, ``-3.142``, ``3.142E-10``, ``3.142e+10``.

-

  ``%c``: Um único caractere de qualquer ordem.

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$string`` não combina com o conteúdo do ``$formatFile``.

``assertStringNotMatchesFormatFile()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertStringMatchesFormatFile()
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se as variáveis ``$expected`` e ``$actual`` não tem o mesmo tipo e valor.

``assertNotSame()`` é o inverso desta asserção e recebe os mesmos argumentos.

``assertAttributeSame()`` e ``assertAttributeNotSame()`` são invólucros convenientes que usam um atributo ``public``, ``protected``, ou ``private`` de uma classe ou objeto como o valor atual.

.. code-block:: php
    :caption: Utilização de assertSame()
    :name: appendixes.assertions.assertSame.example

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se as variáveis ``$expected`` e ``$actual`` não referenciam o mesmo objeto.

.. code-block:: php
    :caption: Utilização de assertSame() com objetos
    :name: appendixes.assertions.assertSame.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$string`` não termina com ``$suffix``.

``assertStringEndsNotWith()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertStringEndsWith()
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEndsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEndsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o arquivo especificado por ``$expectedFile`` não tem ``$actualString`` como seu conteúdo.

``assertStringNotEqualsFile()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertStringEqualsFile()
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEqualsFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEqualsFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$string`` não inicia com ``$prefix``.

``assertStringStartsNotWith()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertStringStartsWith()
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringStartsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringStartsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

Asserções mais complexas podem ser formuladas usando as
classes ``PHPUnit_Framework_Constraint``. Elas podem ser
avaliadas usando o método ``assertThat()``.
:numref:`appendixes.assertions.assertThat.example` mostra como as
restrições ``logicalNot()`` e ``equalTo()``
podem ser usadas para expressar a mesma asserção como
``assertNotEquals()``.

``assertThat(mixed $value, PHPUnit_Framework_Constraint $constraint[, $message = ''])``

Reporta um erro identificado pela ``$message`` se o ``$value`` não combina com a ``$constraint``.

.. code-block:: php
    :caption: Utilização de assertThat()
    :name: appendixes.assertions.assertThat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class BiscuitTest extends TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }
    ?>

:numref:`appendixes.assertions.assertThat.tables.constraints` mostra as
classes ``PHPUnit_Framework_Constraint`` disponíveis.

.. rst-class:: table
.. list-table:: Restrições
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - Restrição
      - Significado
    * - ``PHPUnit_Framework_Constraint_Attribute attribute(PHPUnit_Framework_Constraint $constraint, $attributeName)``
      - Restrição que aplica outra restrição a um atributo de uma classe ou objeto.
    * - ``PHPUnit_Framework_Constraint_IsAnything anything()``
      - Restrição que aceita qualquer valor de entrada.
    * - ``PHPUnit_Framework_Constraint_ArrayHasKey arrayHasKey(mixed $key)``
      - Restrição que assevera que o array foi avaliado a ter uma dada chave.
    * - ``PHPUnit_Framework_Constraint_TraversableContains contains(mixed $value)``
      - Restrição que assevera que o ``array`` ou objeto que implementa a interface ``Iterator`` foi avaliado para conter um dado valor.
    * - ``PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnly(string $type)``
      - Restrição que assevera que o ``array`` ou objeto que implementa a interface ``Iterator`` foi avaliado para conter somente valores de um dado tipo.
    * - ``PHPUnit_Framework_Constraint_TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - Restrição que assevera que o ``array`` ou objeto que implementa a interface ``Iterator`` foi avaliado para conter somente instâncias de um dado nome de classe.
    * - ``PHPUnit_Framework_Constraint_IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - Restrição que verifica se um valor é igual a outro.
    * - ``PHPUnit_Framework_Constraint_Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)``
      - Restrição que verifica se um valor é igual a um atributo de uma classe ou objeto.
    * - ``PHPUnit_Framework_Constraint_DirectoryExists directoryExists()``
      - Restrição que verifica se o diretório que foi avaliado existe.
    * - ``PHPUnit_Framework_Constraint_FileExists fileExists()``
      - Restrição que verifica se o arquivo(nome) que foi avaliado existe.
    * - ``PHPUnit_Framework_Constraint_IsReadable isReadable()``
      - Restrição que verifica se o arquivo(nome) que foi avaliado é legível.
    * - ``PHPUnit_Framework_Constraint_IsWritable isWritable()``
      - Restrição que verifica se o arquivo(nome) que foi avaliado é gravável.
    * - ``PHPUnit_Framework_Constraint_GreaterThan greaterThan(mixed $value)``
      - Restrição que assevera que o valor que foi avaliado é maior que um dado valor.
    * - ``PHPUnit_Framework_Constraint_Or greaterThanOrEqual(mixed $value)``
      - Restrição que assevera que o valor que foi avaliado é maior ou igual a um dado valor.
    * - ``PHPUnit_Framework_Constraint_ClassHasAttribute classHasAttribute(string $attributeName)``
      - Restrição que assevera que a classe que foi avaliada tem um dado atributo.
    * - ``PHPUnit_Framework_Constraint_ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - Restrição que assevera que a classe que foi avaliada tem um dado atributo estático.
    * - ``PHPUnit_Framework_Constraint_ObjectHasAttribute hasAttribute(string $attributeName)``
      - Restrição que assevera que o objeto que foi avaliado tem um dado atributo.
    * - ``PHPUnit_Framework_Constraint_IsIdentical identicalTo(mixed $value)``
      - Restrição que assevera que um valor é idêntico a outro.
    * - ``PHPUnit_Framework_Constraint_IsFalse isFalse()``
      - Restrição que assevera que o valor que foi avaliado é ``false``.
    * - ``PHPUnit_Framework_Constraint_IsInstanceOf isInstanceOf(string $className)``
      - Restrição que assevera que o objeto que foi avaliado é uma instância de uma dada classe.
    * - ``PHPUnit_Framework_Constraint_IsNull isNull()``
      - Restrição que assevera que o valor que foi avaliado é ``null``.
    * - ``PHPUnit_Framework_Constraint_IsTrue isTrue()``
      - Restrição que assevera que o valor que foi avaliado é ``true``.
    * - ``PHPUnit_Framework_Constraint_IsType isType(string $type)``
      - Restrição que assevera que o valor que foi avaliado é de um tipo especificado.
    * - ``PHPUnit_Framework_Constraint_LessThan lessThan(mixed $value)``
      - Restrição que assevera que o valor que foi avaliado é menor que um dado valor.
    * - ``PHPUnit_Framework_Constraint_Or lessThanOrEqual(mixed $value)``
      - Restrição que assevera que o valor que foi avaliado é menor ou igual a um dado valor.
    * - ``logicalAnd()``
      - Lógico AND.
    * - ``logicalNot(PHPUnit_Framework_Constraint $constraint)``
      - Lógico NOT.
    * - ``logicalOr()``
      - Lógico OR.
    * - ``logicalXor()``
      - Lógico XOR.
    * - ``PHPUnit_Framework_Constraint_PCREMatch matchesRegularExpression(string $pattern)``
      - Restrição que assevera que a string que foi avaliada combina com uma expressão regular.
    * - ``PHPUnit_Framework_Constraint_StringContains stringContains(string $string, bool $case)``
      - Restrição que assevera que a string que foi avaliada contém uma dada string.
    * - ``PHPUnit_Framework_Constraint_StringEndsWith stringEndsWith(string $suffix)``
      - Restrição que assevera que a string que foi avaliada termina com um dado sufixo.
    * - ``PHPUnit_Framework_Constraint_StringStartsWith stringStartsWith(string $prefix)``
      - Restrição que assevera que a string que foi avaliada começa com um dado prefixo.

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se a ``$condition`` é ``false``.

``assertNotTrue()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertTrue()
    :name: appendixes.assertions.assertTrue.example

    <?php
    use PHPUnit\Framework\TestCase;

    class TrueTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(false);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit TrueTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o documento XML em ``$actualFile`` não é igual ao documento XML em ``$expectedFile``.

``assertXmlFileNotEqualsXmlFile()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertXmlFileEqualsXmlFile()
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o documento XML em ``$actualXml`` não é igual ao documento XML em ``$expectedFile``.

``assertXmlStringNotEqualsXmlFile()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertXmlStringEqualsXmlFile()
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

Reporta um erro identificado pela ``$message`` se o documento XML em ``$actualXml`` não é igual ao documento XML em ``$expectedXml``.

``assertXmlStringNotEqualsXmlString()`` é o inverso desta asserção e recebe os mesmos argumentos.

.. code-block:: php
    :caption: Utilização de assertXmlStringEqualsXmlString()
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.


