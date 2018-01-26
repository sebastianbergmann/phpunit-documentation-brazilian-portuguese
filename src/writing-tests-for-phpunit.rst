

.. _writing-tests-for-phpunit:

================================
Escrevendo Testes para o PHPUnit
================================

:numref:`writing-tests-for-phpunit.examples.StackTest.php` mostra
como podemos escrever testes usando o PHPUnit que exercita operações de vetor do PHP.
O exemplo introduz as convenções básicas e passos para escrever testes
com o PHPUnit:

#.

   Os testes para uma classe ``Class`` vão dentro de uma classe ``ClassTest``.

#.

   ``ClassTest`` herda (na maioria das vezes) de ``PHPUnit\Framework\TestCase``.

#.

   Os testes são métodos públicos que são nomeados ``test*``.

   Alternativamente, você pode usar a anotação ``@test`` em um bloco de documentação de um método para marcá-lo como um método de teste.

#.

   Dentro dos métodos de teste, métodos de asserção tal como ``assertEquals()`` (veja :ref:`appendixes.assertions`) são usados para asseverar que um valor real equivale a um valor esperado.

.. code-block:: php
    :caption: Testando operações de vetores com o PHPUnit
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testPushAndPop()
        {
            $stack = [];
            $this->assertEquals(0, count($stack));

            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertEquals(1, count($stack));

            $this->assertEquals('foo', array_pop($stack));
            $this->assertEquals(0, count($stack));
        }
    }
    ?>
|
    *Martin Fowler*:

    Sempre que você estiver tentado a escrever algo em uma
    declaração ``print`` ou uma expressão depuradora, escreva-a
    como um teste em vez disso.

.. _writing-tests-for-phpunit.test-dependencies:

Dependências de Testes
######################

    *Adrian Kuhn et. al.*:

    Testes Unitários são primeiramente escritos como uma boa prática para ajudar desenvolvedores
    a identificar e corrigir defeitos, refatorar o código e servir como documentação
    para uma unidade de programa sob teste. Para conseguir esses benefícios, testes unitários
    idealmente deveriam cobrir todos os caminhos possíveis em um programa. Um teste unitário
    geralmente cobre um caminho específico em uma função ou método. Porém um
    método de teste não é necessariamente uma entidade encapsulada e independente. Frequentemente
    existem dependências implícitas entre métodos de teste, escondidas no
    cenário de implementação de um teste.

O PHPUnit suporta a declaração de dependências explícitas entre métodos de
teste. Tais dependências não definem a ordem em que os métodos de teste
devem ser executados, mas permitem o retorno de uma instância do
ambiente do teste por um produtor e a passagem dele para os consumidores dependentes.

-

  Um produtor é um método de teste que produz a sua unidade sob teste como valor de retorno.

-

  Um consumidor é um método de teste que depende de um ou mais produtores e seus valores retornados.

:numref:`writing-tests-for-phpunit.examples.StackTest2.php` mostra
como usar a anotação ``@depends`` para expressar
dependências entre métodos de teste.

.. code-block:: php
    :caption: Usando a anotação ``@depends`` para expressar dependências
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testEmpty()
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack)
        {
            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack)
        {
            $this->assertEquals('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }
    ?>

No exemplo acima, o primeiro teste, ``testEmpty()``,
cria um novo vetor e assegura que o mesmo é vazio. O teste então retorna
o ambiente como resultado. O segundo teste, ``testPush()``,
depende de ``testEmpty()`` e lhe é passado o resultado do qual
ele depende como um argumento. Finalmente, ``testPop()``
depende de ``testPush()``.

.. admonition:: Note

   O valor de retorno produzido por um produtor é passado "como está" para seus
   consumidores por padrão. Isso significa que quando um produtor retorna um objeto,
   uma referência para esse objeto é passada para os consumidores. Quando uma cópia
   deve ser usada ao invés de uma referência, então @depends clone
   deve ser usado ao invés de @depends.

Para localizar defeitos rapidamente, queremos nossa atenção focada nas
falhas relevantes dos testes. É por isso que o PHPUnit pula a execução de um teste
quando um teste do qual ele depende falha. Isso melhora a localização de defeitos por
explorar as dependências entre os testes como mostrado em
:numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`.

.. code-block:: php
    :caption: Explorando as dependências entre os testes
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyFailureTest extends TestCase
    {
        public function testOne()
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyFailureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

Um teste pode ter mais de uma anotação ``@depends``.
O PHPUnit não muda a ordem em que os testes são executados, portanto você deve
se certificar de que as dependências de um teste podem realmente ser encontradas antes de
executar o teste.

Um teste que tem mais de uma anotação ``@depends``
vai obter um ambiente a partir do primeiro produtor como o primeiro argumento, um ambiente
a partir do segundo produtor como o segundo argumento, e assim por diante.
Veja :numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`

.. code-block:: php
    :caption: Teste com múltiplas dependências
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php
    use PHPUnit\Framework\TestCase;

    class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 3 assertions)

.. _writing-tests-for-phpunit.data-providers:

Provedores de Dados
###################

Um método de teste pode aceitar argumentos arbitrários. Esses argumentos devem ser
fornecidos por um método provedor de dados (``additionProvider()`` em
:numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
O método provedor de dados a ser usado é especificado usando a
anotação ``@dataProvider``.

Um método provedor de dados deve ser ``public`` e retornar
um vetor de vetores ou um objeto que implemente a interface ``Iterator``
e produza um vetor para cada passo da iteração. Para cada vetor que
é parte da coleção o método de teste será chamado com os conteúdos
do vetor como seus argumentos.

.. code-block:: php
    :caption: Usando um provedor de dados que retorna um vetor de vetores
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

Quando usar um número grande de conjuntos de dados é útil nomear cada um com uma chave string ao invés do padrão numérico.
Output will be more verbose as it'll contain that name of a dataset that breaks a test.

.. code-block:: php
    :caption: Usando um provedor de dados com conjuntos de dados nomeados
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Usando um provedor de dados que retorna um objeto Iterador
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    require 'CsvFileIterator.php';

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return new CsvFileIterator('data.csv');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 matches expected '3'.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: A classe CsvFileIterator
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php
    use PHPUnit\Framework\TestCase;

    class CsvFileIterator implements Iterator {
        protected $file;
        protected $key = 0;
        protected $current;

        public function __construct($file) {
            $this->file = fopen($file, 'r');
        }

        public function __destruct() {
            fclose($this->file);
        }

        public function rewind() {
            rewind($this->file);
            $this->current = fgetcsv($this->file);
            $this->key = 0;
        }

        public function valid() {
            return !feof($this->file);
        }

        public function key() {
            return $this->key;
        }

        public function current() {
            return $this->current;
        }

        public function next() {
            $this->current = fgetcsv($this->file);
            $this->key++;
        }
    }
    ?>

Quando um teste recebe uma entrada tanto de um método ``@dataProvider``
quanto de um ou mais testes dos quais ele ``@depends``, os
argumentos do provedor de dados virão antes daqueles dos quais ele
é dependente. Os argumentos dos quais o teste depende serão os
mesmos para cada conjunto de dados.
Veja :numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`

.. code-block:: php
    :caption: Combinação de @depends e @dataProvider no mesmo teste
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider()
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
    Array (
    -    0 => 'provider1'
    +    0 => 'provider2'
    1 => 'first'
    2 => 'second'
    )

    /home/sb/DependencyAndDataProviderComboTest.php:31

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. admonition:: Note

   Quando um teste depende de um teste que usa provedores de dados, o teste dependente
   será executado quando o teste do qual ele depende for bem sucedido em pelo
   menos um conjunto de dados. O resultado de um teste que usa provedores de dados não pode
   ser injetado dentro de um teste dependente.

.. admonition:: Note

   Todos provedores de dados são executados antes da chamada ao método estático ``setUpBeforeClass``
   e a primeira chamada ao método ``setUp``.
   Por isso você não pode acessar quaisquer variáveis que criar
   ali de dentro de um provedor de dados. Isto é necessário para que o PHPUnit seja capaz
   de calcular o número total de testes.

.. _writing-tests-for-phpunit.exceptions:

Testando Exceções
#################

:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
mostra como usar a anotação ``expectException()`` para
testar se uma exceção é lançada dentro do código de teste.

.. code-block:: php
    :caption: Usando o método expectException()
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        public function testException()
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Além do método ``expectException()`` os métodos
``expectExceptionCode()``,
``expectExceptionMessage()``, e
``expectExceptionMessageRegExp()`` existem para configurar
expectativas de exceções lançadas pelo código sob teste.

Alternativamente, você pode usar as anotações ``@expectedException``,
``@expectedExceptionCode``,
``@expectedExceptionMessage``, e
``@expectedExceptionMessageRegExp`` para configurar
expectativas de exceções lançadas pelo código sob teste.
:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php`
mostra um exemplo.

.. code-block:: php
    :caption: Usando a anotação @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        /**
         * @expectedException InvalidArgumentException
         */
        public function testException()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.errors:

Testando Erros PHP
##################

Por padrão, o PHPUnit converte os erros, avisos e notificações do PHP que são
disparados durante a execução de um teste para uma exceção. Usando essas
exceções, você pode, por exemplo, esperar que um teste dispare um erro PHP como
mostrado no :numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`.

.. admonition:: Note

   A configuração em tempo de execução ``error_reporting`` do PHP pode
   limitar quais erros o PHPUnit irá converter para exceções. Se você
   está tendo problemas com essa funcionalidade, certifique-se que o PHP não está configurado para
   suprimir os tipos de erros que você esta testando.

.. code-block:: php
    :caption: Esperando um erro PHP usando @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExpectedErrorTest extends TestCase
    {
        /**
         * @expectedException PHPUnit\Framework\Error
         */
        public function testFailingInclude()
        {
            include 'not_existing_file.php';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit -d error_reporting=2 ExpectedErrorTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 0 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

``PHPUnit\Framework\Error\Notice`` e
``PHPUnit\Framework\Error\Warning`` representam notificações
e avisos do PHP, respectivamente.

.. admonition:: Note

   Você deve ser o mais específico possível quando testar exceções. Testar
   por classes que são muito genéricas pode causar efeitos colaterais
   indesejáveis. Da mesma forma, testar para a classe ``Exception``
   com ``@expectedException`` ou
   ``setExpectedException()`` não é mais permitido.

Ao testar algo que dependa de funções php que disparam erros como
``fopen`` pode ser útil algumas vezes usar a supressão de
erros enquanto testa. Isso permite a você verificar os valores retornados por
suprimir notificações que levariam a uma
``PHPUnit\Framework\Error\Notice`` phpunit.

.. code-block:: php
    :caption: Testando valores de retorno de código que utiliza PHP Errors
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting() {
            $writer = new FileWriter;
            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }
    class FileWriter
    {
        public function write($file, $content) {
            $file = fopen($file, 'w');
            if($file == false) {
                return false;
            }
            // ...
        }
    }

    ?>

.. code-block:: bash

    $ phpunit ErrorSuppressionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

Sem a supressão de erros o teste teria relatado uma falha
``fopen(/is-not-writeable/file): failed to open stream:
    No such file or directory``.

.. _writing-tests-for-phpunit.output:

Testando Saídas
###############

Às vezes você quer assegurar que a execução de um método, por
exemplo, gere uma saída esperada (via ``echo`` ou
``print``, por exemplo). A
classe ``PHPUnit\Framework\TestCase`` usa a funcionalidade
`Output
Buffering <http://www.php.net/manual/en/ref.outcontrol.php>`_ do PHP para fornecer a funcionalidade que é
necessária para isso.

:numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`
mostra como usar o método ``expectOutputString()`` para
definir a saída esperada. Se essa saída esperada não for gerada, o
teste será contado como uma falha.

.. code-block:: php
    :caption: Testando a saída de uma função ou método
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo()
        {
            $this->expectOutputString('foo');
            print 'foo';
        }

        public function testExpectBarActualBaz()
        {
            $this->expectOutputString('bar');
            print 'baz';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit OutputTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

:numref:`writing-tests-for-phpunit.output.tables.api`
mostra os métodos fornecidos para testar saídas.

.. rst-class:: table
.. list-table:: Métodos para testar a saída
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - Método
      - Significado
    * - ``void expectOutputRegex(string $regularExpression)``
      - Configura a expectativa de que a saída combine com uma ``$regularExpression``.
    * - ``void expectOutputString(string $expectedString)``
      - Configura a expectativa de que a saída é igual a uma ``$expectedString``.
    * - ``bool setOutputCallback(callable $callback)``
      - Define um callback que é usado, por exemplo, para normalizar a saída real.
    * - ``string getActualOutput()``
      - Recupera a saída real.

.. admonition:: Note

   Um teste que emite saída irá falhar no modo estrito.

.. _writing-tests-for-phpunit.error-output:

Saída de Erro
#############

Sempre que um teste falha o PHPUnit faz o melhor para fornecer a você o
máximo possível de conteúdo que possa ajudar a identificar o problema.

.. code-block:: php
    :caption: Saída de erro gerada quando uma comparação de vetores falha
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Neste exemplo apenas um dos valores dos vetores diferem e os outros valores
são exibidos para fornecer o contexto onde o erro ocorreu.

Quando a saída gerada for longa demais para ler o PHPUnit vai quebrá-la
e fornecer algumas linhas de contexto ao redor de cada diferença.

.. code-block:: php
    :caption: Saída de erro quando uma comparação de um vetor longo falha
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class LongArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LongArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

Casos Extremos
==============

Quando uma comparação falha o PHPUnit cria uma representação textual da
entrada de valores e as compara. Devido a essa implementação uma diferenciação
pode mostrar mais problemas do que realmente existem.

Isso só acontece quando se usa assertEquals ou outra função de comparação 'fraca'
em vetores ou objetos.

.. code-block:: php
    :caption: Caso extremo na geração de diferenciação quando se usa uma comparação fraca
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayWeakComparisonTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Neste exemplo a diferença no primeiro índice entre
``1`` e ``'1'`` é
relatada ainda que o assertEquals considere os valores como uma combinação.


