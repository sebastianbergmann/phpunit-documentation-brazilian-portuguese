

.. _risky-tests:

=================
Testes arriscados
=================

PHPUnit pode realizar verificações adicionais documentadas abaixo enquanto executa
os testes.

.. _risky-tests.useless-tests:

Testes Inúteis
##############

PHPUnit pode ser estrito sobre testes que não testam nada. Esta verificação
pode ser habilitada usando a opção ``--report-useless-tests``
na linha de comando ou pela definição
``beStrictAboutTestsThatDoNotTestAnything="true"`` no
arquivo de configuração XML do PHPUnit.

Um teste que não realiza uma afirmação irá ser marcado como arriscado
quando essa verificação está habilitada. Expectativas sobre objetos falsificados ou anotações
tais como @expectedException contam como uma asserção.

.. _risky-tests.unintentionally-covered-code:

Cobertura de Código Involuntária
################################

PHPUnit pode ser estrito sobre cobertura de código involuntária. Esta verificação
pode ser habilitada usando a opção ``--strict-coverage``
na linha de comando ou pela definição
``beStrictAboutCoversAnnotation="true"`` no
arquivo de configuração XML do PHPUnit.

Um teste que é anotado com @covers e executa código que
não está na lista utilizando uma anotação @covers ou @uses
será marcado como arriscado quando essa verificação é habilitada.

.. _risky-tests.output-during-test-execution:

Saída Durante a Execução de Teste
#################################

PHPUnit pode ser estrito sobre a saída durante os testes. Esta verificação pode ser habilitada
usando a opção ``--disallow-test-output`` na
linha de comando ou pela definição
``beStrictAboutOutputDuringTests="true"`` no
arquivo de configuração XML do PHPUnit.

Um teste que emite saída, por exemplo pela invocação de print
tanto no código de teste ou no código testado, será marcado como arriscado quando esta
verificação está habilitada.

.. _risky-tests.test-execution-timeout:

Tempo de Espera de Execução de Teste
####################################

Um limite de tempo pode ser forçado para a execução de um teste se o
pacote ``PHP_Invoker`` está instalado e a
extensão ``pcntl`` está disponível. A imposição deste
tempo de espera pode ser habilitado pelo uso da opção
``--enforce-time-limit`` na linha de comando ou pela definição
``beStrictAboutTestSize="true"`` no
arquivo de configuração XML do PHPUnit.

Um teste anotado com ``@large`` irá falhar se ele demorar
mais que 60 segundos para executar. Esse tempo de espera é configurável através
do atributo ``timeoutForLargeTests`` no arquivo
de configuração XML.

Um teste anotado com ``@medium`` irá falhar se ele demorar
mais que 10 segundos para executar. Esse tempo de espera é configurável através
do atributo ``timeoutForMediumTests`` no arquivo
de configuração XML.

Um teste que não é anotado com ``@medium`` ou
``@large`` será tratado como se fosse anotado com
``@small``. Um teste pequeno irá falhar se demorar mais que
1 segundo para executar. Esse tempo de espera é configurável através
do atributo ``timeoutForSmallTests`` no arquivo
de configuração XML.

.. _risky-tests.global-state-manipulation:

Manipulação do Estado Global
############################

PHPUnit pode ser estrito sobre testes que manipulam o estado global. Esta verificação
pode ser habilitada usando a opção ``--strict-global-state`` na
linha de comando ou pela definição
``beStrictAboutOutputDuringTests="true"`` no
arquivo de configuração XML do PHPUnit.


