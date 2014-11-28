# Curso de Yii2 básico

### Configurando o Ubuntu 14.04

###### Instalando Apache2, PHP5(+extensões necessárias) e Mysql.

```sh
sudo apt-get install apache2 php5 php5-intl php5-mcrypt php5-curl php5-mysql mysql-client mysql-server
```
Durante a instalação será solicitado que você entre com o nome de admin para o Mysql, deixe o padrão(root), também será solicitada a senha, **NÃO** deixe esta senha em branco em hipótese alguma, pois diferente de quando você instala este pacote no Windows(Ex. WAMP), que permite o uso de uma senha em branco, aqui no linux você terá sérios problemas.

###### Instalando o PhpMyAdmin.

```sh
sudo apt-get install phpmyadmin
```
Aqui será solicitado que você entre com a senha configurada para o Mysql.

> Se você não é familiarizado com a utilização do terminal para navegar nos arquivos do linux sugiro que que rode também o seguinte comando:

```sh
sudo apt-get install nautilus-open-terminal
```
Após ter rodado os comandos acima reinicie seu computador, ao retornar ao sistema você já estara com seu sistema em uma configuração mínima necessária para trabalhar-mos com o Yii2. Agora você deve buscar através do gerenciador de arquivos o seguinte caminho:

Clique em Computador /var/www/html

Esta pasta será equivalente a pasta www em um sistema que utilize o WAMP.

Agora dentro desta pasta vamos trabalhar com nosso projeto.

Dentro desta pasta clique com o botão direito do mouse e 'Abrir num terminal'

Agora o terminal será aberto já com este caminho referêciado.
Você deve ter algo semelhante a isto:

```sh
@SeuComputador:/var/www/html$
```
<hr />

### Instalando o Composer

```sh
curl -s http://getcomposer.org/installer | php
```
```sh
mv composer.phar /usr/local/bin/composer
```

O Composer nada mais é que um instalador de dependências!
Com ele vamos instalar e atualizar lib’s de um determinado framework ou ferramenta, de forma a rápida e mantendo nosso sitema atualizado, além disso o composer gera um autoload de nossas dependências.

Veja mais sobre o Compoer: [https://getcomposer.org/](https://getcomposer.org/)

<hr />

### Instalando o Yii

```sh
composer global require "fxp/composer-asset-plugin:1.0.0-beta3"
```
```sh
composer create-project --prefer-dist yiisoft/yii2-app-basic curso
```

###### Testando nossa instalação

Se você ainda esta no seguinte caminho:

```sh
@SeuComputador:/var/www/html$
```
Então acesse a pasta pública do yii onde vamos rodar o servidor embutido do PHP.

```sh
cd curso/web
```
E Agora vamos levantar o servidor embutido.

```sh
php -S 127.0.0.1:8000
```

Agora pressione a tecla CTRL e clique sobre o ip 127.0.0.1:8000 no terminal, ou digite este ip no seu navegador.

>
>Se tudo correu bem devemos ter uma configuração básica do Yii rodando neste momento.
>

<hr />

### Configurações do nosso projeto do curso.

###### Configurando o acesso ao banco de dados

Vamos configurar nosso banco no arquivo **db.php** que esta localizado na pasta **config**.
Basicamente neste arquivo vamos informar a senha de acesso ao nosso mysql, e também o nome do nosso banco de dados.

```php
return [
    'class' => 'yii\db\Connection',
    'dsn' => 'mysql:host=localhost;dbname= >>>>>> NOME DO NOSSO BANCO <<<<<< ',
    'username' => 'root',
    'password' => ' >>>>>> SENHA CONFIGURADA PARA ACESSO AO MYSQL <<<<<< ',
    'charset' => 'utf8',
];
```

###### Configurando módulos e componentes do Yii

O arquivo **web.php** que esta localizado na pasta **config**, vai receber algumas das principais configurações do nosso projeto.

Estou deixando algumas cofigurações já feitas, mas observe nos comentários que temos uma ordem, então vamos implementando uma configuração por vez.

```php
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'defaultRoute' => 'site',
    'language' => 'pt-BR',
    'components' => [
        'request' => [
            'cookieValidationKey' => 'gkakV-1d1kf3qH0mdMDgTF0l8jXuu2p2',
        ],
        'cache' => [
            'class' => 'yii\caching\FileCache',
        ],
        'errorHandler' => [
            'errorAction' => 'site/error',
        ],
        'mailer' => [
            'class' => 'yii\swiftmailer\Mailer',
            'useFileTransport' => FALSE,
            'transport' => [
                'class' => 'Swift_SmtpTransport',
                'host' => 'smtp.gmail.com',
                'username' => 'seu-email@gmail.com',
                'password' => ' ****** ',
                'port' => '465',
                'encryption' => 'ssl',
            ],
        ],
        'log' => [
            'traceLevel' => YII_DEBUG ? 3 : 0,
            'targets' => [
                [
                    'class' => 'yii\log\FileTarget',
                    'levels' => ['error', 'warning'],
                ],
            ],
        ],
        // 1 - Primeiro componente a ser ativado, URL's amigáveis. 
        'urlManager' => [
            'class' => 'yii\web\UrlManager',
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules'=>[
                //Reescrevendo a url para sumir com o default da url
                '<modulo:\w+>/<submodulo:\w+>/<action:\w+>' => '<modulo>/<submodulo>/default/<action>',
            ]
        ],
        'db' => require(__DIR__ . '/db.php'),
    ],
    // 2 - Nossa sengunda configuração, criamos o array MODULES e ativamos o módulo admin. 
    'modules' => [
        'admin' => [
            'class' => 'app\modules\admin\Module',
            // 3 - Na terceira configuração, adicionamos os módulos categorias e cursos dentro do módulo admin. 
            'modules' => [
                'categorias' => [
                    'class' => 'app\modules\admin\modules\categorias\Module',
                ],
                'cursos' => [
                    'class' => 'app\modules\admin\modules\cursos\Module',
                ],
            ],
        ],
        'user' => [
            'class' => 'dektrium\user\Module',
            'admins' => ['igor']
        ],
    ],
];
```

<hr />

###### Primeira configuração

A primeira configuração que vamos fazer no arquivo acima, é a que em resumo, ativa um componente para que tenhamos URL's amigáveis.

Após fazer esta configuração devemos criar um arquivo .htaccess dentro da pasta **web** do nosso projeto, com a seguinte configuração:

```
RewriteEngine on

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d 

RewriteRule . index.php
```

### Criando módulo admin

Vamos agora trabalhar com o **gii**, um gerador de códigos que acompanha o Yii e facilita muito nosso trabalho.

Se você estiver utilizando o mesmo IP que utilizamos para levantar nosso servidor embutido do PHP,
Acesse a seguite url:

```
http://127.0.0.1:8000/gii
```

Clique em **Module Generator**, posicione o mouse sobre o label Module Class e observe a recomendação.
Neste campo vamos utilizar exatamente esta recomendação para nosso módulo admin.

```
app\modules\admin\Module
```
O campo Module ID será preenchido com:
```
admin
```

###### Segunda configuração

Assim que geramos nosso módulo será exibido o código para ativalo em nosso sistema.

```php
<?php
    ......
    'modules' => [
        'admin' => [
            'class' => 'app\modules\admin\Module',
        ],
    ],
    ......

```

Veja no arquivo que tinhamos com algumas configuraçõs prontas a localização exata onde este código deve ser inserido. 

>**OBS:** Respeite a posição do código dentro do array.

### Criando módulos categorias e cursos

Novamente utilizando o **gii** vamos criar em **Module Generator** os módulos, categorias e cursos.

Nosso **web.php** em **modules** deverá ficar da seguinte forma:

###### Terceira configuração

```php
'modules' => [
        'admin' => [
            'class' => 'app\modules\admin\Module',
            'modules' => [
                'categorias' => [
                    'class' => 'app\modules\admin\modules\categorias\Module',
                ],
                'cursos' => [
                    'class' => 'app\modules\admin\modules\cursos\Module',
                ],
            ],
        ],
    ],
```

### Criando models, Categoria e Curso

Ainda no **gii** vamos em Model Generator, aqui vamos preencher o campo **Table name** extamente com o nome da tabela criada em nosso banco de dados, o nome da nossa classe já deve surgir no campo **Model Class**, cabe apenas que cuidemos para manter o nome desta classe no singular seguindo um padrão de desenvolvimento.

>OBS: Logo abaixo do campo Model Class temos o **Namespace**, este campo aparece preenchido e desabilitado, vamos então clicar nele e indicar o namespace correto para cada um de nossos módulos:

```
app\modules\admin\modules\categorias\models
```
```
app\modules\admin\modules\cursos\models
```
### Gerando o CRUD

Agora é a vez de gerarmos nossos CRUD's para categorias e cursos.
Vamos ter caminhos bastante longos para informar ao nosso projeto, mas com atenção este método se torna fácil e vale muito a pena. Vejamos:

O primeiro campo é o **Model Class** onde vamos informar o caminho dos models criados no passo anterior, no nosso caso ficaria assim:
```
app\modules\admin\modules\categorias\models\Categoria
```
Agora temos que indicar um caminho para a **Search Model Class**, esta classe vai gerar algumas buscas automáticamente, para que possamos encontrar mais facilmente algum curso ou categoria na listagem. Este passo é opcional mas recomendado.

```
app\modules\admin\modules\categorias\models\CategoriaSearch
```
No próximo passo vamos fazer algo bem importante, observe nas pastas criadas quando criamos nosso módulo que ele gerou na pasta controllers do nosso módulo um arquivo DefaultController.php, assim mesmo sem um model e sem o crudo já era possível acessar o módulo e nos era exibido uma página index do módulo com a indicaçao do caminho para altera-la. Porém como queremos logo ao digitar nossosite/admin/categorias visualizar diretamente uma listagem com nossas categorias, durante a criação do nosso CRUD vamos sobrescrever esse Controller.
Então no campo **Controller Class**, teremos o seguinte:
```
app\modules\admin\modules\categorias\controllers\DefaultController
```
Estamos quase lá, o próximo passo é alterar o **Module ID**, que esta preenchido e desabilitado. Vamos clicar neste campo e alterar o ID para:
```
admin/categorias
```
Tudo pronto mas **ATENÇÃO**, agora quando clicarmos em Generate observe que dois arquivos serão marcados como já existentes, devemos selecionar estes dois arquivos para que sejam sobrescritos, e alterados.

### Arquivos javascript ou css em módulos

Para que possamos trabalhar com arquivos de script, ou de folhas de estilo, por exemplo, precisamos entender que utilizando estes arquivos dentro de nossos módulos simplesmente, eles ficariam inacessíveis por questão de segurança, visto nada além do que esta na pasta web deve ter permissão de acesso.

Para isso existe uma solução no Yii que esta nos Assets, assim o sistema irá mapear os assets informados por nós e ira criar um atalho, digamos assim, de forma a tornar este arquivos acessíveis de forma segura.

###### Criando uma classe Asset para o módulo

```php
namespace app\modules\admin\modules\imagens\assets;

use yii\web\AssetBundle;

class MeuAsset extends AssetBundle
{
    // Aqui informamos o caminho do nosso asset
    public $sourcePath  = '@app/modules/admin/modules/imagens/assets';
    
    // Agora os arquivos, dentro da pasta asset.
    public $css = [
        
    ];
    
    public $js = [
        'js/teste.js'
    ];
    
    public $depends = [
        'yii\web\YiiAsset',
        'yii\bootstrap\BootstrapAsset',
    ];
    
    // Em desenvolvimento devemos usar esta função para desativar o cache deste asset.
    public $publishOptions = ['forceCopy' => true];
    
}
```
Neste exemplo criei a Classe **MeuAsset** dentro de uma pasta assets, tudo dentro do módulo imagens, e dentro desta pasta assets, outras duas pastas chamadas de js e css.

###### Registrando o asset.

Para que possamos definitivamente utilzar os scripts e estilos, precisamos agora, usar a classe criada.

```php

use app\modules\admin\modules\imagens\assets\MeuAsset;

// Registrando nosso asset
MeuAsset::register($this);

```

Com o código acima estamos dando um use na classe e também registrando o asset no sistema.
Pronto agora basta criarmos nossos arquivos dentro das pastas js e css, sempre definindo o caminho destes dentro da classe.
