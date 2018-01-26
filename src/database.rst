

.. _database:

========================
Testando Bancos de Dados
========================

Muitos exemplos de testes unitários iniciantes e intermediários em qualquer linguagem de
programação sugerem que é perfeitamente fácil testar a lógica de sua aplicação
com testes simples. Para aplicações centradas em bancos de dados isso está longe
da realidade. Comece a usar WordPress, TYPO3 ou Symfony com Doctrine ou Propel,
por exemplo, e você vai experimentar facilmente problemas consideráveis com o
PHPUnit: apenas porque o banco de dados é fortemente acoplado com essas bibliotecas.

.. admonition:: Note

   Tenha certeza que você possui a extensão PHP ``pdo`` e as
   extensões de banco de dados específicas tal como ``pdo_mysql`` instalada.
   Caso contrário os exemplos abaixo não irão funcionar.

Você provavelmente conhece esse cenário dos seus trabalhos e projetos diários,
onde você quer colocar em prática suas habilidades (novas ou não) com PHPUnit
e acaba ficando preso por um dos seguintes problemas:

#.

   O método que você quer testar executa uma operação JOIN muito grande e
   usa os dados para calcular alguns resultados importantes.

#.

   Sua lógica de negócios faz uma mistura de declarações SELECT, INSERT, UPDATE
   e DELETE.

#.

   Você precisa definir os dados de teste em (muito provavelmente) mais de duas tabelas
   para conseguir dados iniciais razoáveis para os métodos que deseja testar.

A extensão DbUnit simplifica consideravelmente a configuração de um banco de dados
para fins de teste e permite a você verificar os conteúdos de um banco de dados após
fazer uma série de operações.

.. _database.supported-vendors-for-database-testing:

Fornecedores Suportados para Testes de Banco de Dados
#####################################################

DbUnit atualmente suporta MySQL, PostgreSQL, Oracle e SQLite. Através
das integrações `Zend Framework <http://framework.zend.com>`_ ou
`Doctrine 2 <http://www.doctrine-project.org>`_
ele tem acesso a outros sistemas como IBM DB2 ou
Microsoft SQL Server.

.. _database.difficulties-in-database-testing:

Dificuldades em Testes de Bancos de Dados
#########################################

Existe uma boa razão pela qual todos os exemplos de testes unitários não incluírem
interações com bancos de dados: esses tipos de testes são complexos tanto em
configuração quanto em manutenção. Enquanto testar contra seu banco de dados você
precisará ter cuidado com as seguintes variáveis:

-

  O Esquema e tabelas do banco de dados

-

  Inserção das linhas exigidas para o teste nessas tabelas

-

  Verificação do estado do banco de dados depois de executar os testes

-

  Limpeza do banco de dados para cada novo teste

Por causa de muitas APIs de bancos de dados como PDO, MySQLi ou OCI8 serem incômodos
de usar e verbosas para escrever, fazer esses passos manualmente é um completo
pesadelo.

O código de teste deve ser o mais curto e preciso possível por várias razões:

-

  Você não quer modificar uma considerável quantidade de código de teste por
  pequenas mudanças em seu código de produção.

-

  Você quer ser capaz de ler e entender o código de teste facilmente,
  mesmo meses depois de tê-lo escrito.

Adicionalmente você tem que perceber que o banco de dados é essencialmente
uma variável global de entrada para seu código. Dois testes em sua suíte de testes
podem executar contra o mesmo banco de dados, possivelmente reutilizando dados
múltiplas vezes. Falhas em um teste podem facilmente afetar o resultado dos
testes seguintes, fazendo sua experiência com os testes muito difícil. O
passo de limpeza mencionado anteriormente é de maior importância
para resolver o problema do “banco de dados ser uma entrada global“.

DbUnit ajuda a simplificar todos esses problemas com testes de bancos de dados de uma
forma elegante.

O PHPUnit só não pode ajudá-lo no fato de que testes de banco de dados
são muito lentos comparados aos testes que não usam bancos de dados. Dependendo
do tamanho das interações com seu banco de dados, seus testes
podem levar um tempo considerável para executar. Porém se você mantiver pequena
a quantidade de dados usados para cada teste e tentar testar o máximo possível
sem usar testes com bancos de dados, você facilmente conseguirá tempos abaixo de um minuto,
mesmo para grandes suítes de teste.

A suíte de testes do `projeto
Doctrine 2 <http://www.doctrine-project.org>`_ por exemplo, atualmente tem uma suíte com
cerca de 1000 testes onde aproximadamente a metade deles tem acesso ao banco
de dados e ainda executa em 15 segundos contra um banco de dados MySQL em
um computador desktop comum.

.. _database.the-four-stages-of-a-database-test:

Os quatro estágios dos testes com banco de dados
################################################

Em seu livro sobre Padrões de Teste xUnit, Gerard Meszaros lista os quatro
estágios de um teste unitário:

#.

   Configurar o ambiente (fixture)

#.

   Exercitar o Sistema Sob Teste

#.

   Verificar o resultado

#.

   Desmontagem (Teardown)

    *O que é um ambiente (fixture)?*

    Um ambiente (fixture) descreve o estado inicial em que sua aplicação e seu banco de dados
    estão ao executar um teste.

Testar o banco de dados exige que você utilize pelo menos a
configuração (setup) e desmontagem (teardown) para limpar e escrever em suas tabelas
os dados de ambiente exigidos. Porém a extensão do banco de dados tem uma boa razão para
reverter esses quatro estágios em um teste de banco de dados para assemelhar o seguinte
fluxo de trabalho que é executado para cada teste:

.. _database.clean-up-database:

1. Limpar o Banco de Dados
==========================

Já que sempre existe um primeiro teste que é executado contra o banco de dados,
você não sabe exatamente se já existem dados nas tabelas.
O PHPUnit vai executar um TRUNCATE contra todas as tabelas que
você especificou para redefinir seus estados para vazio.

.. _database.set-up-fixture:

2. Configurar o ambiente
========================

O PHPUnit então vai iterar sobre todas as linhas do ambiente especificado e
inseri-las em suas respectivas tabelas.

.. _database.run-test-verify-outcome-and-teardown:

3–5. Executar Teste, Verificar resultado e Desmontar (Teardown)
===============================================================

Depois de redefinir o banco de dados e carregá-lo com seu estado inicial,
o verdadeiro teste é executado pelo PHPUnit. Esta parte do código de teste
não exige conhecimento sobre a Extensão do Banco de Dados, então você pode
prosseguir e testar o que quiser com seu código.

Em seu teste use uma asserção especial chamada
``assertDataSetsEqual()`` para fins de verificação,
porém isso é totalmente opcional. Esta função será explicada
na seção “Asserções em Bancos de Dados“.

.. _database.configuration-of-a-phpunit-database-testcase:

Configuração de um Caso de Teste de Banco de Dados do PHPUnit
#############################################################

Ao usar o PHPUnit seus casos de teste vão estender a
classe ``PHPUnit\Framework\TestCase`` da
seguinte forma:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        public function testCalculate()
        {
            $this->assertEquals(2, 1 + 1);
        }
    }
    ?>

Se você quer um código de teste que trabalha com a Extensão para Banco de Dados a
configuração é um pouco mais complexa e você terá que estender um TestCase abstrato
diferente, exigindo que você implemente dois métodos abstratos
``getConnection()`` e
``getDataSet()``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit_Extensions_Database_DataSet_IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }
    ?>

.. _database.implementing-getconnection:

Implementando getConnection()
=============================

Para permitir que as funcionalidades limpeza e carregamento de ambiente funcionem,
a extensão de banco de dados PHPUnit requer acesso a uma conexão de banco de dados
abstraída entre fornecedores através da biblioteca PDO. É
importante notar que sua aplicação não precisa ser
baseada em PDO para usar a Extensão para Banco de Dados do PHPUnit, pois a conexão é
meramente usada para limpeza e configuração de ambiente.

No exemplo anterior criamos uma conexão Sqlite na memória e
a passamos ao método ``createDefaultDBConnection``
que embrulha a instância do PDO e o segundo parâmetro (o
nome do banco de dados) em uma camada simples de abstração para conexões
do banco de dados do tipo
``PHPUnit_Extensions_Database_DB_IDatabaseConnection``.

A seção “Usando a Conexão de Banco de Dados“ explica
a API desta interface e como você pode usá-la da melhor forma possível.

.. _database.implementing-getdataset:

Implementando getDataSet()
==========================

O método ``getDataSet()`` define como deve ser o estado
inicial do banco de dados antes de cada teste ser
executado. O estado do banco de dados é abstraído através de
conceitos DataSet (Conjunto de Dados) e DataTable (Tabela de Dados), ambos sendo representados
pelas interfaces
``PHPUnit_Extensions_Database_DataSet_IDataSet`` e
``PHPUnit_Extensions_Database_DataSet_IDataTable``.
A próxima seção vai descrever em detalhes como esses conceitos trabalham
e quais os benefícios de usá-los nos testes com bancos de dados.

Para a implementação precisaremos apenas saber que o
método ``getDataSet()`` é chamado uma vez durante o
``setUp()`` para recuperar o conjunto de dados do ambiente e
inseri-lo no banco de dados. No exemplo estamos usando um método de fábrica
``createFlatXMLDataSet($filename)`` que
representa um conjunto de dados através de uma representação XML.

.. _database.what-about-the-database-schema-ddl:

E quanto ao Esquema do Banco de Dados (DDL)?
============================================

O PHPUnit assume que o esquema do banco de dados com todas as suas tabelas,
gatilhos, sequências e visualizações é criado antes que um teste seja executado. Isso
quer dizer que você como desenvolvedor deve se certificar que o banco de dados está
corretamente configurado antes de executar a suíte.

Existem vários meios para realizar esta pré-condição para
testar bancos de dados.

#.

   Se você está usando um banco de dados persistente (não Sqlite Memory) você pode
   facilmente configurar o banco de dados uma vez com ferramentas como phpMyAdmin para
   MySQL e reutilizar o banco de dados para cada execução de teste.

#.

   Se você estiver usando bibliotecas como
   `Doctrine 2 <http://www.doctrine-project.org>`_ ou
   `Propel <http://www.propelorm.org/>`_
   você pode usar suas APIs para criar o esquema de banco de dados que
   precisa antes de rodar os testes. Você pode utilizar as capacidades de
   `Configuração e Bootstrap do PHPUnit <textui.html>`_
   para executar esse código sempre que seus testes forem executados.

.. _database.tip-use-your-own-abstract-database-testcase:

Dica: Use seu próprio Caso Abstrato de Teste de Banco de Dados
==============================================================

Do exemplo prévio de implementação você pode facilmente perceber que
o método ``getConnection()`` é bastante estático e
pode ser reutilizado em diferentes casos de teste de banco de dados. Adicionalmente para
manter uma boa performance dos seus testes e pouca carga sobre seu banco de dados, você
pode refatorar o código um pouco para obter um caso de teste abstrato genérico
para sua aplicação, o que ainda permite você especificar um
ambiente de dados diferente para cada caso de teste:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class MyApp_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // só instancia o pdo uma vez para limpeza de teste e carregamento de ambiente
        static private $pdo = null;

        // só instancia PHPUnit_Extensions_Database_DB_IDatabaseConnection uma vez por teste
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }
    ?>

Contudo, isso tem a conexão ao banco de dados codificada na conexão do PDO.
O PHPUnit tem outra incrível característica que pode fazer este
caso de teste ainda mais genérico. Se você usar a
`Configuração XML <appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables>`_
você pode tornar a conexão com o banco de dados configurável por execução de teste.
Primeiro vamos criar um arquivo “phpunit.xml“ em nosso diretório/de/teste
da aplicação, de forma semelhante a isto:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

Agora podemos modificar seu caso de teste para parecer com isso:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class Generic_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // só instancia o pdo uma vez para limpeza de teste e carregamento de ambiente
        static private $pdo = null;

        // só instancia PHPUnit_Extensions_Database_DB_IDatabaseConnection uma vez por teste
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }
    ?>

Agora podemos executar a suíte de testes de banco de dados usando diferentes
configurações através da interface de linha-de-comando:

.. code-block:: bash

    $ user@desktop> phpunit --configuration developer-a.xml MyTests/
    $ user@desktop> phpunit --configuration developer-b.xml MyTests/

A possibilidade de executar facilmente os testes de banco de dados contra diferentes
alvos é muito importante se você está desenvolvendo na
máquina de desenvolvimento. Se vários desenvolvedores executarem os testes de banco
de dados contra a mesma conexão de banco de dados você experimentará facilmente
falhas de testes devido à condição de execução.

.. _database.understanding-datasets-and-datatables:

Entendendo Conjunto de Dados e Tabelas de Dados
###############################################

Um conceito central da Extensão para Banco de Dados do PHPUnit são
os Conjuntos de Dados e as Tabelas de Dados. Você deveria tentar entender este conceito simples para
dominar os testes de banco de dados com PHPUnit. Conjunto de Dados e Tabela de Dados formam
uma camada abstrata em torno das tabelas, linhas e
colunas do seu banco de dados. Uma simples API esconde os conteúdos subjacentes do banco de dados em uma
estrutura de objetos, que também podem ser implementada por outra
fonte que não seja um banco de dados.

Essa abstração é necessária para comparar os conteúdos reais de um
banco de dados contra os conteúdos esperados. Expectativas podem ser
representadas como arquivos XML, YAML, CSV ou vetores PHP, por exemplo. As
interfaces DataSet (Conjunto de Dados) e DataTable (Tabela de Dados)  permitem a comparação dessas
fontes conceitualmente diferentes, emulando o armazenamento de banco de dados
relacional em uma abordagem semanticamente similar.

Um fluxo de trabalho para asserções em banco de dados em seus testes consiste em
três etapas simples:

-

  Especificar uma ou mais tabelas em seu banco de dados por nome de tabela (conjunto de dados
  real)

-

  Especificar o Conjunto de Dados esperado no seu formato preferido (YAML, XML,
  ...)

-

  Asseverar que ambas as representações de conjunto de dados se equivalem.

Asserções não são o único caso de uso para o Conjunto de Dados e a Tabela de Dados
na Extensão para Banco de Dados do PHPUnit. Como mostrado na seção anterior,
eles também descrevem os conteúdos iniciais de um banco de dados. Você é
forçado a definir um conjunto de dados de ambiente pelo Caso de Teste de Banco de Dados, que
então é usado para:

-

  Deletar todas as linhas das tabelas especificadas no conjunto de dados.

-

  Escrever todas as linhas nas tabelas de dados do banco de dados.

.. _database.available-implementations:

Implementações disponíveis
==========================

Existem três tipos diferentes de conjuntos de dados/tabelas de dados:

-

  Conjuntos de Dados e Tabelas de Dados baseados em arquivo

-

  Conjuntos de Dados e Tabelas de Dados baseados em query

-

  Filtro e Composição de Conjunto de Dados e Tabelas de Dados

Os Conjuntos de Dados e Tabelas de Dados baseadas em arquivo são geralmente usados para o
ambiente inicial e para descrever os estados esperados do banco de dados.

.. _database.flat-xml-dataset:

Conjunto de Dados de XML Plano
------------------------------

O Conjunto de Dados mais comum é chamado XML Plano. É um formato de xml muito simples
onde uma tag dentro do nó-raiz
``<dataset>`` representa exatamente uma linha no
banco de dados. Os nomes das tags equivalem à tabela onde inserir a linha e
um atributo representa a coluna. Um exemplo para uma simples aplicação de
livro de visitas poderia se parecer com isto:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

Isso é, obviamente, fácil de escrever. Aqui
``<guestbook>`` é o nome da tabela onde duas linhas
são inseridas onde cada qual com quatro colunas “id“,
“content“, “user“ e
“created“ com seus respectivos valores.

Porém essa simplicidade tem um preço.

O exemplo anterior não deixa tão óbvio como você pode fazer para especificar uma
tabela vazia. Você pode inserir uma tag sem atributos com o nome da
tabela vazia. Um arquivo xml plano para uma tabela vazia do livro de visitas
ficaria parecido com isso:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

A manipulação de valores NULL com o xml plano é tedioso. Um
valor NULL é diferente de uma string com valor vazio em quase todos
os bancos de dados (Oracle é uma exceção), algo difícil
de descrever no formato xml plano. Você pode representar um valor NULL
omitindo o atributo da especificação da linha. Se nosso
livro de visitas vai permitir entradas anônimas representadas por um valor NULL
na coluna user, um estado hipotético para a tabela do livro de visitas
seria parecido com:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" created="2010-04-26 12:14:20" />
    </dataset>

Nesse caso a segunda entrada é postada anonimamente. Porém isso
acarreta um problema sério no reconhecimento de colunas. Durante as asserções de igualdade
do conjunto de dados, cada conjunto de dados tem que especificar quais colunas uma
tabela possui. Se um atributo for NULL para todas as linhas de uma
tabela de dados, como a Extensão para Banco de Dados vai saber que a coluna
deve ser parte da tabela?

O conjunto de dados em xml plano faz uma presunção crucial agora, definindo que
os atributos na primeira linha definida de uma tabela define as
colunas dessa tabela. No exemplo anterior isso significaria que
“id“, “content“, “user“ e
“created“ são colunas da tabela guestbook. Para a
segunda linha, onde “user“ não está definido, um NULL seria
inserido no banco de dados.

Quando a primeira entrada do guestbook for apagada do conjunto de dados, apenas
“id“, “content“ e
“created“ seriam colunas da tabela guestbook,
já que “user“ não é especificado.

Para usar o Conjunto de Dados em XML Plano efetivamente, quando valores NULL forem
relevantes, a primeira linha de cada tabela não deve conter qualquer valor NULL
e apenas as linhas seguintes poderão omitir atributos. Isso
pode parecer estranho, já que a ordem das linhas é um fator relevante
para as asserções com bancos de dados.

Em troca, se você especificar apenas um subconjunto das colunas da tabela no
Conjunto de Dados do XML Plano, todos os valores omitidos serão definidos para seus valores
padrão. Isso vai induzir a erros se uma das colunas omitidas estiver
definida como “NOT NULL DEFAULT NULL“.

Para concluir eu posso dizer que os conjuntos de dados XML Plano só devem ser usadas se você
não precisar de valores NULL.

Você pode criar uma instância de conjunto de dados xml plano de dentro de seu
Caso de Teste de Banco de Dados chamando o método
``createFlatXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }
    ?>

.. _database.xml-dataset:

Conjunto de Dados XML
---------------------

Existe um outro Conjunto de Dados em XML mais estruturado, que é um pouco mais
verboso para escrever mas evita os problemas do NULL nos conjuntos de dados em
XML Plano. Dentro do nó-raiz ``<dataset>`` você
pode especificar as tags ``<table>``,
``<column>``, ``<row>``,
``<value>`` e
``<null />``. Um Conjunto de Dados equivalente ao
definido anteriormente no Guestbook em XML Plano seria como:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Hello buddy!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>I like it!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

Qualquer ``<table>`` definida tem um nome e requer
uma definição de todas as colunas com seus nomes. Pode conter zero
ou qualquer número positivo de elementos ``<row>``
aninhados. Não definir nenhum elemento ``<row>`` significa
que a tabela está vazia. As tags ``<value>`` e
``<null />`` têm que ser especificadas na
ordem dos elementos fornecidos previamente em
``<column>``. A tag ``<null />`` obviamente significa
que o valor é NULL.

Você pode criar uma instância de conjunto de dados xml de dentro de seu
Caso de Teste de Banco de Dados chamando o método
``createXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }
    ?>

.. _database.mysql-xml-dataset:

Conjunto de Dados XML MySQL
---------------------------

Este novo formato XML é específico para o
`servidor de banco de dados MySQL <http://www.mysql.com>`_.
O suporte para ele foi adicionado no PHPUnit 3.5. Arquivos nesse formato podem
ser gerados usando o utilitário
```mysqldump`` <http://dev.mysql.com/doc/refman/5.0/en/mysqldump.html>`_.
Diferente dos conjuntos de dados CSV, que o ``mysqldump``
também suporta, um único arquivo neste formato XML pode conter dados
para múltiplas tabelas. Você pode criar um arquivo nesse formato
invocando o ``mysqldump`` desta forma:

.. code-block:: bash

    $ mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

Esse arquivo pode ser usado em seu Caso de Teste de Banco de Dados chamando o método
``createMySQLXMLDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }
    ?>

.. _database.yaml-dataset:

Conjunto de Dados YAML
----------------------

Alternativamente, você pode usar o Conjunto de dados YAML para o exemplo guestbook:

.. code-block:: bash

    guestbook:
      -
        id: 1
        content: "Hello buddy!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "I like it!"
        user:
        created: 2010-04-26 12:14:20

Isso é simples, conveniente E resolve o problema do NULL que o
Conjunto de Dados similar do XML Plano tem. Um NULL em um YAML é apenas o nome
da coluna sem nenhum valor especificado. Uma string vazia é especificada como
``column1: ""``.

O Conjunto de Dados YAML atualmente não possui método fábrica no Caso de Teste
de Banco de Dados, então você tem que instanciar manualmente:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\YamlDataSet;

    class YamlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new YamlDataSet(dirname(__FILE__)."/_files/guestbook.yml");
        }
    }
    ?>

.. _database.csv-dataset:

Conjunto de Dados CSV
---------------------

Outro Conjunto de Dados baseado em arquivo é baseado em arquivos CSV. Cada tabela do
conjunto de dados é representada como um único arquivo CSV. Para nosso exemplo
do guestbook, vamos definir um arquivo guestbook-table.csv:

.. code-block:: bash

    id,content,user,created
    1,"Hello buddy!","joe","2010-04-24 17:15:23"
    2,"I like it!","nancy","2010-04-26 12:14:20"

Apesar disso ser muito conveniente para edição no Excel ou OpenOffice,
você não pode especificar valores NULL em um Conjunto de Dados CSV. Uma coluna
vazia levaria a um valor vazio padrão de banco de dados a ser inserido
na coluna.

Você pode criar um Conjunto de Dados CSV chamando:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\CsvDataSet;

    class CsvGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            $dataSet = new CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }
    ?>

.. _database.array-dataset:

Conjunto de Dados em Vetor
--------------------------

Não existe Conjunto de Dados baseado em vetor na Extensão de Banco de Dados do PHPUnit
(ainda), mas podemos implementar a nossa própria facilmente. Nosso exemplo do guestbook
deveria ficar parecido com:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ArrayGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(
                [
                    'guestbook' => [
                        [
                            'id' => 1,
                            'content' => 'Hello buddy!',
                            'user' => 'joe',
                            'created' => '2010-04-24 17:15:23'
                        ],
                        [
                            'id' => 2,
                            'content' => 'I like it!',
                            'user' => null,
                            'created' => '2010-04-26 12:14:20'
                        ],
                    ],
                ]
            );
        }
    }
    ?>

Um Conjunto de Dados do PHP tem algumas vantagens óbvias sobre todos os outros
conjuntos de dados baseados em arquivos:

-

  Vetores PHP podem, obviamente, trabalhar com valores ``NULL``.

-

  Você não precisa de arquivos adicionais para asserções e pode especificá-las diretamente
  no TestCase (na classe de Caso de Teste).

Para este Conjunto de Dados, como nos Conjuntos de Dados em XML Plano, CSV e YAML, as chaves
da primeira linha especificada definem os nomes das colunas das tabelas, que no
caso anterior seriam “id“,
“content“, “user“ e
“created“.

A implementação para esse Conjunto de Dados é simples e
direta:

.. code-block:: php

    <?php
    class MyApp_DbUnit_ArrayDataSet extends PHPUnit_Extensions_Database_DataSet_AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = [];

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data AS $tableName => $rows) {
                $columns = [];
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new PHPUnit_Extensions_Database_DataSet_DefaultTableMetaData($tableName, $columns);
                $table = new PHPUnit_Extensions_Database_DataSet_DefaultTable($metaData);

                foreach ($rows AS $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = false)
        {
            return new PHPUnit_Extensions_Database_DataSet_DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName is not a table in the current database.");
            }

            return $this->tables[$tableName];
        }
    }
    ?>

.. _database.query-sql-dataset:

Conjunto de Dados Query (SQL)
-----------------------------

Para asserções de banco de dados você não precisa somente de conjuntos de dados baseados em arquivos,
mas também de conjuntos de dados baseados em Query/SQL que contenha os conteúdos
reais do banco de dados. É aí que entra o Query DataSet:

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');
    ?>

Adicionar uma tabela apenas por nome é um modo implícito de definir a
tabela de dados com a seguinte query:

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');
    ?>

Você pode fazer uso disso especificando queries arbitrárias para suas
tabelas, por exemplo restringindo linhas, colunas, ou adicionando
cláusulas ``ORDER BY``:

.. code-block:: php

    <?php
    $ds = new PHPUnit_Extensions_Database_DataSet_QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');
    ?>

A seção nas Asserções de Banco de Dados mostrará mais alguns detalhes sobre
como fazer uso do Conjunto de Dados Query.

.. _database.database-db-dataset:

Conjunto de Dados de Banco de Dados (BD)
----------------------------------------

Acessando a Conexão de Teste você pode criar automaticamente um
Conjunto de Dados que consiste de todas as tabelas com seus conteúdos no
banco de dados especificado como segundo parâmetro ao método
Fábrica de Conexões.

Você pode tanto criar um Conjunto de Dados para todo o banco de dados como mostrado
em  ``testGuestbook()``, ou restringi-lo a um conjunto de
nomes específicos de tabelas com uma lista branca, como mostrado no
método ``testFilteredGuestbook()``.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MySqlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit_Extensions_Database_DB_IDatabaseConnection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }
    ?>

.. _database.replacement-dataset:

Conjunto de Dados de Substituição
---------------------------------

Eu tenho falado sobre problemas com NULL nos Conjunto de Dados XML Plano e
CSV, mas existe uma alternativa um pouco complicada para fazer ambos
funcionarem com NULLs.

O Conjunto de Dados de Substituição é um decorador para um Conjunto de Dados existente e
permite que você substitua valores em qualquer coluna do conjunto de dados por outro
valor de substituição. Para fazer nosso exemplo do guestbook funcionar com valores NULL
devemos especificar o arquivo como:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Então envolvemos o Conjunto de Dados em XML Plano dentro de um Conjunto de Dados de Substituição:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ReplacementTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit_Extensions_Database_DataSet_ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }
    ?>

.. _database.dataset-filter:

Filtro de Conjunto de Dados
---------------------------

Se você tiver um arquivo grande de ambiente você pode usar o Filtro de Conjunto de Dados para
as listas branca e negra das tabelas e colunas que deveriam estar
contidas em um sub-conjunto de dados. Isso ajuda especialmente em combinação
com o Conjunto de dados DB para filtrar as colunas dos conjuntos de dados.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class DataSetFilterTest extends TestCase
    {
        use TestCaseTrait;

        public function testIncludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(['guestbook']);
            $filterDataSet->setIncludeColumnsForTable('guestbook', ['id', 'content']);
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit_Extensions_Database_DataSet_DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(['foo', 'bar', 'baz']); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', ['user', 'created']);
            // ..
        }
    }
    ?>

    *NOTA* Você não pode usar ambos os
    filtros de incluir e excluir coluna na mesma tabela, apenas em tabelas
    diferentes. E mais: só é possível para a lista branca ou negra,
    mas não para ambas.

.. _database.composite-dataset:

Conjunto de Dados Composto
--------------------------

O Conjunto de Dados composto é muito útil para agregar vários
conjuntos de dados já existentes em um único Conjunto de Dados. Quando vários
conjuntos de dados contém as mesmas tabelas, as linhas são anexadas na
ordem especificada. Por exemplo se tivermos dois conjuntos de dados
*fixture1.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

e *fixture2.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Usando o Conjunto de Dados Composto podemos agregar os dois arquivos de ambiente:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class CompositeTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit_Extensions_Database_DataSet_CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }
    ?>

.. _database.beware-of-foreign-keys:

Cuidado com Chaves Estrangeiras
===============================

Durante a Configuração do Ambiente a Extensão para Banco de Dados do PHPUnit insere as linhas
no banco de dados na ordem que são especificadas em seu ambiente.
Se seu esquema de banco de dados usa chaves estrangeiras isso significa que você tem que
especificar as tabelas em uma ordem que não faça as restrições das chaves estrangeiras
falharem.

.. _database.implementing-your-own-datasetsdatatables:

Implementando seus próprios Conjuntos de Dados/ Tabelas de Dados
================================================================

Para entender os interiores dos Conjuntos de Dados e Tabelas de Dados, vamos dar uma
olhada na interface de um Conjunto de Dados. Você pode pular esta parte se você
não planeja implementar seu próprio Conjunto de Dados ou Tabela de Dados.

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DataSet_IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_IDataSet $other);

        public function getReverseIterator();
    }
    ?>

A interface pública é usada internamente pela asserção
``assertDataSetsEqual()`` no Caso de Teste de
Banco de Dados para verificar a qualidade do conjunto de dados. Da interface
``IteratorAggregate`` o IDataSet
herda o método ``getIterator()`` para iterar
sobre todas as tabelas do conjunto de dados. O iterador reverso permite o PHPUnit
truncar corretamente as tabelas em ordem reversa à que foi criada para satisfazer
as restrições de chaves estrangeiras.

Dependendo da implementação, diferentes abordagens são usadas para
adicionar instâncias de tabela a um Conjunto de Dados. Por exemplo, tabelas são adicionadas
internamente durante a construção a partir de um arquivo fonte em todos
os conjuntos de dados baseados em arquivo como ``YamlDataSet``,
``XmlDataSet`` ou ``FlatXmlDataSet``.

Uma tabela também é representada pela seguinte interface:

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DataSet_ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(PHPUnit_Extensions_Database_DataSet_ITable $other);
    }
    ?>

Com exceção do método ``getTableMetaData()`` isso é
bastante auto-explicativo. Os métodos usados são todos requeridos para
as diferentes asserções da Extensão para Banco de Dados que são
explicados no próximo capítulo. O método
``getTableMetaData()`` deve retornar uma
implementação da interface
``PHPUnit_Extensions_Database_DataSet_ITableMetaData``
que descreve a estrutura da tabela. Possui informações
sobre:

-

  O nome da tabela

-

  Um vetor de nomes de colunas da tabela, ordenado por suas aparições
  nos conjuntos de resultados.

-

  Um vetor de colunas de chaves-primárias.

Essa interface também tem uma asserção que verifica se duas instâncias
de Metadados de Tabela se equivalem, o que é usado pela asserção de igualdade
do conjunto de dados.

.. _database.the-connection-api:

A API de Conexão
################

Existem três métodos interessantes na interface Connection
que devem ser retornados do método
``getConnection()`` no Caso de Teste de Banco de Dados:

.. code-block:: php

    <?php
    interface PHPUnit_Extensions_Database_DB_IDatabaseConnection
    {
        public function createDataSet(Array $tableNames = NULL);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = NULL);

        // ...
    }
    ?>

#.

   O método ``createDataSet()`` cria um Conjunto de Dados
   de Banco de Dados (BD) como descrito na seção de implementações de Conjunto de Dados.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSet()
           {
               $tableNames = ['guestbook'];
               $dataSet = $this->getConnection()->createDataSet();
           }
       }
       ?>

#.

   O método ``createQueryTable()`` pode ser usado para
   criar instâncias de uma QueryTable, dado um nome de resultado e uma query
   SQL. Este é um método conveniente quando se fala sobre asserções de resultado/tabela
   como será mostrado na próxima seção de API de Asserções
   de Banco de Dados.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateQueryTable()
           {
               $tableNames = ['guestbook'];
               $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
           }
       }
       ?>

#.

   O método ``getRowCount()`` é uma forma conveniente de
   acessar o número de linhas em uma tabela, opcionalmente filtradas por uma
   cláusula where adicional. Isso pode ser usado com uma simples asserção
   de igualdade:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testGetRowCount()
           {
               $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'));
           }
       }
       ?>

.. _database.database-assertions-api:

API de Asserções de Banco de Dados
##################################

Para uma ferramenta de testes, a Extensão para Banco de Dados certamente fornece algumas
asserções que você pode usar para verificar o estado atual do banco de dados,
tabelas e a contagem de linhas de tabelas. Esta seção
descreve essa funcionalidade em detalhes:

.. _database.asserting-the-row-count-of-a-table:

Asseverando a contagem de linhas de uma Tabela
==============================================

Às vezes ajuda verificar se uma tabela contém uma quantidade específica
de linhas. Você pode conseguir isso facilmente sem colar códigos adicionais
usando a API de Conexão. Suponha que queiramos verificar se após a
inserção de uma linha em nosso guestbook não temos somente as duas entradas
iniciais que nos acompanharam em todos os exemplos
anteriores, mas uma terceira:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $this->assertEquals(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertEquals(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }
    ?>

.. _database.asserting-the-state-of-a-table:

Asseverando o Estado de uma Tabela
==================================

A asserção anterior ajuda, mas certamente queremos verificar os
conteúdos reais da tabela para verificar se todos os valores foram
escritos nas colunas corretas. Isso pode ser conseguido com uma
asserção de tabela.

Para isso vamos definir uma instância de Tabela Query que deriva seu
conteúdo de um nome de tabela e de uma query SQL e compara isso a um
Conjunto de Dados baseado em Arquivo/Vetor:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

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

Agora temos que escrever o arquivo XML Plano *expectedBook.xml*
para esta asserção:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Hello world!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

Apesar disso, esta asserção só vai passar em exatamente um segundo
do universo, em *2010–05–01 21:47:08*. Datas
possuem um problema especial nos testes de bancos de dados e podemos circundar
a falha omitindo a coluna “created“
da asserção.

O arquivo ajustado *expectedBook.xml* em XML Plano
provavelmente vai ficar parecido com o seguinte para fazer a
asserção passar:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" />
        <guestbook id="2" content="I like it!" user="nancy" />
        <guestbook id="3" content="Hello world!" user="suzy" />
    </dataset>

Nós temos que consertar a chamada da Tabela Query:

.. code-block:: php

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );
    ?>

.. _database.asserting-the-result-of-a-query:

Asseverando o Resultado de uma Query
====================================

Você também pode asseverar o resultado de queries complexas com a abordagem
da Tabela Query, apenas especificando um nome de resultado com uma query e
comparando isso a um conjunto de dados:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ComplexQueryTest extends TestCase
    {
        use TestCaseTrait;

        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _database.asserting-the-state-of-multiple-tables:

Asseverando o Estado de Múltiplas Tabelas
=========================================

Certamente você pode asseverar o estado de múltiplas tabelas de uma vez e
comparar um conjunto de dados de query contra um conjunto de dados baseado em arquivo. Existem duas
formas diferentes para asserções de Conjuntos de Dados.

#.

   Você pode usar o Database (Banco de Dados - DB) e o DataSet (Conjunto de Dados) da Connection (Conexão) e
   compará-lo com um Conjunto de Dados Baseado em Arquivo.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSetAssertion()
           {
               $dataSet = $this->getConnection()->createDataSet(['guestbook']);
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

#.

   Você pode construir o Conjunto de Dados por si próprio:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testManualDataSetAssertion()
           {
               $dataSet = new PHPUnit_Extensions_Database_DataSet_QueryDataSet();
               $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // tabelas adicionais
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

.. _database.frequently-asked-questions:

Perguntas Mais Frequentes
#########################

.. _database.will-phpunit-re-create-the-database-schema-for-each-test:

O PHPUnit vai (re)criar o esquema do banco de dados para cada teste?
====================================================================

Não, o PHPUnit exige que todos os objetos do banco de dados estejam disponíveis quando a
suíte começar os testes. O Banco de Dados, tabelas, sequências, gatilhos e
visualizações devem ser criadas antes que você execute a suíte de testes.

`Doctrine 2 <http://www.doctrine-project.org>`_ ou
`eZ Components <http://www.ezcomponents.org>`_ possuem
ferramentas poderosas que permitem você criar o esquema de banco de dados através
de estruturas de dados pré-definidas. Entretanto, devem ser ligados à
extensão do PHPUnit para permitir a recriação automática de banco de dados
antes que a suíte de testes completa seja executada.

Já que cada teste limpa completamente o banco de dados, você nem sequer é
forçado a recriar o banco de dados para cada execução de teste. Um banco de dados
permanentemente disponível funciona perfeitamente.

.. _database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work:

Sou forçado a usar PDO em minha aplicação para que a Extensão para Banco de Dados funcione?
===========================================================================================

Não, PDO só é exigido para limpeza e configuração do ambiente e para
asserções. Você pode usar qualquer abstração de banco de dados que quiser
dentro de seu próprio código.

.. _database.what-can-i-do-when-i-get-a-too-much-connections-error:

O que posso fazer quando recebo um Erro “Too much Connections“?
===================================================================

Se você não armazena em cache a instância de PDO que é criada a partir do
método do Caso de Teste ``getConnection()`` o número de
conexões ao banco de dados é aumentado em um ou mais com cada
teste do banco de dados. Com a configuração padrão o MySQL só permite 100
conexões concorrentes e outros fornecedores também têm um limite máximo
de conexões.

A Sub-seção
“Use seu próprio Caso Abstrato de Teste de Banco de Dados“ mostra como
você pode prevenir o acontecimento desse erro usando uma instância única armazenada
em cache do PDO em todos os seus testes.

.. _database.how-to-handle-null-with-flat-xml-csv-datasets:

Como lidar com NULL usando Conjuntos de Dados XML Plano / CSV?
==============================================================

Não faça isso. Em vez disso, você deveria usar Conjuntos de Dados XML
ou YAML.


