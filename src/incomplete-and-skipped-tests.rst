

.. _incomplete-and-skipped-tests:

============================
Testes Incompletos e Pulados
============================

.. _incomplete-and-skipped-tests.incomplete-tests:

Testes Incompletos
##################

Quando você está trabalhando em uma nova classe de caso de teste, você pode querer começar
a escrever métodos de teste vazios, como:

.. code-block:: php

    public function testSomething()
    {
    }

para manter o controle sobre os testes que você já escreveu. O
problema com os métodos de teste vazios é que eles são interpretados como
bem-sucedidos pelo framework do PHPUnit. Esse erro de interpretação leva à
inutilização dos relatórios de testes -- você não pode ver se um teste foi
realmente bem-sucedido ou simplesmente ainda não foi implementado. Chamar
``$this->fail()`` no método de teste não implementado
não ajuda em nada, já que o teste será interpretado como uma
falha. Isso seria tão errado quanto interpretar um teste não implementado
como bem-sucedido.

Se imaginarmos que um teste bem-sucedido é uma luz verde e um teste mal-sucedido (falho)
é uma luz vermelha, precisaremos de uma luz amarela adicional para marcar um teste
como incompleto ou ainda não implementado.
O ``PHPUnit_Framework_IncompleteTest`` é uma interface
marcadora para marcar uma exceção que surge de um método de teste como
resultado do teste ser incompleto ou atualmente não implementado.
O ``PHPUnit_Framework_IncompleteTestError`` é a
implementação padrão dessa interface.

O :numref:`incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php`
mostra uma classe de caso de teste, ``SampleTest``, que contém um método
de teste, ``testSomething()``. Chamando o conveniente método
``markTestIncomplete()`` (que automaticamente
lançará uma exceção ``PHPUnit_Framework_IncompleteTestError``)
no método de teste, marcamos o teste como sendo incompleto.

.. code-block:: php
    :caption: Marcando um teste como incompleto
    :name: incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SampleTest extends TestCase
    {
        public function testSomething()
        {
            // Opcional: Teste alguma coisa aqui, se quiser.
            $this->assertTrue(true, 'This should already work.');

            // Pare aqui e marque este teste como incompleto.
            $this->markTestIncomplete(
              'This test has not been implemented yet.'
            );
        }
    }
    ?>

Um teste incompleto é denotado por um ``I`` na saída
do executor de testes em linha-de-comando do PHPUnit, como mostrado no exemplo
abaixo:

.. code-block:: bash

    $ phpunit --verbose SampleTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    This test has not been implemented yet.

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

A :numref:`incomplete-and-skipped-tests.incomplete-tests.tables.api`
mostra a API para marcar testes como incompletos.

.. rst-class:: table
.. list-table:: API para Testes Incompletos
    :name: incomplete-and-skipped-tests.incomplete-tests.tables.api
    :header-rows: 1

    * - Método
      - Significado
    * - ``void markTestIncomplete()``
      - Marca o teste atual como incompleto.
    * - ``void markTestIncomplete(string $message)``
      - Marca o teste atual como incompleto usando ``$message`` como uma mensagem explanatória.

.. _incomplete-and-skipped-tests.skipping-tests:

Pulando Testes
##############

Nem todos os testes podem ser executados em qualquer ambiente. Considere, por exemplo,
uma camada de abstração de banco de dados contendo vários drivers para os diversos
sistemas de banco de dados que suporta. Os testes para o driver MySQL podem ser
executados apenas, é claro, se um servidor MySQL estiver disponível.

O :numref:`incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php`
mostra uma classe de caso de teste, ``DatabaseTest``, que contém um método
de teste, ``testConnection()``. No método-modelo ``setUp()``
da classe de caso de teste, verificamos se a extensão MySQLi
está disponível e usamos o método ``markTestSkipped()``
para pular o teste caso contrário.

.. code-block:: php
    :caption: Pulando um teste
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected function setUp()
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'The MySQLi extension is not available.'
                );
            }
        }

        public function testConnection()
        {
            // ...
        }
    }
    ?>

Um teste que tenha sido pulado é denotado por um ``S`` na
saída do executor de testes em linha-de-comando do PHPUnit, como mostrado
no seguinte exemplo:

.. code-block:: bash

    $ phpunit --verbose DatabaseTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    The MySQLi extension is not available.

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

:numref:`incomplete-and-skipped-tests.skipped-tests.tables.api`
mostra a API para pular testes.

.. rst-class:: table
.. list-table:: API para Pular Testes
    :name: incomplete-and-skipped-tests.skipped-tests.tables.api
    :header-rows: 1

    * - Método
      - Significado
    * - ``void markTestSkipped()``
      - Marca o teste atual como pulado.
    * - ``void markTestSkipped(string $message)``
      - Marca o teste atual como pulado usando ``$message`` como uma mensagem explanatória.

.. _incomplete-and-skipped-tests.skipping-tests-using-requires:

Pulando Testes usando @requires
###############################

Além do método acima também é possível usar a
anotação ``@requires`` para expressar pré-condições comuns para um caso de teste.

.. rst-class:: table
.. list-table:: Possíveis usos para @requires
    :name: incomplete-and-skipped-tests.requires.tables.api
    :header-rows: 1

    * - Tipo
      - Valores Possíveis
      - Exemplos
      - Outro exemplo
    * - ``PHP``
      - Qualquer identificador de versão do PHP
      - @requires PHP 5.3.3
      - @requires PHP 7.1-dev
    * - ``PHPUnit``
      - Qualquer identificador de versão do PHPUnit
      - @requires PHPUnit 3.6.3
      - @requires PHPUnit 4.6
    * - ``OS``
      - Uma expressão regular que combine `PHP_OS <http://php.net/manual/en/reserved.constants.php#constant.php-os>`_
      - @requires OS Linux
      - @requires OS WIN32|WINNT
    * - ``função``
      - Qualquer parâmetro válido para `function_exists <http://php.net/function_exists>`_
      - @requires function imap_open
      - @requires function ReflectionMethod::setAccessible
    * - ``extensão``
      - Qualquer nome de extensão junto com um opcional identificador de versão
      - @requires extension mysqli
      - @requires extension redis 2.2.0

.. code-block:: php
    :caption: Pulando casos de teste usando @requires
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseClassSkippingTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @requires extension mysqli
     */
    class DatabaseTest extends TestCase
    {
        /**
         * @requires PHP 5.3
         */
        public function testConnection()
        {
            // O Teste requer as extensões mysqli e PHP >= 5.3
        }

        // ... Todos os outros testes requerem a extensão mysqli
    }
    ?>

Se você está usando uma sintaxe que não compila com uma certa versão do PHP, procure dentro da configuração
xml por inclusões dependentes de versão na :ref:`appendixes.configuration.testsuites`.


