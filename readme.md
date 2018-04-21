# Validando formulários

## Escreva a lógica de validação
- Nos métodos store e update do controller app/Http/Controllers/LivroController
```php
$validacao = $request->validate([
    'titulo' => 'required',
    'autor' => 'required',
    'edicao' => 'required',
    'isbn' => 'nullable|numeric',
]);
```

## Mostre as mensagens de erro
- Insira a div que mostra os erros em resources/views/livros/create.blade.php
```php
/* Inserir abaixo de <div class="col-md-8"> */
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

## Fontes de estudo - saiba mais
[Laravel 5.6 - Writing the validation logic](https://laravel.com/docs/5.6/validation#quick-writing-the-validation-logic)

[Laravel 5.6 - Displaying the validation errors](https://laravel.com/docs/5.6/validation#quick-displaying-the-validation-errors)

[Laravel 5.6 - A note on optional fields](https://laravel.com/docs/5.6/validation#a-note-on-optional-fields)
