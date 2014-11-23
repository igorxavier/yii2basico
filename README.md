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
            'showScriptName' => false
        ],
        'db' => require(__DIR__ . '/db.php'),
    ],
    'modules' => [
        'admin' => [
            'class' => 'app\modules\admin\Module',
            'layout' => 'teste',
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
