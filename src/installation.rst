

.. _installation:

==================
Instalando PHPUnit
==================

.. _installation.requirements:

Requerimentos
#############

PHPUnit 6.4 requer PHP 7; Usar a última versão do PHP é altamente
recomendável.

PHPUnit requer as extensões `dom <http://php.net/manual/en/dom.setup.php>`_ e `json <http://php.net/manual/en/json.installation.php>`_,
que são normalmente habilitadas por padrão.

PHPUnit também requer as extensões
`pcre <http://php.net/manual/en/pcre.installation.php>`_,
`reflection <http://php.net/manual/en/reflection.installation.php>`_
e `spl <http://php.net/manual/en/spl.installation.php>`_.
Elas são requeridas pelo núcleo do PHP desde 5.3.0 e normalmente não podem
ser desabilitadas.

A funcionalidade de relatório de cobertura de código requer as extensões
`Xdebug <http://xdebug.org/>`_ (2.5.0 or later) e
`tokenizer <http://php.net/manual/en/tokenizer.installation.php>`_.
Geração de relatórios XML requer a extensão
`xmlwriter <http://php.net/manual/en/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

A maneira mais fácil de obter o PHPUnit é baixar um `PHP Archive (PHAR) <http://php.net/phar>`_ que tem todas dependências
requeridas (assim como algumas opcionais) do PHPUnit empacotados em um único
arquivo.

A extensão `phar <http://php.net/manual/en/phar.installation.php>`_
é requerida para usar PHP Archives (PHAR).

Se a extensão `Suhosin <http://suhosin.org/>`_ está
habilitada, você precisa permitir a execução dos PHARs em seu
``php.ini``:

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

Para instalar globalmente o PHAR:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-6.4.phar
    $  chmod +x phpunit-6.4.phar
    $  sudo mv phpunit-6.4.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

Você pode também usar o arquivo PHAR baixado, diretamente:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-6.4.phar
    $  php phpunit-6.4.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

A instalção global do PHAR envolve o mesmo procedimento que
`a instalação manual do Composer no Windows <https://getcomposer.org/doc/00-intro.md#installation-windows>`_:

#.

   Crie um diretório para os binários PHP; e.g., :file:`C:\\bin`

#.

   Acrescente ;C:\bin à sua variável de ambiente
   ``PATH``
   (`ajuda relacionada <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_)

#.

   Baixe `<https://phar.phpunit.de/phpunit.phar>`_ e
   salve o arquivo como :file:`C:\\bin\\phpunit.phar`

#.

   Abra uma linha de comando (e.g.,
   pressione :kbd:`Windows`:kbd:`R`
   » digite cmd
   » :kbd:`ENTER`)

#.

   Crie um script batch envoltório (resulta em
   :file:`C:\\bin\\phpunit.cmd`):

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   Abra uma nova linha de comando e confirme que você pode executar PHPUnit
   de qualquer caminho:

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

Para ambientes shell Cygwin e/ou MingW32 (e.g., TortoiseGit), você
pode pular o passo 5 acima, simplesmente salve o arquivo como
:file:`phpunit` (sem a extensão :file:`.phar`),
e torne-o executável via
chmod 775 phpunit.

.. _installation.phar.verification:

Verificando lançamentos do PHAR PHPUnit
=======================================

Todos lançamentos oficiais do código distribuído pelo Projeto PHPUnit são
assinados pelo gerenciador de lançamentos para o lançamento. Assinaturas PGP e hashes SHA1
estão disponíveis para verificação no `phar.phpunit.de <https://phar.phpunit.de/>`_.

O exemplo a seguir detalha como a verificação de lançamento funciona. Começamos
baixando :file:`phpunit.phar` bem como sua
assinatura PGP independente :file:`phpunit.phar.asc`:

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

Queremos verificar o PHP Archive do PHPUnit (:file:`phpunit.phar`)
contra sua assinatura independente (:file:`phpunit.phar.asc`):

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

Nós não temos a chave pública do gerenciador de lançamento (``6372C20A``)
no próprio sistema local. A fim de prosseguir com a verificação nós precisamos
recuperar a chave pública do gerenciador de lançamentos a partir de um servidor de chaves.
Um tal servidor é :file:`pgp.uni-mainz.de`. Os servidores de chaves públicas
são conectados entre si, então você deve ser capaz de se conectar a qualquer servidor de chaves.

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

Agora recebemos uma chave pública para uma entidade conhecida como "Sebastian
Bergmann <sb@sebastian-bergmann.de>". Porém, nós não temos nenhuma maneira
de verificar que essa foi criada pela pessoa conhecida como Sebastian
Bergmann. Mas, vamos tentar verificar a assinatura de lançamento novamente.

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

Neste ponto, a assinatura é boa, mas não confiamos nesta chave. Uma
boa assinatura significa que o arquivo não foi adulterado. Porém, devido
a natureza da criptografia de chave pública, você precisa adicionalmente
verificar que a chave ``6372C20A`` foi criada pelo verdadeiro
Sebastian Bergmann.

Qualquer invasor pode criar uma chave pública e enviá-la para os servidores
de chave pública. Eles podem, então, criar um lançamento malicioso assinado pela
chave fake. Tal que, se você tentar verificar a assinatura desse lançamento corrompido,
terá sucesso porque a chave não é a chave "verdadeira". Portanto, você
precisa validar a autenticidade dessa chave. Validar a
autenticidade de uma chave pública, no entanto, está fora do escopo desta
documentação.

Pode ser prudente criar um script shell para gerenciar a instalação do PHPUnit
que verifica a assinatura GnuPG antes de rodar sua suíte de teste. Por
exemplo:

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # Delete phpunit.phar after the tests are complete?
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # Let's clean them up, if they exist
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # Let's grab the latest release and its signature
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # Verify before running
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # Run the testing suite
        `$after_cmd`
        # Cleanup
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

Simplesmente adicione uma dependência (em desenvolvimento) ``phpunit/phpunit``
ao arquivo ``composer.json`` do projeto
se você usa `Composer <https://getcomposer.org/>`_ para gerenciar as
dependências do seu projeto:

.. code-block:: bash

    composer require --dev phpunit/phpunit ^6.4

.. _installation.optional-packages:

Pacotes opcionais
#################

Os seguintes pacotes opcionais estão disponíveis:

``PHP_Invoker``

    A classe utilitária para invocar callables com um tempo limite. Este pacote é
    requerido para impor limites de tempo de teste no modo estrito.

    Este pacote está incluso na distribuição PHAR do PHPUnit. Ele pode ser
    instalado via Composer usando o seguinte comando:

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    Porta do DbUnit para PHP/PHPUnit suportar teste de interação de banco de dados.

    Este pacote está incluso na distribuição PHAR do PHPUnit. Ele pode
    ser instalado via Composer usando o seguinte comando:

    .. code-block:: bash

        composer require --dev phpunit/dbunit


