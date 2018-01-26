

.. _textui:

========================================
O executor de testes em linha-de-comando
========================================

O executor de testes em linha-de-comando do PHPUnit pode ser invocado através do comando
:file:`phpunit`. O código seguinte mostra como executar
testes com o executor de testes em linha-de-comando do PHPUnit:

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

Quando invocado como mostrado acima, o executor de testes em linha-de-comando do PHPUnit irá procurar
por um arquivo-fonte :file:`ArrayTest.php` no diretório de trabalho atual,
carregá-lo, e espera encontrar uma classe de caso de teste ``ArrayTest``.
Irá então executar os testes desta classe.

Para cada teste executado, a ferramenta de linha-de-comando do PHPUnit imprime um caractere para
indicar o progresso:

``.``

    Impresso quando um teste é bem sucedido.

``F``

    Impresso quando uma asserção falha enquanto o método de teste executa.

``E``

    Impresso quando um erro ocorre enquanto o método de teste executa.

``R``

    Impresso quando o teste foi marcado como arriscado (veja
    :ref:`risky-tests`).

``S``

    Impresso quando o teste é pulado (veja
    :ref:`incomplete-and-skipped-tests`).

``I``

    Impresso quando o teste é marcado como incompleto ou ainda não
    implementado (veja :ref:`incomplete-and-skipped-tests`).

O PHPUnit distingue entre *falhas* e
*erros*. Uma falha é uma asserção do PHPUnit violada
assim como uma chamada falha ao ``assertEquals()``.
Um erro é uma exceção inesperada ou um erro do PHP. Às vezes
essa distinção se mostra útil já que erros tendem a ser mais fáceis de consertar
do que falhas. Se você tiver uma grande lista de problemas, é melhor
enfrentar os erros primeiro e ver se quaisquer falhas continuam depois de
todos consertados.

.. _textui.clioptions:

Opções de linha-de-comando
##########################

Vamos dar uma olhada nas opções do executor de testes em linha-de-comando
no código seguinte:

.. code-block:: bash

    $ phpunit --help
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format.
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
      --coverage-html <dir>       Generate code coverage report in HTML format.
      --coverage-php <file>       Export PHP_CodeCoverage object to file.
      --coverage-text=<file>      Generate code coverage report in text format.
                                  Default: Standard output.
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
      --whitelist <dir>           Whitelist <dir> for code coverage analysis.
      --disable-coverage-ignore   Disable annotations for ignoring code coverage.

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file.
      --log-teamcity <file>       Log test execution in TeamCity format to file.
      --testdox-html <file>       Write agile documentation in HTML format to file.
      --testdox-text <file>       Write agile documentation in Text format to file.
      --testdox-xml <file>        Write agile documentation in XML format to file.
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run.
      --testsuite <name,...>      Filter which testsuite to run.
      --group ...                 Only runs tests from the specified group(s).
      --exclude-group ...         Exclude tests from the specified group(s).
      --list-groups               List available test groups.
      --list-suites               List available test suites.
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything.
      --strict-coverage           Be strict about @covers annotation usage.
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests.
      --disallow-resource-usage   Be strict about resource usage during small tests.
      --enforce-time-limit        Enforce time limit based on test size.
      --disallow-todo-tests       Disallow @todo-annotated tests.

      --process-isolation         Run each test in a separate PHP process.
      --globals-backup            Backup and restore $GLOBALS for each test.
      --static-backup             Backup and restore static attributes for each test.

      --colors=<flag>             Use colors in output ("never", "auto" or "always").
      --columns <n>               Number of columns to use for progress output.
      --columns max               Use maximum number of columns for progress output.
      --stderr                    Write to STDERR instead of STDOUT.
      --stop-on-error             Stop execution upon first error.
      --stop-on-failure           Stop execution upon first error or failure.
      --stop-on-warning           Stop execution upon first warning.
      --stop-on-risky             Stop execution upon first risky test.
      --stop-on-skipped           Stop execution upon first skipped test.
      --stop-on-incomplete        Stop execution upon first incomplete test.
      --fail-on-warning           Treat tests with warnings as failures.
      --fail-on-risky             Treat risky tests as failures.
      -v|--verbose                Output more verbose information.
      --debug                     Display debugging information.

      --loader <loader>           TestSuiteLoader implementation to use.
      --repeat <times>            Runs the test(s) repeatedly.
      --teamcity                  Report test execution progress in TeamCity format.
      --testdox                   Report test execution progress in TestDox format.
      --testdox-group             Only include tests from the specified group(s).
      --testdox-exclude-group     Exclude tests from the specified group(s).
      --printer <printer>         TestListener implementation to use.

    Configuration Options:

      --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
      -c|--configuration <file>   Read configuration from XML file.
      --no-configuration          Ignore default configuration file (phpunit.xml).
      --no-coverage               Ignore code coverage configuration.
      --no-extensions             Do not load PHPUnit extensions.
      --include-path <path(s)>    Prepend PHP's include_path with given path(s).
      -d key[=value]              Sets a php.ini value.
      --generate-configuration    Generate configuration file with suggested settings.

    Miscellaneous Options:

      -h|--help                   Prints this usage information.
      --version                   Prints the version and exits.
      --atleast-version <min>     Checks that version is greater than min and exits.

``phpunit UnitTest``

    Executa os testes que são fornecidos pela classe
    ``UnitTest``. Espera-se que essa classe seja declarada
    no arquivo-fonte :file:`UnitTest.php`.

    ``UnitTest`` deve ser ou uma classe que herda
    de ``PHPUnit\Framework\TestCase`` ou uma classe que
    fornece um método ``public static suite()`` que
    retorna um objeto ``PHPUnit_Framework_Test``,
    por exemplo uma instância da classe
    ``PHPUnit_Framework_TestSuite``.

``phpunit UnitTest UnitTest.php``

    Executa os testes que são fornecidos pela classe
    ``UnitTest``. Espera-se que esta classe seja declarada
    no arquivo-fonte especificado.

``--coverage-clover``

    Gera um arquivo de registro no formato XML com as informações da cobertura de código
    para a execução dos testes. Veja :ref:`logging` para mais detlahes.

    Por favor, note que essa funcionalidade está disponível somente quando as
    extensões tokenizer e Xdebug estão instaladas.

``--coverage-crap4j``

    Gera um relatório de cobertura de código no formato Crap4j. Veja
    :ref:`code-coverage-analysis` para mais detalhes.

    Por favor, note que essa funcionalidade está disponível somente quando as
    extensões tokenizer e Xdebug estão instaladas.

``--coverage-html``

    Gera um relatório no formato HTML. Veja
    :ref:`code-coverage-analysis` para mais detalhes.

    Por favor, note que essa funcionalidade está disponível somente quando as
    extensões tokenizer e Xdebug estão instaladas.

``--coverage-php``

    Gera um objeto PHP_CodeCoverage serializado com as
    informações de cobertura de código.

    Por favor, note que essa funcionalidade está disponível somente quando as
    extensões tokenizer e Xdebug estão instaladas.

``--coverage-text``

    Gera um arquivo de registro ou saída de linha de comando no formato legível por humanos
    com a informação de cobertura de código para a execução dos testes.
    Veja :ref:`logging` para mais detalhes.

    Por favor, note que essa funcionalidade está disponível somente quando as
    extensões tokenizer e Xdebug estão instaladas.

``--log-junit``

    Gera um arquivo de registro no formato XML Junit para a execução dos testes.
    Veja :ref:`logging` para mais detalhes.

``--testdox-html`` e ``--testdox-text``

    Gera documentação ágil no formato HTML ou texto plano para os
    testes que são executados. Veja :ref:`other-uses-for-tests` para
    mais detalhes.

``--filter``

    Apenas executa os testes cujos nomes combinam com o padrão
    fornecido. Se o padrão não for colocado entre delimitadores, o PHPUnit
    irá colocar o padrão no delimitador ``/``.

    Os nomes de teste para combinar estará em um dos seguintes formatos:

    ``TestNamespace\TestCaseClass::testMethod``

        O formato do nome de teste padrão é o equivalente ao usar
        a constante mágica ``__METHOD__`` dentro
        do método de teste.

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        Quando um teste tem um provedor de dados, cada iteração dos
        dados obtém o índice atual acrescido ao final do
        nome do teste padrão.

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        Quando um teste tem um provedor de dados que usa conjuntos nomeados, cada
        iteração dos dados obtém o nome atual acrescido ao
        final do nome do teste padrão. Veja
        :numref:`textui.examples.TestCaseClass.php` para um
        exemplo de conjunto de dados nomeados.

        .. code-block:: php
            :caption: Conjunto de dados nomeados
            :name: textui.examples.TestCaseClass.php

            <?php
            use PHPUnit\Framework\TestCase;

            namespace TestNamespace;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }
            ?>

    ``/path/to/my/test.phpt``

        O nome do teste para um teste PHPT é o caminho do sistema de arquivos.

    Veja :numref:`textui.examples.filter-patterns` para exemplos
    de padrões de filtros válidos.

    .. code-block:: php
        :caption: Exmplos de padrão de filtro
        :name: textui.examples.filter-patterns

    Veja :numref:`textui.examples.filter-shortcuts` para alguns
    atalhos adicionais que estão disponíveis para combinar provedores
    de dados

    .. code-block:: php
        :caption: Atalhos de filtro
        :name: textui.examples.filter-shortcuts

``--testsuite``

    Só roda o conjunto de teste cujo nome combina com o padrão dado.

``--group``

    Apenas executa os testes do(s) grupo(s) especificado(s). Um teste pode ser marcado como
    pertencente a um grupo usando a anotação ``@group``.

    A anotação ``@author`` é um apelido para
    ``@group``, permitindo filtrar os testes com base em seus
    autores.

``--exclude-group``

    Exclui testes do(s) grupo(s) especificado(s). Um teste pode ser marcado como
    pertencente a um grupo usando a anotação ``@group``.

``--list-groups``

    Lista os grupos de teste disponíveis.

``--test-suffix``

    Só procura por arquivos de teste com o sufixo(s) especificado..

``--report-useless-tests``

    Seja estrito sobre testes que não testam nada. Veja :ref:`risky-tests` para detalhes.

``--strict-coverage``

    Seja estrito sobre a cobertura de código involuntariamente. Veja :ref:`risky-tests` para detalhes.

``--strict-global-state``

    Seja estrito sobre manipulação de estado global. Veja :ref:`risky-tests` para detalhes.

``--disallow-test-output``

    Seja estrito sobre a saída durante os testes. Veja :ref:`risky-tests` para detalhes.

``--disallow-todo-tests``

    Não executa testes que tem a anotação ``@todo`` em seu bloco de documentação.

``--enforce-time-limit``

    Impõem limite de tempo baseado no tamanho do teste. Veja :ref:`risky-tests` para detalhes.

``--process-isolation``

    Executa cada teste em um processo PHP separado.

``--no-globals-backup``

    Não faz cópia de segurança e restauração de $GLOBALS. Veja :ref:`fixtures.global-state`
    para mais detalhes.

``--static-backup``

    Faz cópia de segurança e restauração atributos estáticos das classes definidas pelo usuário.
    Veja :ref:`fixtures.global-state` para mais detalhes.

``--colors``

    Usa cores na saída.
    No Windows, usar `ANSICON <https://github.com/adoxa/ansicon>`_ ou `ConEmu <https://github.com/Maximus5/ConEmu>`_.

    Existem três valores possíveis para esta opção:

    -

      ``never``: nunca exibe cores na saída. Esse é o valor padrão quando a opção ``--colors`` não é usada.

    -

      ``auto``: exibe cores na saída a menos que o terminal atual não suporte cores,
      ou se a saída é canalizada (piped) para um comando ou redirecionada para um arquivo.

    -

      ``always``: sempre exibe cores na saída mesmo quando o terminal atual não suporta cores,
      ou quando a saída é canalizada para um comando ou redirecionada para um arquivo.

    Quando ``--colors`` é usada com nenhum valor, ``auto`` é o valor escolhido.

``--columns``

    Define o número de colunas a serem usadas para a saída de progresso.
    Se ``max`` é definido como valor, o número de colunas será o máximo do terminal atual.

``--stderr``

    Opcionalmente imprime para ``STDERR`` em vez de
    ``STDOUT``.

``--stop-on-error``

    Para a execução no primeiro erro.

``--stop-on-failure``

    Para a execução no primeiro erro ou falha.

``--stop-on-risky``

    Para a execução no primeiro teste arriscado.

``--stop-on-skipped``

    Para a execução no primeiro teste pulado.

``--stop-on-incomplete``

    Para a execução no primeiro teste incompleto.

``--verbose``

    Saída mais verbosa de informações, por exemplo, os nomes dos testes
    que ficaram incompletos ou foram pulados.

``--debug``

    Informação de depuração na saída, tal como o nome de um teste quando a
    execução começa.

``--loader``

    Especifica a implementação ``PHPUnit_Runner_TestSuiteLoader``
    a ser usada.

    O carregador de suíte de teste padrão irá procurar pelo arquivo-fonte no
    diretório de trabalho atual e em cada diretório que está especificado na
    diretiva de configuração ``include_path`` do PHP.
    Um nome de classe como ``Project_Package_Class`` é
    mapeado para o nome de arquivo-fonte
    :file:`Project/Package/Class.php`.

``--repeat``

    Executa repetidamente o(s) teste(s) um determinado número de vezes.

``--testdox``

    Relata o progresso do teste como uma documentação ágil. Veja
    :ref:`other-uses-for-tests` para mais detalhes.

``--printer``

    Especifica a impressora de resultados a ser usada. A classe impressora deve estender
    ``PHPUnit_Util_Printer`` e implementar a interface
    ``PHPUnit\Framework\TestListener``.

``--bootstrap``

    Um arquivo PHP "bootstrap" que é executado antes dos testes.

``--configuration``, ``-c``

    Lê a configuração de um arquivo XML.
    Veja :ref:`appendixes.configuration` para mais detalhes.

    Se :file:`phpunit.xml` ou
    :file:`phpunit.xml.dist` (nessa ordem) existir no
    diretório de trabalho atual e ``--configuration``
    *não* for usado, a configuração será lida automaticamente
    desse arquivo.

``--no-configuration``

    Ignora :file:`phpunit.xml` e
    :file:`phpunit.xml.dist` do diretório de trabalho
    atual.

``--include-path``

    Precede o ``include_path`` do PHP com o(s) caminho(s) fornecido(s).

``-d``

    Define o valor da opção de configuração do PHP fornecida.

.. admonition:: Note

   Por favor, note que a partir da 4.8, opções podem ser colocadas após o argumento(s).


