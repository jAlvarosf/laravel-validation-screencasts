# Criando regras personalizadas
[Link para o screencast no YouTube](https://www.youtube.com/watch?v=b6jlJL2gMKQ)

## Crie uma nova regra
Exemplo simples e não muito útil, mas eficiente para enterdermos as regras personalizadas.
```php
php artisan make:rule Maiuscula
```

## Implemente a regra na nova classe app/Rules/Maiuscula.php
```php
<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class Maiuscula implements Rule
{
    public function __construct()
    {
        //
    }

    public function passes($attribute, $value)
    {
        return strtoupper($value) === $value;
    }

    public function message()
    {
        return 'O campo :attribute deve ter somente letras maiúsculas.';
    }
}
```

## Importe a nova regra no LivroRequest e coloque a nova lógica de validação do campo "autor"
- Arquivo app/Http/Requests/LivroRequest.php
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use App\Rules\Maiuscula;

class LivroRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'titulo' => 'required',
            'autor' => ['required', 'string', new Maiuscula],
            'edicao' => 'required',
            'isbn' => 'nullable|numeric',
        ];
    }
}
```

## Fonte de estudo - vá além
[Laravel 5.6 - Custom Validation Rules - Using Rule Objects](https://laravel.com/docs/5.6/validation#using-rule-objects)
