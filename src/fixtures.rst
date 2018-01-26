

.. _fixtures:

=========
Ambientes
=========

Uma das partes que mais consomem tempo ao se escrever testes é escrever o
código para ajustar o ambiente para um estado conhecido e então retorná-lo ao seu
estado original quando o teste está completo. Esse estado conhecido é chamado
de *ambiente* do teste.

Em :ref:`writing-tests-for-phpunit.examples.StackTest.php`, o
ambiente era simplesmente o vetor que está guardado na variável ``$stack``.
Na maior parte do tempo, porém, o ambiente será mais complexo
do que um simples vetor, e a quantidade de código necessária para defini-lo aumentará
na mesma proporção. O conteúdo real do teste se perde na bagunça
da configuração do ambiente. Esse problema piora ainda mais quando você escreve
vários testes com ambientes similares. Sem alguma ajuda do framework de teste,
teríamos que duplicar o código que define o ambiente
para cada teste que escrevermos.

O PHPUnit suporta compartilhamento do código de configuração. Antes que um método seja executado, um
método modelo chamado ``setUp()`` é invocado.
``setUp()`` é onde você cria os objetos que serão
alvo dos testes. Uma vez que o método de teste tenha terminado sua execução, seja
bem-sucedido ou falho, outro método modelo chamado
``tearDown()`` é invocado. ``tearDown()``
é onde você limpa os objetos que foram alvo dos testes.

Em :ref:`writing-tests-for-phpunit.examples.StackTest2.php` usamos
o relacionamento produtor-consumidor entre testes para compartilhar ambientes. Isso
nem sempre é desejável, ou mesmo possível. :numref:`fixtures.examples.StackTest.php`
mostra como podemos escrever os testes do ``StackTest`` de forma
que o próprio ambiente não é reutilizado, mas o código que o cria.
Primeiro declaramos a variável de instância ``$stack``, que
usaremos no lugar de uma variável do método local. Então colocamos a
criação do ambiente ``vetor`` dentro do método
``setUp()``. Finalmente, removemos o código redundante
dos métodos de teste e usamos a nova variável de instância,
``$this->stack``, em vez da variável do método local
``$stack`` com o método de asserção
``assertEquals()``.

.. code-block:: php
    :caption: Usando setUp() para criar o ambiente stack
    :name: fixtures.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        protected $stack;

        protected function setUp()
        {
            $this->stack = [];
        }

        public function testEmpty()
        {
            $this->assertTrue(empty($this->stack));
        }

        public function testPush()
        {
            array_push($this->stack, 'foo');
            $this->assertEquals('foo', $this->stack[count($this->stack)-1]);
            $this->assertFalse(empty($this->stack));
        }

        public function testPop()
        {
            array_push($this->stack, 'foo');
            $this->assertEquals('foo', array_pop($this->stack));
            $this->assertTrue(empty($this->stack));
        }
    }
    ?>

Os métodos-modelo ``setUp()`` e ``tearDown()``
são executados uma vez para cada método de teste (e em novas instâncias) da
classe do caso de teste.

Além disso, os métodos-modelo ``setUpBeforeClass()`` e
``tearDownAfterClass()`` são chamados antes
do primeiro teste da classe do caso de teste ser executado e após o último teste da
classe do caso de teste ser executado, respectivamente.

O exemplo abaixo mostra todos os métodos-modelo que estão disponíveis em uma classe
de caso de teste.

.. code-block:: php
    :caption: Exemplo mostrando todos os métodos-modelo disponíveis
    :name: fixtures.examples.TemplateMethodsTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class TemplateMethodsTest extends TestCase
    {
        public static function setUpBeforeClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function setUp()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function assertPreConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public function testOne()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(true);
        }

        public function testTwo()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(false);
        }

        protected function assertPostConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function tearDown()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public static function tearDownAfterClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function onNotSuccessfulTest(Exception $e)
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            throw $e;
        }
    }
    ?>

.. code-block:: bash

    $ phpunit TemplateMethodsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    TemplateMethodsTest::setUpBeforeClass
    TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testOne
    TemplateMethodsTest::assertPostConditions
    TemplateMethodsTest::tearDown
    .TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testTwo
    TemplateMethodsTest::tearDown
    TemplateMethodsTest::onNotSuccessfulTest
    FTemplateMethodsTest::tearDownAfterClass

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) TemplateMethodsTest::testTwo
    Failed asserting that <boolean:false> is true.
    /home/sb/TemplateMethodsTest.php:30

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _fixtures.more-setup-than-teardown:

Mais setUp() que tearDown()
###########################

``setUp()`` e ``tearDown()`` são bastante
simétricos em teoria, mas não na prática. Na prática, você só precisa
implementar ``tearDown()`` se você tiver alocado
recursos externos como arquivos ou sockets no ``setUp()``.
Se seu ``setUp()`` apenas cria objetos planos do PHP, você
pode geralmente ignorar o ``tearDown()``. Porém, se você
criar muitos objetos em seu ``setUp()``, você pode querer
``unset()`` as variáveis que apontam para aqueles objetos
em seu ``tearDown()`` para que eles possam ser coletados como lixo.
A coleta de lixo dos objetos dos casos de teste não é previsível.

.. _fixtures.variations:

Variações
#########

O que acontece quando você tem dois testes com definições (setups) ligeiramente diferentes?
Existem duas possibilidades:

-

  Se o código ``setUp()`` diferir só um pouco, mova
  o código que difere do código do ``setUp()`` para
  o método de teste.

-

  Se você tiver um ``setUp()`` realmente diferente, você precisará
  de uma classe de caso de teste diferente. Nomeie a classe após a diferença
  na configuração.

.. _fixtures.sharing-fixture:

Compartilhando Ambientes
########################

Existem algumas boas razões para compartilhar ambientes entre testes, mas na maioria
dos casos a necessidade de compartilhar um ambiente entre testes deriva de um problema
de design não resolvido.

Um bom exemplo de um ambiente que faz sentido compartilhar através de vários
testes é a conexão ao banco de dados: você loga no banco de dados uma vez e
reutiliza essa conexão em vez de criar uma nova conexão para cada
teste. Isso faz seus testes serem executados mais rápido.

:numref:`fixtures.sharing-fixture.examples.DatabaseTest.php`
usa os métodos-modelo ``setUpBeforeClass()`` e
``tearDownAfterClass()`` para conectar ao
banco de dados antes do primeiro teste da classe de casos de teste e para desconectar do
banco de dados após o último teste dos casos de teste, respectivamente.

.. code-block:: php
    :caption: Compartilhando ambientes entre os testes de uma suíte de testes
    :name: fixtures.sharing-fixture.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected static $dbh;

        public static function setUpBeforeClass()
        {
            self::$dbh = new PDO('sqlite::memory:');
        }

        public static function tearDownAfterClass()
        {
            self::$dbh = null;
        }
    }
    ?>

Não dá para enfatizar o suficiente que o compartilhamento de ambientes entre testes
reduz o custo dos testes. O problema de design subjacente é
que objetos não são de baixo acoplamento. Você vai conseguir
melhores resultados resolvendo o problema de design subjacente e então escrevendo testes
usando pontas (veja :ref:`test-doubles`), do que criando
dependências entre os testes em tempo de execução e ignorando a oportunidade
de melhorar seu design.

.. _fixtures.global-state:

Estado Global
#############

`É difícil testar um código que usa singletons (instâncias únicas de objetos). <http://googletesting.blogspot.com/2008/05/tott-using-dependancy-injection-to.html>`_
Isso também vale para os códigos que usam variáveis globais. Tipicamente, o código
que você quer testar é fortemente acoplado com uma variável global e você não pode
controlar sua criação. Um problema adicional é o fato de que uma alteração em uma
variável global para um teste pode quebrar um outro teste.

Em PHP, variáveis globais trabalham desta forma:

-

  Uma variável global ``$foo = 'bar';`` é guardada como ``$GLOBALS['foo'] = 'bar';``.

-

  A variável ``$GLOBALS`` é chamada de variável *super-global*.

-

  Variáveis super-globais são variáveis embutidas que estão sempre disponíveis em todos os escopos.

-

  No escopo de uma função ou método, você pode acessar a variável global ``$foo`` tanto por acesso direto à ``$GLOBALS['foo']`` ou usando ``global $foo;`` para criar uma variável local com uma referência à variável global.

Além das variáveis globais, atributos estáticos de classes também são parte do
estado global.

Antes da versão 6, por padrão, o PHPUnit executa seus testes de forma que mudanças às
variáveis globais ou super-globais (``$GLOBALS``,
``$_ENV``, ``$_POST``,
``$_GET``, ``$_COOKIE``,
``$_SERVER``, ``$_FILES``,
``$_REQUEST``) não afetem outros testes.

A partir da versão 6, o PHPUnit não executa essa operação de backup
e restauração para variáveis globais e super-globais por padrão.
Isso pode ser ativado usando a opção ``--globals-backup``
ou definindo ``backupGlobals="true"`` no
arquivo de configuração XML.

Ao usar a opção ``--static-backup`` ou ao definir
``backupStaticAttributes="true"`` no
no arquivo de configuração XML, esse isolamento pode ser estendido para atributos estáticos
de classes.

.. admonition:: Note

   As operações de backup e restauração para variáveis globais e atributos
   estáticos de classes usa ``serialize()`` e
   ``unserialize()``.

   Objetos de algumas classes (e.g., ``PDO``) não podem ser
   serializados e a operação de backup vai quebrar quando esse tipo de objeto
   for guardado no vetor ``$GLOBALS``, por exemplo.

A anotação ``@backupGlobals`` que é discutida na
:ref:`appendixes.annotations.backupGlobals` pode ser usada para
controlar as operações de backup e restauração para variáveis globais.
Alternativamente, você pode fornecer uma lista-negra de variáveis globais que deverão
ser excluídas das operações de backup e restauração como esta:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupGlobalsBlacklist = ['globalVariable'];

        // ...
    }

.. admonition:: Note

   Definir a propriedade ``$backupGlobalsBlacklist`` dentro
   do método ``setUp()``, por exemplo, não tem efeito.

A anotação ``@backupStaticAttributes`` que é discutida na
:ref:`appendixes.annotations.backupStaticAttributes`
pode ser usada para fazer backup de todos os valores de propriedades estáticas em todas as classes declaradas
antes de cada teste e restaurá-los depois.

Ele processa todas as classes que são declaradas no momento que um teste começa, não
só a classe de teste. Ele só se aplica a propriedades estáticas de classe,
e não variáveis estáticas dentro de funções.

.. admonition:: Note

   A operação ``@backupStaticAttributes`` é executada
   antes de um método de teste, mas somente se ele está habilitado. Se um valor estático foi
   alterado por um teste executado anteriormente que não
   tinha ativado ``@backupStaticAttributes``, então esse valor será
   copiado e restaurado - não o valor padrão originalmente declarado.
   O PHP não registra o valor padrão originalmente declarado de nenhuma variável
   estática.

   O mesmo se aplica a propriedades estáticas de classes que foram
   recém-carregadas/declaradas dentro de um teste. Elas não podem ser redefinidas para o seu valor
   padrão originalmente declarado após o teste, uma vez que esse valor é desconhecido.
   Qualquer que seja o valor definido irá vazar para testes subsequentes.

   Para teste unitários, recomenda-se redefinir explicitamente os valores das
   propriedades estáticas sob teste em seu código ``setUp()``
   ao invés (e, idealmente, também ``tearDown()``, de modo a não
   afetar os testes posteriormente executados).

Você pode fornecer uma lista-negra de atributos estáticos que serão excluídos
das operações de backup e restauração:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupStaticAttributesBlacklist = [
            'className' => ['attributeName']
        ];

        // ...
    }

.. admonition:: Note

   Definir a propriedade ``$backupStaticAttributesBlacklist``
   dentro do método ``setUp()`` , por exemplo, não tem efeito.


