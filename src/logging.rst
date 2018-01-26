

.. _logging:

===========
Registrando
===========

O PHPUnit pode produzir vários tipos de arquivos de registro (logfiles).

.. _logging.xml:

Resultados de Teste (XML)
#########################

O arquivo de registro XML para resultados de testes produzidos pelo PHPUnit é baseado naquele
usado pela `tarefa do
JUnit para Apache Ant <http://ant.apache.org/manual/Tasks/junit.html>`_. O seguinte exemplo mostra o arquivo de
registro XML gerado para os testes em ``ArrayTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="ArrayTest"
                 file="/home/sb/ArrayTest.php"
                 tests="2"
                 assertions="2"
                 failures="0"
                 errors="0"
                 time="0.016030">
        <testcase name="testNewArrayIsEmpty"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="6"
                  assertions="1"
                  time="0.008044"/>
        <testcase name="testArrayContainsAnElement"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="15"
                  assertions="1"
                  time="0.007986"/>
      </testsuite>
    </testsuites>

O seguinte arquivo de registro XML foi gerado por dois testes,
``testFailure`` e ``testError``,
de uma classe de caso de teste chamada ``FailureErrorTest`` e
mostra como falhas e erros são denotadas.

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="FailureErrorTest"
                 file="/home/sb/FailureErrorTest.php"
                 tests="2"
                 assertions="1"
                 failures="1"
                 errors="1"
                 time="0.019744">
        <testcase name="testFailure"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="6"
                  assertions="1"
                  time="0.011456">
          <failure type="PHPUnit_Framework_ExpectationFailedException">
    testFailure(FailureErrorTest)
    Failed asserting that &lt;integer:2&gt; matches expected value &lt;integer:1&gt;.

    /home/sb/FailureErrorTest.php:8
    </failure>
        </testcase>
        <testcase name="testError"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="11"
                  assertions="0"
                  time="0.008288">
          <error type="Exception">testError(FailureErrorTest)
    Exception:

    /home/sb/FailureErrorTest.php:13
    </error>
        </testcase>
      </testsuite>
    </testsuites>

.. _logging.codecoverage.xml:

Cobertura de Código (XML)
#########################

O formato XML para registro de informação de cobertura de código produzido pelo PHPUnit
é amplamente baseado naquele usado pelo `Clover <http://www.atlassian.com/software/clover/>`_. O seguinte exemplo mostra o arquivo de registro
XML gerado para os testes em ``BankAccountTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <coverage generated="1184835473" phpunit="3.6.0">
      <project name="BankAccountTest" timestamp="1184835473">
        <file name="/home/sb/BankAccount.php">
          <class name="BankAccountException">
            <metrics methods="0" coveredmethods="0" statements="0"
                     coveredstatements="0" elements="0" coveredelements="0"/>
          </class>
          <class name="BankAccount">
            <metrics methods="4" coveredmethods="4" statements="13"
                     coveredstatements="5" elements="17" coveredelements="9"/>
          </class>
          <line num="77" type="method" count="3"/>
          <line num="79" type="stmt" count="3"/>
          <line num="89" type="method" count="2"/>
          <line num="91" type="stmt" count="2"/>
          <line num="92" type="stmt" count="0"/>
          <line num="93" type="stmt" count="0"/>
          <line num="94" type="stmt" count="2"/>
          <line num="96" type="stmt" count="0"/>
          <line num="105" type="method" count="1"/>
          <line num="107" type="stmt" count="1"/>
          <line num="109" type="stmt" count="0"/>
          <line num="119" type="method" count="1"/>
          <line num="121" type="stmt" count="1"/>
          <line num="123" type="stmt" count="0"/>
          <metrics loc="126" ncloc="37" classes="2" methods="4" coveredmethods="4"
                   statements="13" coveredstatements="5" elements="17"
                   coveredelements="9"/>
        </file>
        <metrics files="1" loc="126" ncloc="37" classes="2" methods="4"
                 coveredmethods="4" statements="13" coveredstatements="5"
                 elements="17" coveredelements="9"/>
      </project>
    </coverage>

.. _logging.codecoverage.text:

Cobertura de Código (TEXT)
##########################

Saída de cobertura de código legível para linha-de-comando ou arquivo de texto.
O objetivo deste formato de saída é fornecer uma rápida visão geral de cobertura
enquanto se trabalha em um pequeno grupo de classes. Para projetos maiores esta saída
pode ser útil para conseguir uma rápida visão geral da cobertura do projeto ou quando usado com
a funcionalidade ``--filter``.
Quando usada da linha-de-comando escrevendo para ``php://stdout``
isso vai honrar a configuração ``--colors``.
Escrever na saída padrão é a opção padrão quando usado a partir da linha-de-comando.
Por padrão isso só vai mostrar arquivos que tenham pelo menos uma linha coberta.
Isso só pode ser alterado através da opção de configuração
xml ``showUncoveredFiles``. Veja :ref:`appendixes.configuration.logging`.
Por padrão todos arquivos e seus status de cobertura são mostrados no relatório detalhado.
Isso pode ser alterado através da opção de configuração
xml ``showOnlySummary``.


