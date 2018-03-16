

.. _extending-phpunit:

====================
Estendendo o PHPUnit
====================

O PHPUnit pode ser estendido de várias formas para facilitar a escrita de testes
e personalizar as respostas que você recebe ao executar os testes. Aqui estão
pontos de partida comuns para estender o PHPUnit.

.. _extending-phpunit.PHPUnit_Framework_TestCase:

Subclasse PHPUnit\\Framework\\TestCase
######################################

Escreva asserções personalizadas e métodos utilitários em uma subclasse abstrata de
``PHPUnit\Framework\TestCase`` e derive suas classes de caso de teste
dessa classe. Essa é uma das formas mais fáceis de estender
o PHPUnit.

.. _extending-phpunit.custom-assertions:

Escreva asserções personalizadas
################################

Ao escrever asserções personalizadas, a melhor prática é seguir a mesma
forma que as asserções do próprio PHPUnit são implementadas. Como você pode ver no
:numref:`extending-phpunit.examples.Assert.php`, o método
``assertTrue()`` é apenas um invólucro em torno dos métodos
``isTrue()`` e ``assertThat()``:
``isTrue()`` cria um objeto comparador que é passado para
``assertThat()`` para avaliação.

.. code-block:: php
    :caption: Os métodos assertTrue() e isTrue() da classe PHPUnit_Framework_Assert
    :name: extending-phpunit.examples.Assert.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class PHPUnit_Framework_Assert
    {
        // ...

        /**
         * Assevera que uma condição é verdadeira (true).
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit_Framework_AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Retorna um objeto comparador PHPUnit_Framework_Constraint_IsTrue.
         *
         * @return PHPUnit_Framework_Constraint_IsTrue
         * @since  Método disponível desde a versão 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit_Framework_Constraint_IsTrue;
        }

        // ...
    }?>

O :numref:`extending-phpunit.examples.IsTrue.php` mostra como
``PHPUnit_Framework_Constraint_IsTrue`` estende a
classe base abstrata para objetos comparadores (ou objetos-restrições),
``PHPUnit_Framework_Constraint``.

.. code-block:: php
    :caption: A classe PHPUnit_Framework_Constraint_IsTrue
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class PHPUnit_Framework_Constraint_IsTrue extends PHPUnit_Framework_Constraint
    {
        /**
         * Avalia a restrição para o parâmetro $other. Retorna true se a
         * restrição é confirmada, false caso contrário.
         *
         * @param mixed $other Valor ou objeto a avaliar.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * Retorna uma representação string da restrição.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }?>

O esforço de implementar os métodos ``assertTrue()`` e
``isTrue()`` assim como a classe
``PHPUnit_Framework_Constraint_IsTrue`` rende o
benefício de que ``assertThat()`` automaticamente cuida de
avaliar a asserção e escriturar tarefas tais como contá-las para
estatísticas. Além disso, o método ``isTrue()`` pode ser
usado como um comparador ao configurar objetos falsificados.

.. _extending-phpunit.PHPUnit_Framework_TestListener:

Implementando PHPUnit\\Framework\\TestListener
##############################################

O :numref:`extending-phpunit.examples.SimpleTestListener.php`
mostra uma implementação simples da interface
``PHPUnit\Framework\TestListener``.

.. code-block:: php
    :caption: Um simples ouvinte de teste
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time)
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit_Framework_Test $test)
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }
    ?>

:numref:`extending-phpunit.examples.BaseTestListener.php`
mostra como a subclasse da classe abstrata ``PHPUnit_Framework_BaseTestListener``,
que permite que você especifique apenas os métodos de interface que
são interessantes para seu caso de uso, ao fornecer implementações vazias
para todos os outros.

.. code-block:: php
    :caption: Usando o ouvinte de teste base
    :name: extending-phpunit.examples.BaseTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ShortTestListener extends PHPUnit_Framework_BaseTestListener
    {
        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }
    ?>

Em :ref:`appendixes.configuration.test-listeners` você pode ver
como configurar o PHPUnit para anexar seu ouvinte de teste para a
execução do teste.

.. _extending-phpunit.PHPUnit_Framework_Test:

Implementando PHPUnit_Framework_Test
####################################

A interface ``PHPUnit_Framework_Test`` é limitada e
fácil de implementar. Você pode escrever uma implementação do
``PHPUnit_Framework_Test`` que é mais simples que
``PHPUnit\Framework\TestCase`` e que executa
*testes guiados por dados*, por exemplo.

O :numref:`extending-phpunit.examples.DataDrivenTest.php`
mostra uma classe de caso de teste guiado por dados que compara valores de um arquivo
com Valores Separados por Vírgula (CSV). Cada linha de tal arquivo parece com
``foo;bar``, onde o primeiro valor é o qual esperamos
e o segundo valor é o real.

.. code-block:: php
    :caption: Um teste guiado por dados
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit_Framework_Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit_Framework_TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit_Framework_TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit_Framework_Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit_Framework_AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit_TextUI_TestRunner::run($test);
    ?>

.. code-block:: bash

    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.


