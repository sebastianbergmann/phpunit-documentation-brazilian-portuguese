

.. _appendixes.configuration:

=============================
O Arquivo de Configuração XML
=============================

.. _appendixes.configuration.phpunit:

PHPUnit
#######

Os atributos do elemento ``<phpunit>`` podem
ser usados para configurar a funcionalidade principal do PHPUnit.

.. code-block:: bash

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/6.3/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             mapTestClassNameToCoveredClassName="false"
             printerClass="PHPUnit_TextUI_ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit_Runner_StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

A configuração XML acima corresponde ao comportamento padrão do
executor de teste TextUI documentado na :ref:`textui.clioptions`.

Opções adicionais que não estão disponíveis como opções em linha-de-comando são:

``convertErrorsToExceptions``

    Por padrão, PHPUnit irá instalar um manipulador de erro que converte
    os seguintes erros para exceções:

    - ``E_WARNING``

    - ``E_NOTICE``

    - ``E_USER_ERROR``

    - ``E_USER_WARNING``

    - ``E_USER_NOTICE``

    - ``E_STRICT``

    - ``E_RECOVERABLE_ERROR``

    - ``E_DEPRECATED``

    - ``E_USER_DEPRECATED``

    Definir ``convertErrorsToExceptions`` para
    ``false`` desabilita esta funcionalidade.

``convertNoticesToExceptions``

    Quando definido para ``false``, o manipulador de erro instalado
    pelo ``convertErrorsToExceptions`` não irá converter os erros
    ``E_NOTICE``, ``E_USER_NOTICE``, ou
    ``E_STRICT`` para exceções.

``convertWarningsToExceptions``

    Quando definido para ``false``, o manipulador de erro instalado
    pelo ``convertErrorsToExceptions`` não irá converter os erros
    ``E_WARNING`` ou ``E_USER_WARNING``
    para exceções.

``forceCoversAnnotation``

    A Cobertura de Código só será registrada para testes que usem a anotação
    ``@covers`` documentada na
    :ref:`appendixes.annotations.covers`.

``timeoutForLargeTests``

    Se o limite de tempo baseado no tamanho do teste são forçados então esse atributo
    define o tempo limite para todos os testes marcados como ``@large``.
    Se um teste não completa dentro do tempo limite configurado, irá
    falhar.

``timeoutForMediumTests``

    Se o limite de tempo baseado no tamanho do teste são forçados então esse atributo
    define o tempo limite para todos os testes marcados como ``@medium``.
    Se um teste não completa dentro do tempo limite configurado, irá
    falhar.

``timeoutForSmallTests``

    Se o limite de tempo baseado no tamanho do teste são forçados então esse atributo
    define o tempo limite para todos os testes marcados como
    ``@medium`` ou ``@large``. Se um teste
    não completa dentro do tempo limite configurado, irá falhar.

.. _appendixes.configuration.testsuites:

Suítes de Teste
###############

O elemento ``<testsuites>`` e seu(s)
um ou mais filhos ``<testsuite>`` podem ser
usados para compor uma suíte de teste fora das suítes e casos de teste.

.. code-block:: bash

    <testsuites>
      <testsuite name="My Test Suite">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

Usando os atributos ``phpVersion`` e
``phpVersionOperator``, uma versão exigida do PHP
pode ser especificada. O exemplo abaixo só vai adicionar os
arquivos :file:`/path/to/\*Test.php` e
:file:`/path/to/MyTest.php` se a versão do PHP
for no mínimo 5.3.0.

.. code-block:: bash

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

O atributo ``phpVersionOperator`` é opcional e
padronizado para ``>=``.

.. _appendixes.configuration.groups:

Grupos
######

O elemento ``<groups>`` e seus filhos
``<include>``,
``<exclude>``, e
``<group>`` podem ser usados para selecionar
grupos de testes marcados com a anotação ``@group``
(documentada na :ref:`appendixes.annotations.group`)
que (não) deveriam ser executados.

.. code-block:: bash

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

A configuração XML acima corresponde a invocar o executor de testes TextUI
com as seguintes opções:

-

  ``--group name``

-

  ``--exclude-group name``

.. _appendixes.configuration.whitelisting-files:

Lista-branca de Arquivos para Cobertura de Código
#################################################

O elemento ``<filter>`` e seus filhos podem
ser usados para configurar a lista-branca para o relatório de cobertura de código.

.. code-block:: bash

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

.. _appendixes.configuration.logging:

Registrando
###########

O elemento ``<logging>`` e seus filhos
``<log>`` podem ser usados para configurar o
registro da execução de teste.

.. code-block:: bash

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="junit" target="/tmp/logfile.xml" logIncompleteSkipped="false"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

A configuração XML acima corresponde a invocar o executor de teste TextUI
com as seguintes opções:

-

  ``--coverage-html /tmp/report``

-

  ``--coverage-clover /tmp/coverage.xml``

-

  ``--coverage-php /tmp/coverage.serialized``

-

  ``--coverage-text``

-

  ``> /tmp/logfile.txt``

-

  ``--log-junit /tmp/logfile.xml``

-

  ``--testdox-html /tmp/testdox.html``

-

  ``--testdox-text /tmp/testdox.txt``

Os atributos ``lowUpperBound``, ``highLowerBound``,
``logIncompleteSkipped`` e
``showUncoveredFiles`` não possuem opção de
execução de teste TextUI equivalente.

-

  ``lowUpperBound``: Porcentagem máxima de cobertura para ser considerado "moderadamente" coberto.

-

  ``highLowerBound``: Porcentagem mínima de cobertura para ser considerado "altamente" coberto.

-

  ``showUncoveredFiles``: Mostra todos arquivos da lista-branca na saída ``--coverage-text`` não apenas aqueles com informação de cobertura.

-

  ``showOnlySummary``: Mostra somente o resumo na saída ``--coverage-text``.

.. _appendixes.configuration.test-listeners:

Ouvintes de Teste
#################

O elemento ``<listeners>`` e seus filhos
``<listener>`` podem ser usados para anexar
ouvintes adicionais de teste para a execução dos testes.

.. code-block:: bash

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

A configuração XML acima corresponde a anexar o objeto
``$listener`` (veja abaixo) à execução de teste:

.. code-block:: bash

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. _appendixes.configuration.php-ini-constants-variables:

Definindo configurações PHP INI, Constantes e Variáveis Globais
###############################################################

O elemento ``<php>`` e seus filhos podem ser
usados para definir configurações do PHP, constantes e variáveis globais. Também pode
ser usado para preceder o ``include_path``.

.. code-block:: bash

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

A configuração XML acima corresponde ao seguinte código PHP:

.. code-block:: bash

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';


