

.. _organizing-tests:

==================
Organizando Testes
==================

Um dos objetivos do PHPUnit é que os testes
devem ser combináveis: queremos ser capazes de executar qualquer quantidade ou combinação
de testes juntos, por exemplo todos os testes para um projeto inteiro, ou os
testes para todas as classes de um ambiente que é parte do projeto, ou apenas
os testes para uma única classe.

O PHPUnit suporta diferentes formas de organizar testes e combiná-los em uma
suíte de testes. Este capítulo mostra as abordagens mais comuns.

.. _organizing-tests.filesystem:

Compondo uma Suíte de Testes usando o Sistema de Arquivos
#########################################################

Provavelmente o jeito mais fácil de compor uma suíte de testes é manter todos
os arquivos-fonte dos casos de teste em um diretório de testes. O PHPUnit pode descobrir
automaticamente e executar os testes atravessando recursivamente o diretório de testes.

Vamos dar uma olhada na suíte de testes da
biblioteca `sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_.
Observando a estrutura de diretórios desse projeto, podemos ver que as
classes dos casos de teste no diretório :file:`tests` espelha o
pacote e estrutura de classes do Sistema Sob Teste (SST – ou SUT: System Under Teste) no diretório
:file:`src`:

.. code-block:: bash

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

Para executar todos os testes para a biblioteca precisamos apenas apontar o
executor de testes em linha-de-comando do PHPUnit para o diretório de teste:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Note

   Se você apontar o executor de testes em linha-de-comando do PHPUnit para um diretório,
   ele irá procurar por arquivos :file:`*Test.php`.

Para executar apenas os testes declarados na classe de casos de teste ``CurrencyTest``
em :file:`tests/CurrencyTest.php`, podemos usar
o seguinte comando:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

Para um controle mais refinado sobre quais testes executar, podemos usar a opção
``--filter``:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Note

   Uma desvantagem dessa abordagem é que não temos controle sobre a ordem em
   que os testes são executados. Isso pode causar problemas com relação às
   dependências dos testes, veja :ref:`writing-tests-for-phpunit.test-dependencies`.
   Na próxima seção você vai ver como pode tornar explícita a ordem de execução
   de testes usando o arquivo de configuração XML.

.. _organizing-tests.xml-configuration:

Compondo uma Suíte de Testes Usando Configuração XML
####################################################

O arquivo de configuração XML do PHPUnit (:ref:`appendixes.configuration`)
também pode ser usado para compor uma suíte de testes.
:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`
mostra um arquivo mínimo :file:`phpunit.xml` que adicionará todas
as classes ``*Test`` que forem encontradas em
arquivos :file:`*Test.php` quando o diretório :file:`tests`
é atravessado recursivamente.

.. code-block:: php
    :caption: Compondo uma Suíte de Testes Usando Configuração XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

Se o arquivo :file:`phpunit.xml` ou
:file:`phpunit.xml.dist` (nessa ordem) existir no
diretório de trabalho atual e ``--configuration``
*não* é usada, a configuração será automaticamente
lida desse arquivo.

A ordem em que os testes são executados pode ser explicitada:

.. code-block:: php
    :caption: Compondo uma Suíte de Testes Usando Configuração XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>


