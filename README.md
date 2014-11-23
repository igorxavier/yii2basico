# Curso de Yii2 básico

### Configurando o Ubuntu 14.04

###### Instalando Apache2, PHP5(+extensões necessárias) e Mysql.

```sh
$ sudo apt-get install apache2 php5 php5-intl php5-mcrypt php5-curl php5-mysql mysql-client mysql-server

```
Durante a instalação será solicitado que você entre com o nome de admin para o Mysql, deixe o padrão(root), também será solicitada a senha, **NÃO** deixe esta senha em branco em hipótese alguma, pois diferente de quando você instala este pacote no Windows(Ex. WAMP), que permite o uso de uma senha em branco, aqui no linux você terá sérios problemas.

###### Instalando o PhpMyAdmin.

```sh
$ sudo apt-get install phpmyadmin

```
Aqui será solicitado que você entre com a senha configurada para o Mysql.

> Se você não é familiarizado com a utilização do terminal para navegar nos arquivos do linux sugiro que que rode também o seguinte comando:

```sh
$ sudo apt-get install nautilus-open-terminal

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
$ curl -s http://getcomposer.org/installer | php

$ mv composer.phar /usr/local/bin/composer

```

O Composer nada mais é que um instalador de dependências!
Com ele vamos instalar e atualizar lib’s de um determinado framework ou ferramenta, de forma a rápida e mantendo nosso sitema atualizado, além disso o composer gera um autoload de nossas dependências.

Veja mais sobre o Compoer: [https://getcomposer.org/](https://getcomposer.org/)

<hr />

### Instalando o Yii

```sh
$ composer global require "fxp/composer-asset-plugin:1.0.0-beta3"

$ composer create-project --prefer-dist yiisoft/yii2-app-basic curso

```

###### Testando nossa instalação

Se você ainda esta no seguinte caminho:

```sh
@SeuComputador:/var/www/html$

```
Então acesse a pasta pública do yii onde vamos rodar o servidor embutido do PHP.

```sh
$ cd yii/web

```
E Agora vamos levantar o servidor embutido.

```sh
$ php -S 127.0.0.1:8000

```

Agora pressione a tecla CTRL e clique sobre o ip 127.0.0.1:8000 no terminal, ou digite este ip no seu navegador.

>
>Se tudo correu bem devemos ter uma configuração básica do Yii rodando neste momento.
>

<hr />

### Configurações do nosso projeto do curso.

```php
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'defaultRoute' => 'site',
    'language' => 'pt-BR',
    'components' => [
//        'authManager' => [
//            'class' => 'yii\rbac\DbManager',
//        ],
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
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
