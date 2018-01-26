

.. _testing-practices:

=================
Práticas de Teste
=================

    *Erich Gamma*:

    Você sempre pode escrever mais testes. Porém, você vai descobrir rapidamente
    que apenas uma fração dos testes que você pode imaginar são realmente úteis. O que
    você quer é escrever testes que falham mesmo quando você acha que eles deveriam
    funcionar, ou testes que passam mesmo quando você acha que eles deveria falhar.
    Outra forma de pensar sobre isso é com relação ao custo/benefício. Você quer escrever
    testes que lhe darão retorno com informação.

.. _testing-practices.during-development:

Durante o Desenvolvimento
#########################

Quando você precisa fazer uma mudança na estrutura interna do programa
em que está trabalhando para torná-lo mais fácil de entender e mais barato de modificar
sem alterar seu comportamento visível, uma suíte de testes é inestimável na
aplicação das assim chamadas `refatorações <http://martinfowler.com/bliki/DefinitionOfRefactoring.html>`_
seguras. De outra forma, você poderia não notar o sistema quebrando enquanto você
está cuidando da reestruturação.

As seguintes condições vão ajudá-lo a melhorar o código e design
do seu projeto, enquanto usa testes unitários para verificar que os passos de transformação
da refatoração são, de fato, preservadores de comportamento e não
introduzem erros:

#.

   Todos os testes unitários são executados corretamente.

#.

   O código comunica seus princípios de design.

#.

   O código não contém redundâncias.

#.

   O código contém o mínimo número de classes e métodos.

Quando você precisar adicionar novas funcionalidades ao sistema, escreva os testes
primeiro. Então, você terá terminado de desenvolver quando os testes executarem. Esta
prática será discutida em detalhes no próximo capítulo.

.. _testing-practices.during-debugging:

Durante a Depuração
###################

Quando você recebe um relatório de defeito, seu impulso pode ser consertar o defeito
o mais rápido possível. A experiência mostra que esse impulso não vai lhe
servir bem; é provável que o conserto do defeito acaba causando outro
defeito.

Você pode conter esses seus impulsos fazendo o seguinte:

#.

   Verifique que você pode reproduzir o defeito.

#.

   Encontre a demonstração em menor escala do defeito no código.
   Por exemplo, se um número aparece incorretamente em uma saída, encontre o
   objeto que está calculando esse número.

#.

   Escreva um teste automatizado que falha agora, mas vai passar quando o
   defeito for consertado.

#.

   Conserte o defeito.

Encontrar a menor reprodução confiável do defeito vai te dar a
oportunidade de examinar realmente a causa do defeito. O teste que você
escreve vai melhorar as chances de que, quando você corrigir o defeito, você realmente
terá corrigido-o, porque o novo teste reduz a probabilidade de desfazer a correção
com futuras modificações no código. Todos os testes que você escreveu antes reduzem a
probabilidade de causar diferentes problemas inadvertidamente.

    *Benjamin Smedberg*:

    Testes unitários oferecem muitas vantagens:

    -

      Testar dá aos autores e revisores de código confiança de que remendos produzem os resultados corretos.

    -

      Criar casos de testes é um bom ímpeto para desenvolvedores descobrirem casos extremos.

    -

      Testar fornece uma boa maneira de capturar regressões rapidamente, e ter certeza de que nenhuma regressão será repetida duas vezes.

    -

      Testes unitários fornecem exemplos funcionais de como usar uma API e podem auxiliar significativamente os trabalhos de documentação.

    No geral, testes unitários integrados reduzem o custo e o risco de qualquer
    mudança individual menor. Isso permitirá que o projeto faça \[...]
    maiores mudanças arquitetônicas \[...] rápida e confiavelmente.


