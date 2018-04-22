# Traduzindo as mensagens de erro

[Link para o screencast no YouTube](https://www.youtube.com/watch?v=0b-glTSPwck)

## Crie o arquivo das mensagens em português brasileiro
- resources/lang/pt-br/validation.php
```php
<?php

return [


    'numeric'              => 'O campo :attribute deve ser numérico.',
    'required'             => 'O campo :attribute é obrigatório.',
    'url'                  => 'O campo :attribute deve ser uma URL válida.',

];
```

## Configure o idioma da aplicação
- Altere o atributo "locale" em config/app.php
```php
'locale' => 'pt-br',
```

## Fonte de estudo - vá além
[Laravel 5.6 - Localization](https://laravel.com/docs/5.6/localization)
