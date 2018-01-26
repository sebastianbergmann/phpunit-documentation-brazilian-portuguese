

.. _test-doubles:

================
Dublês de Testes
================

Gerard Meszaros introduz o conceito de Dublês de Testes em
:ref:`Meszaros2007` desta forma:

    *Gerard Meszaros*:

    Às vezes é muito difícil testar o sistema sob teste (SST - em inglês: system under test - SUT)
    porque isso depende de outros ambientes que não podem ser usados no ambiente
    de testes. Isso pode ser porque não estão disponíveis, não retornarão
    os resultados necessários para o teste, ou porque executá-los
    causaria efeitos colaterais indesejáveis. Em outros casos, nossa estratégia de testes requer
    que tenhamos mais controle ou visibilidade do comportamento interno do SST.

    Quando estamos escrevendo um teste no qual não podemos (ou decidimos não) usar um componente
    dependente (depended-on component - DOC) real, podemos substituí-lo por um Dublê de Teste. O
    Dublê de Teste não precisa se comportar exatamente como o DOC real; apenas precisa
    fornecer a mesma API como o real, de forma que o SST pense que é
    o real!

Os métodos ``createMock($type)`` e
``getMockBuilder($type)`` fornecidos pelo PHPUnit podem
ser usados em um teste para gerar automaticamente um objeto que possa atuar como um dublê
de teste para a classe original especificada. Esse objeto de
dublê de teste pode ser usado em cada contexto onde um objeto da classe original
é esperado ou requerido.

O método ``createMock($type)`` imediatamente retorna um objeto de
dublê de teste para o tipo especificado (interface ou classe). A criação desse
dublê de teste é realizada usando os padrões de boas práticas (os métodos
``__construct()`` e ``__clone()`` da
classe original não são executados) e os argumentos passados para um método do
dublê de teste não serão clonados. Se esses padrões não são o que você precisa
então você pode usar o método ``getMockBuilder($type)`` para
customizar a geração do dublê de teste usando uma interface fluente.

Por padrão, todos os métodos da classe original são substituídos com uma implementação
simulada que apenas retorna ``null`` (sem chamar
o método original). Usando o método ``will($this->returnValue())``,
por exemplo, você pode configurar essas implementações simuladas para
retornar um valor quando chamadas.

.. admonition:: Limitações: métodos final, private e static

   Por favor, note que os métodos ``final``, ``private``
   e ``static`` não podem ser esboçados (stubbed) ou falsificados (mocked). Eles
   são ignorados pela funcionalidade de dublê de teste do PHPUnit e mantêm seus
   comportamentos originais.

.. _test-doubles.stubs:

Esboços (stubs)
###############

A prática de substituir um objeto por um dublê de teste que (opcionalmente)
retorna valores de retorno configurados é chamada de
*stubbing*. Você pode usar um *esboço* para
"substituir um componente real do qual o SST depende de modo que o teste tenha um
ponto de controle para as entradas indiretas do SST. Isso permite ao teste
forçar o SST através de caminhos que não seriam executáveis de outra forma".

:numref:`test-doubles.stubs.examples.StubTest.php` mostra como
esboçar chamadas de método e configurar valores de retorno. Primeiro usamos o
método ``createMock()`` que é fornecido pela
classe ``PHPUnit\Framework\TestCase`` para configurar um esboço
de objeto que parece com um objeto de ``SomeClass``
(:numref:`test-doubles.stubs.examples.SomeClass.php`). Então
usamos a `Interface Fluente <http://martinfowler.com/bliki/FluentInterface.html>`_
que o PHPUnit fornece para especificar o comportamento para o esboço. Essencialmente,
isso significa que você não precisa criar vários objetos temporários e
uni-los depois. Em vez disso, você encadeia chamadas de método como mostrado no
exemplo. Isso leva a códigos mais legíveis e "fluentes".

.. code-block:: php
    :caption: A classe que queremos esboçar
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SomeClass
    {
        public function doSomething()
        {
            // Faz algo.
        }
    }
    ?>

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar um valor fixo
    :name: test-doubles.stubs.examples.StubTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Chamando $stub->doSomething() agora vai retornar
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

.. admonition:: Limitações: Métodos nomeados como "method"

   O exemplo mostado acima só funciona quando a classe original não
   declara um método nomeado "method".

   Se a classe original declara um método nomeado "method" então ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');`` deve ser usado.

"Atrás dos bastidores" o PHPUnit automaticamente gera uma nova classe PHP que
implementa o comportamento desejado quando o método ``createMock()``
é usado.

:numref:`test-doubles.stubs.examples.StubTest2.php` mostra um
exemplo de como usar a interface fluente do Mock Builder para configurar a
criação do dublê de teste. A configuração desse dublê de teste usa
os mesmos padrões de boas práticas usados por ``createMock()``.

.. code-block:: php
    :caption: A API Mock Builder pode ser usada para configurar a classe de dublê de teste gerada
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->getMockBuilder($originalClassName)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // Configura o esboço.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Chamar $stub->doSomething() agora irá retornar
            // 'foo'.
            $this->assertEquals('foo', $stub->doSomething());
        }
    }
    ?>

Nos exemplos até agora temos retornado valores simples usando
``willReturn($value)``. Essa sintaxe curta é o mesmo que
``will($this->returnValue($value))``. Podemos usar variações
desta sintaxe longa para alcançar comportamento mais complexo de esboço.

Às vezes você quer retornar um dos argumentos de uma chamada de método
(inalterada) como o resultado de uma chamada ao método esboçado.
:numref:`test-doubles.stubs.examples.StubTest3.php` mostra como você
pode conseguir isso usando ``returnArgument()`` em vez de
``returnValue()``.

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar um dos argumentos
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnArgumentStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') retorna 'foo'.
            $this->assertEquals('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') retorna 'bar'.
            $this->assertEquals('bar', $stub->doSomething('bar'));
        }
    }
    ?>

Ao testar uma interface fluente, às vezes é útil fazer um método
esboçado retornar uma referência ao objeto esboçado.
:numref:`test-doubles.stubs.examples.StubTest4.php` mostra como você
pode usar ``returnSelf()`` para conseguir isso.

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar uma referência ao objeto esboçado
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnSelf()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() retorna $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }
    ?>

Algumas vezes um método esboçado deveria retornar valores diferentes dependendo de
uma lista predefinida de argumentos. Você pode usar
``returnValueMap()`` para criar um mapa que associa
argumentos com valores de retorno correspondentes. Veja
:numref:`test-doubles.stubs.examples.StubTest5.php` para
ter um exemplo.

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar o valor de um mapa
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnValueMapStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Cria um mapa de argumentos para valores retornados.
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() retorna diferentes valores dependendo do
            // argumento fornecido.
            $this->assertEquals('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertEquals('h', $stub->doSomething('e', 'f', 'g'));
        }
    }
    ?>

Quando a chamada ao método esboçado deve retornar um valor calculado em vez de
um fixo (veja ``returnValue()``) ou um argumento (inalterado)
(veja ``returnArgument()``), você pode usar
``returnCallback()`` para que o método esboçado retorne o
resultado da função ou método callback. Veja
:numref:`test-doubles.stubs.examples.StubTest6.php` para ter um exemplo.

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar um valor de um callback
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnCallbackStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) retorna str_rot13($argument)
            $this->assertEquals('fbzrguvat', $stub->doSomething('something'));
        }
    }
    ?>

Uma alternativa mais simples para configurar um método callback pode ser
especificar uma lista de valores de retorno desejados. Você pode fazer isso com
o método ``onConsecutiveCalls()``. Veja
:numref:`test-doubles.stubs.examples.StubTest7.php` para
ter um exemplo.

.. code-block:: php
    :caption: Esboçando uma chamada de método para retornar uma lista de valores na ordem especificada
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() retorna um valor diferente a cada vez
            $this->assertEquals(2, $stub->doSomething());
            $this->assertEquals(3, $stub->doSomething());
            $this->assertEquals(5, $stub->doSomething());
        }
    }
    ?>

Em vez de retornar um valor, um método esboçado também pode causar uma
exceção. :numref:`test-doubles.stubs.examples.StubTest8.php`
mostra como usar ``throwException()`` para fazer isso.

.. code-block:: php
    :caption: Esboçando uma chamada de método para lançar uma exceção
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testThrowExceptionStub()
        {
            // Cria um esboço para a classe SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Configura o esboço.
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() lança Exceção
            $stub->doSomething();
        }
    }
    ?>

Alternativamente, você mesmo pode escrever um esboço enquanto melhora
o design. Recursos amplamente utilizados são acessados através de uma única fachada,
então você pode substituir facilmente o recurso pelo esboço. Por exemplo,
em vez de ter chamadas diretas ao banco de dados espalhadas pelo código,
você tem um único objeto ``Database`` que implementa a interface
``IDatabase``. Então, você pode criar um esboço
de implementação da ``IDatabase`` e usá-la em seus
testes. Você pode até criar uma opção para executar os testes com o
esboço do banco de dados ou com o banco de dados real, então você pode usar seus testes tanto
para testes locais durante o desenvolvimento quanto para integração dos testes com o
banco de dados real.

Funcionalidades que precisam ser esboçadas tendem a se agrupar no mesmo
objeto, aumentando a coesão. Por apresentar a funcionalidade com uma
interface única e coerente, você reduz o acoplamento com o resto do
sistema.

.. _test-doubles.mock-objects:

Objetos Falsos (Mock Objects)
#############################

A prática de substituir um objeto por um dublê de teste que verifica
expectativas, por exemplo asseverando que um método foi chamado, é
conhecido como *falsificação (mocking)*.

Você pode usar um *objeto falso* "como um ponto de observação
que é usado para verificar as saídas indiretas do SST durante seu exercício.
Tipicamente, o objeto falso também inclui a funcionalidade de um esboço de teste
que deve retornar valores para o SST se ainda não tiver falhado
nos testes, mas a ênfase está na verificação das saídas indiretas.
Portanto, um objeto falso é muito mais que apenas um esboço de testes mais
asserções; é utilizado de uma forma fundamentalmente diferente".

.. admonition:: Limitações: Verificação automática de expectativas

   Somente objetos falsos gerados no escopo de um teste irá ser verificado
   automaticamente pelo PHPUnit. Objetos falsos gerados em provedores de dados, por
   exemplo, ou injetados dentro do teste usando a anotação
   ``@depends`` não serão verificados pelo PHPUnit.

Aqui está um exemplo: suponha que queiramos testar se o método correto,
``update()`` em nosso exemplo, é chamado em um objeto que
observa outro objeto. :numref:`test-doubles.mock-objects.examples.SUT.php`
mostra o código para as classes ``Subject`` e ``Observer``
que são parte do Sistema Sob Teste (SST).

.. code-block:: php
    :caption: As classes Subject e Observer que são parte do Sistema Sob Teste (SST)
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // Faz algo.
            // ...

            // Notifica aos observadores que fizemos algo.
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // Outros métodos.
    }

    class Observer
    {
        public function update($argument)
        {
            // Faz algo.
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // Faz algo.
        }

        // Outros métodos.
    }
    ?>

:numref:`test-doubles.mock-objects.examples.SubjectTest.php`
mostra como usar um objeto falso para testar a interação entre
os objetos ``Subject`` e ``Observer``.

Primeiro usamos o método ``getMockBuilder()`` que é fornecido pela
classe ``PHPUnit\Framework\TestCase`` para configurar um objeto
falso para ser o ``Observer``. Já que fornecemos um vetor como
segundo parâmetro (opcional) para o método ``getMock()``,
apenas o método ``update()`` da classe
``Observer`` é substituído por uma implementação falsificada.

Porque estamos interessados em verificar se um método foi chamado, e com quais
argumentos ele foi chamado, introduzimos os métodos ``expects()`` e
``with()`` para especificar como essa interação deve se parecer.

.. code-block:: php
    :caption: Testando se um método é chamado uma vez e com o argumento especificado
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            // Cria uma falsificação para a classe Observer,
            // apenas falsificando o método update().

            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['update'])
                             ->getMock();

            // Configura a expectativa para o método update()
            // para ser chamado apenas uma vez e com a string 'something'
            // como seu parâmetro.
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Cria um objeto Subject e anexa a ele o objeto
            // Observer falsificado.
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Chama o método doSomething() no objeto $subject
            // no qual esperamos chamar o método update()
            // do objeto falsificado Observer, com a string 'something'.
            $subject->doSomething();
        }
    }
    ?>

O método ``with()`` pode receber qualquer número de
argumentos, correspondendo ao número de argumentos
sendo falsos. Você pode especificar restrições mais avançadas
do que uma simples igualdade no argumento do método.

.. code-block:: php
    :caption: Testando se um método é chamado com um número de argumentos restringidos de formas diferentes
    :name: test-doubles.mock-objects.examples.MultiParameterTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Cria um mock para a classe Observer, falsificando o
            // método reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // O método doSomethingBad() deve reportar um erro ao observer
            // através do método reportError()
            $subject->doSomethingBad();
        }
    }
    ?>

O método ``withConsecutive()`` pode receber qualquer número de
vetores de argumentos, dependendo das chamadas que você desejar testar.
Cada vetor é uma lista de restrições correspondentes para os argumentos do
método falsificado, como em ``with()``.

.. code-block:: php
    :caption: Testar que um método foi chamado duas vezes com argumentos especificados
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments()
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }
    ?>

A restrição ``callback()`` pode ser usada para verificação de argumento
mais complexa. Essa restrição recebe um callback PHP como seu único
argumento. O callback PHP receberá o argumento a ser verificado como
seu único argumento e deverá retornar ``true`` se o
argumento passou a verificação e ``false`` caso contrário.

.. code-block:: php
    :caption: Verificação de argumento mais complexa
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Cria um mock para a classe Observer, falsificando o
            // método reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with($this->greaterThan(0),
                            $this->stringContains('Something'),
                            $this->callback(function($subject){
                              return is_callable([$subject, 'getName']) &&
                                     $subject->getName() == 'My subject';
                            }));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // O método doSomethingBad() deve reportar um erro ao observer
            // através do método reportError()
            $subject->doSomethingBad();
        }
    }
    ?>

.. code-block:: php
    :caption: Testando se um método foi chamado uma vez e com o objeto idêntico ao que foi passado
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }
    ?>

.. code-block:: php
    :caption: Cria um objeto falsificado com clonagem de parâmetros habilitada
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // Agora seu mock clona parâmetros tal que a restrição identicalTo
            // irá falhar.
        }
    }
    ?>

:ref:`appendixes.assertions.assertThat.tables.constraints`
mostra as restrições que podem ser aplicadas aos argumentos do método e
:numref:`test-doubles.mock-objects.tables.matchers`
mostra os comparados que estão disponíveis para especificar o número de
invocações.

.. rst-class:: table
.. list-table:: Comparadores
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - Comparador
      - Significado
    * - ``PHPUnit_Framework_MockObject_Matcher_AnyInvokedCount any()``
      - Retorna um comparador que corresponde quando o método que é avaliado for executado zero ou mais vezes.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount never()``
      - Retorna um comparador que corresponde quando o método que é avaliado nunca for executado.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedAtLeastOnce atLeastOnce()``
      - Retorna um comparador que corresponde quando o método que é avaliado for executado pelo menos uma vez.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount once()``
      - Retorna um comparador que corresponde quando o método que é avaliado for executado exatamente uma vez.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedCount exactly(int $count)``
      - Retorna um comparador que corresponde quando o método que é avaliado for executado exatamente ``$count`` vezes.
    * - ``PHPUnit_Framework_MockObject_Matcher_InvokedAtIndex at(int $index)``
      - Retorna um comparador que corresponde quando o método que é avaliado for invocado no ``$index`` fornecido.

.. admonition:: Note

   O parâmetro ``$index`` para o comparador ``at()``
   se refere ao índice, iniciando em zero, em *todas invocações
   de métodos* para um dado objeto falsificado. Tenha cuidado ao
   usar este comparador, pois pode levar a testes frágeis que são muito
   intimamente ligados a detalhes de implementação específicos.

As mentioned in the beginning, when the defaults used by the
``createMock()`` method to generate the test double do not
match your needs then you can use the ``getMockBuilder($type)``
method to customize the test double generation using a fluent interface.
Here is a list of methods provided by the Mock Builder:

-

  ``setMethods(array $methods)`` pode ser chamado no objeto Mock Builder para especificar os métodos que devem ser substituídos com um dublê de teste configurável. O comportamento dos outros métodos não muda. Se você chamar ``setMethods(null)``, então nenhum dos métodos serão substituídos.

-

  ``setConstructorArgs(array $args)`` pode ser chamado para fornecer um vetor de parâmetros que é passado ao construtor da classe original (que por padrão não é substituído com uma implementação falsa).

-

  ``setMockClassName($name)`` pode ser usado para especificar um nome de classe para a classe de dublê de teste gerada.

-

  ``disableOriginalConstructor()`` pode ser usado para desabilitar a chamada ao construtor da classe original.

-

  ``disableOriginalClone()`` pode ser usado para desabilitar a chamada ao construtor clone da classe original.

-

  ``disableAutoload()`` pode ser usado para desabilitar o ``__autoload()`` durante a geração da classe de dublê de teste.

.. _test-doubles.prophecy:

Profecia
########

`Prophecy <https://github.com/phpspec/prophecy>`_ é um
"framework PHP de falsificação de objetos muito poderoso e flexível, porém
altamente opcional. Embora inicialmente criado para atender as necessidades do phpspec2, ele é
flexível o suficiente para ser usado dentro de qualquer framework de teste por aí, com
o mínimo de esforço".

O PHPUnit tem suporte nativo para uso do Prophecy para criar dublês de testes.
:numref:`test-doubles.prophecy.examples.SubjectTest.php`
mostra como o mesmo teste mostrado no :numref:`test-doubles.mock-objects.examples.SubjectTest.php`
pode ser expressado usando a filosofia do Prophecy de profecias e
revelações:

.. code-block:: php
    :caption: Testando que um método foi chamado uma vez e com um argumento específico
    :name: test-doubles.prophecy.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            $subject = new Subject('My subject');

            // Cria uma profecia para a classe Observer.
            $observer = $this->prophesize(Observer::class);

            // Configura a expectativa para o método update()
            // para que seja chamado somente uma vez e com a string 'something'
            // como parâmetro.
            $observer->update('something')->shouldBeCalled();

            // Revela a profecia e anexa o objeto falsificado
            // ao Subject.
            $subject->attach($observer->reveal());

            // Chama o método doSomething() no objeto $subject
            // que esperamos que chame o método update() do objeto
            // Observer falsificado com a string 'something'.
            $subject->doSomething();
        }
    }
    ?>

Por favor, referencie a `documentação <https://github.com/phpspec/prophecy#how-to-use-it>`_
do Prophecy para mais detalhes sobre como criar, configurar, e usar
esboços, espiões, e falsificações usando essa alternativa de framework de dublê de teste.

.. _test-doubles.mocking-traits-and-abstract-classes:

Falsificando Traits e Classes Abstratas
#######################################

O método ``getMockForTrait()`` retorna um objeto falsificado
que usa uma trait especificada. Todos métodos abstratos de uma dada trait
são falsificados. Isto permite testar os métodos concretos de uma trait.

.. code-block:: php
    :caption: Testando os métodos concretos de uma trait
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class TraitClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }
    ?>

O método ``getMockForAbstractClass()`` retorna um objeto
falso para uma classe abstrata. Todos os métodos abstratos da classe abstrata fornecida
são falsificados. Isto permite testar os métodos concretos de uma
classe abstrata.

.. code-block:: php
    :caption: Testando os métodos concretos de uma classe abstrata
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }
    ?>

.. _test-doubles.stubbing-and-mocking-web-services:

Esboçando e Falsificando Serviços Web
#####################################

Quando sua aplicação interage com um serviço web você quer testá-lo
sem realmente interagir com o serviço web. Para tornar mais fácil o esboço
e falsificação dos serviços web, o ``getMockFromWsdl()``
pode ser usado da mesma forma que o ``getMock()`` (veja acima). A única
diferença é que ``getMockFromWsdl()`` retorna um esboço ou
falsificação baseado em uma descrição de um serviço web em WSDL e ``getMock()``
retorna um esboço ou falsificação baseado em uma classe ou interface PHP.

:numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php`
mostra como ``getMockFromWsdl()`` pode ser usado para esboçar, por
exemplo, o serviço web descrito em :file:`GoogleSearch.wsdl`.

.. code-block:: php
    :caption: Esboçando um serviço web
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GoogleTest extends TestCase
    {
        public function testSearch()
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() agora irá retornar um resultado esboçado e
             * o método doGoogleSearch() do serviço web não será invocado.
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }
    ?>

.. _test-doubles.mocking-the-filesystem:

Esboçando o Sistema de Arquivos
###############################

`vfsStream <https://github.com/mikey179/vfsStream>`_
é um `stream wrapper <http://www.php.net/streams>`_ para um
`sistema de arquivos virtual <http://en.wikipedia.org/wiki/Virtual_file_system>`_ que pode ser útil em testes unitários para falsificar um sistema
de arquivos real.

Simplesmente adicione a dependência ``mikey179/vfsStream`` ao seu
arquivo ``composer.json`` do projeto se você usa o
`Composer <https://getcomposer.org/>`_ para gerenciar as
dependências do seu projeto. Aqui é um exemplo simplório de um arquivo
``composer.json`` que apenas define uma dependência em ambiente de
desenvolvimento para o PHPUnit 4.6 e vfsStream:

.. code-block:: php

    {
        "require-dev": {
            "phpunit/phpunit": "~4.6",
            "mikey179/vfsStream": "~1"
        }
    }

:numref:`test-doubles.mocking-the-filesystem.examples.Example.php`
mostra a classe que interage com o sistema de arquivos.

.. code-block:: php
    :caption: Uma classe que interage com um sistema de arquivos
    :name: test-doubles.mocking-the-filesystem.examples.Example.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Example
    {
        protected $id;
        protected $directory;

        public function __construct($id)
        {
            $this->id = $id;
        }

        public function setDirectory($directory)
        {
            $this->directory = $directory . DIRECTORY_SEPARATOR . $this->id;

            if (!file_exists($this->directory)) {
                mkdir($this->directory, 0700, true);
            }
        }
    }?>

Sem um sistema de arquivos virtual tal como o vfsStream não poderíamos testar o
método ``setDirectory()`` isolado de influências
externas (veja :numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest.php`).

.. code-block:: php
    :caption: Testando uma classe que interage com o sistema de arquivos
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        protected function setUp()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(file_exists(dirname(__FILE__) . '/id'));

            $example->setDirectory(dirname(__FILE__));
            $this->assertTrue(file_exists(dirname(__FILE__) . '/id'));
        }

        protected function tearDown()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }
    }
    ?>

A abordagem acima tem várias desvantagens:

-

  Assim como um recurso externo, podem haver problemas intermitentes com o sistema de arquivos. Isso deixa os testes, com os quais interage, esquisitos.

-

  Nos métodos ``setUp()`` e ``tearDown()`` temos que assegurar que o diretório não existe antes e depois do teste.

-

  Quando a execução do teste termina antes do método ``tearDown()`` ser invocado, o diretório permanece no sistema de arquivos.

:numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest2.php`
mostra como o vfsStream pode ser usado para falsificar o sistema de arquivos em um teste para uma
classe que interage com o sistema de arquivos.

.. code-block:: php
    :caption: Falsificando o sistema de arquivos em um teste para uma classe que interage com o sistema de arquivos
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        public function setUp()
        {
            vfsStreamWrapper::register();
            vfsStreamWrapper::setRoot(new vfsStreamDirectory('exampleDir'));
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(vfsStreamWrapper::getRoot()->hasChild('id'));

            $example->setDirectory(vfsStream::url('exampleDir'));
            $this->assertTrue(vfsStreamWrapper::getRoot()->hasChild('id'));
        }
    }
    ?>

Isso tem várias vantagens:

-

  O próprio teste fica mais conciso.

-

  O vfsStream concede ao desenvolvedor de testes controle total sobre a aparência do ambiente do sistema de arquivos para o código testado.

-

  Já que as operações do sistema de arquivos não operam mais no sistema de arquivos real, operações de limpeza em um método ``tearDown()`` não são mais exigidas.


