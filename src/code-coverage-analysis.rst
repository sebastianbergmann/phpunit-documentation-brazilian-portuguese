

.. _code-coverage-analysis:

==============================
Análise de Cobertura de Código
==============================

    *Wikipedia*:

    Na ciência da computação, cobertura de código é uma medida usada para descrever o
    grau em que um código fonte de um programa é testado por uma particular
    suíte de testes. Um programa com cobertura de código alta foi mais exaustivamente
    testado e tem uma menor chance de conter erros de software do que um programa
    com baixa cobertura de código.

Neste capítulo você aprenderá tudo sobre a funcionalidade de cobertura de código
do PHPUnit que lhe dará uma perspicácia sobre quais partes do código de produção
são executadas quando os testes são executados. Ele faz uso do componente
`PHP_CodeCoverage <https://github.com/sebastianbergmann/php-code-coverage>`_,
que por sua vez utiliza a funcionalidade de cobertura de código fornecida
pela extensão `Xdebug <http://xdebug.org/>`_ para PHP.

.. admonition:: Note

   Xdebug não é distribuído como parte do PHPUnit. Se você receber um aviso
   durante a execução de testes que a extensão Xdebug não está carregada, isso significa
   que Xdebug não está instalada ou não está configurada corretamente. Antes
   que você possa usar as funcionalidades de análise de cobertura de código no PHPUnit, você deve
   ler `o manual de instalação Xdebug <http://xdebug.org/docs/install>`_.

PHPUnit pode gerar um relatório de cobertura de código baseado em HTML, tal como
arquivos de registros baseado em XML com informações de cobertura de código em vários formatos
(Clover, Crap4J, PHPUnit). Informação de cobertura de código também pode ser relatada
como texto (e impressa na STDOUT) e exportada como código PHP para futuro
processamento.

Por favor, consulte o :ref:`textui` para uma lista de opções de linha de comando
que controlam a funcionalidade de cobertura de código, bem como em :ref:`appendixes.configuration.logging` para as definições de
configurações relevantes.

.. _code-coverage-analysis.metrics:

Métricas de Software para  Cobertura de Código
##############################################

Várias métricas de software existem para mensurar a cobertura de código:

*Cobertura de Linha*

    A métrica de software *Cobertura de Linha* mensura
    se cada linha executável foi executada.

*Cobertura de Função e Método*

    A métrica de software *Cobertura de Função e Método*
    mensura se cada função ou método foi invocado.
    PHP_CodeCoverage só considera uma função ou método como coberto quando
    todas suas linhas executáveis são cobertas.

*Cobertura de Classe e Trait*

    A métrica de software *Cobertura de Classe e Trait*
    mensura se cada método de uma classe ou trait é coberto.
    PHP_CodeCoverage só considera uma classe ou trait como coberta quando todos
    seus métodos são cobertos.

*Cobertura de Código de Operação*

    A métrica de software *Cobertura de Código de Operação* mensura
    se cada código de operação de uma função ou método foi  executado enquanto
    executa a suíte de teste. Uma linha de código geralmente compila em mais
    de um código de operação. Cobertura de Linha considera uma linha de código como coberta
    logo que um dos seus códigos de operações é executado.

*Cobertura de Ramo*

    A métrica de software *Cobertura de Ramo* mensura
    se cada expressão booleana de cada estrutura de controle avaliou
    tanto para ``true`` quanto para ``false`` enquanto
    executa a suíte de teste.

*Cobertura de Caminho*

    A métrica de software *Cobertura de Caminho* mensura
    se cada um dos caminhos de execução possíveis em uma função ou método
    foi seguido durante a execução da suíte de teste. Um caminho de execução é
    uma sequência única de ramos a partir da entrada de uma função ou
    método para a sua saída.

*Índice de Anti-Patterns de Mudança de Risco (CRAP - Change Risk Anti-Patterns)*

    O *Índice de Anti-Patterns de Mudança de Risco (CRAP - Change Risk Anti-Patterns)* é
    calculado baseado na complexidade ciclomática e cobertura de código de uma
    unidade de código. Código que não é muito complexo e tem uma cobertura de teste
    adequada terá um baixo índice CRAP. O índice CRAP pode ser reduzido
    escrevendo testes e refatorando o código para reduzir sua
    complexidade.

.. admonition:: Note

   As métricas de software *Cobertura de Código de Operação*,
   *Cobertura de Ramo* e
   *Cobertura de Caminho* ainda não são
   suportadas pelo PHP_CodeCoverage.

.. _code-coverage-analysis.whitelisting-files:

Lista-branca de arquivos
########################

É obrigatório configurar uma *whitelist* para dizer ao
PHPUnit quais arquivos de código-fonte incluir no relatório de cobertura de código.
Isso pode ser feito usando a opção de linha de comando ``--whitelist``
ou via arquivo de configuração (veja :ref:`appendixes.configuration.whitelisting-files`).

Opcionalmente, todos arquivos da lista-branca podem ser adicionados ao relatório
de cobertura de código ao definiri ``addUncoveredFilesFromWhitelist="true"``
em sua configuração do PHPUnit (veja :ref:`appendixes.configuration.whitelisting-files`). Isso permite a
inclusão de arquivos que não são testados ainda no todo. Se você quer ter
informação sobre quais linhas de um tal arquivo não-coberto são executáveis,
por exemplo, você também precisa definir
``processUncoveredFilesFromWhitelist="true"`` em sua
configuração do PHPUnit (veja :ref:`appendixes.configuration.whitelisting-files`).

.. admonition:: Note

   Por favor, note que o carregamento de arquivos de código-fonte que é realizado, quando
   ``processUncoveredFilesFromWhitelist="true"`` é definido, pode
   causar problemas quando um arquivo de código-fonte contém código fora do escopo de
   uma classe ou função, por exemplo.

.. _code-coverage-analysis.ignoring-code-blocks:

Ignorando Blocos de Código
##########################

Às vezes você tem blocos de código que não pode testar e que pode
querer ignorar durante a análise de cobertura de código. O PHPUnit deixa você fazer isso
usando as anotações ``@codeCoverageIgnore``,
``@codeCoverageIgnoreStart`` e
``@codeCoverageIgnoreEnd`` como mostrado em
:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`.

.. code-block:: php
    :caption: Usando as anotações ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` e ``@codeCoverageIgnoreEnd``
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore
    ?>

As linhas de código ignoradas (marcadas como ignoradas usando as anotações)
são contadas como executadas (se forem executáveis) e não serão
destacadas.

.. _code-coverage-analysis.specifying-covered-methods:

Especificando métodos cobertos
##############################

A anotação ``@covers`` (veja
:ref:`appendixes.annotations.covers.tables.annotations`) pode ser
usada em um código de teste para especificar qual(is) método(s) um método de teste quer
testar. Se fornecido, apenas a informação de cobertura de código para o(s) método(s)
especificado(s) será considerada.
:numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`
mostra um exemplo.

.. code-block:: php
    :caption: Testes que especificam quais métodos eles querem cobrir
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertEquals(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertEquals(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertEquals(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertEquals(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertEquals(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertEquals(0, $this->ba->getBalance());
        }
    }
    ?>

Também é possível especificar que um teste não deve cobrir
*qualquer* método usando a anotação
``@coversNothing`` (veja
:ref:`appendixes.annotations.coversNothing`). Isso pode ser
útil quando escrever testes de integração para certificar-se de que você só
gerará cobertura de código com testes unitários.

.. code-block:: php
    :caption: Um teste que especifica que nenhum método deve ser coberto
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GuestbookIntegrationTest extends PHPUnit_Extensions_Database_TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _code-coverage-analysis.edge-cases:

Casos Extremos
##############

Esta seção mostra casos extremos notáveis que induz a confundir a informação
de cobertura de código.

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // Por ser "baseado em linha" e não em declaração,
    // uma linha sempre terá um estado de cobertura
    if (false) this_function_call_shows_up_as_covered();

    // Devido ao modo como a cobertura de código funciona internamente, estas duas linhas são especiais.
    // Esta linha vai aparecer como não-executável
    if (false)
        // Esta linha vai aparecer como coberta, pois de fato é a cobertura
        // da declaração if da linha anterior que é mostrada aqui!
        will_also_show_up_as_covered();

    // Para evitar isso é necessário usar chaves
    if (false) {
        this_call_will_never_show_up_as_covered();
    }
    ?>


