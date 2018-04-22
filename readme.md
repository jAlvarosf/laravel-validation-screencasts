# Validando Manualmente
[Link para o screencast no YouTube](https://www.youtube.com/watch?v=J0aqAZdF3KU)

## Usando o método make da classe Validator
### Importe a classe Validator em app/Http/Controllers/LivroController
```php
use Validator;
```

### Coloque a lógica de validação no método store em app/Http/Controllers/LivroController
```php
public function store(Request $request)
{
    $validacao = Validator::make($request->all(), [
        'titulo' => 'required',
        'autor' => 'required',
        'edicao' => 'required',
        'isbn' => 'nullable|numeric',
    ]);

    if ($validacao->fails()) {
        return redirect('livros/create')
                    ->withErrors($validacao)
                    ->withInput();
    }

    $livro = new Livro;
    $livro->titulo = $request->titulo;
    $livro->autor = $request->autor;
    $livro->edicao = $request->edicao;
    $livro->isbn = $request->isbn;

    $livro->save();

    $request->session()->flash('alert-success', 'Livro adicionado com sucesso!');
    return redirect()->route('livros.index');
}
```

## Usando redirecionamento automático
### Coloque a lógica de validação no método update em app/Http/Controllers/LivroController
- Chame o método validate() para redirecionar o validador criado manualmente:
```php
public function update(Request $request, Livro $livro)
{
    $validacao = Validator::make($request->all(), [
        'titulo' => 'required',
        'autor' => 'required',
        'edicao' => 'required',
        'isbn' => 'nullable|numeric',
    ])->validate();

    $livro->titulo = $request->titulo;
    $livro->autor = $request->autor;
    $livro->edicao = $request->edicao;
    $livro->isbn = $request->isbn;

    $livro->save();

    $request->session()->flash('alert-success', 'Livro alterado com sucesso!');
    return redirect(route('livros.index'));
}
```

## Named error bags (nomeando o conjunto de erros)
### Enviando os erros para o formulário adequado
Técnica que pode ser usada quando tivermos mais de um formulário na página.
- Crie um método storeEditora em app/Http/Controllers/LivroController
```php
public function storeEditora(Request $request)
{               
    $validacao = Validator::make($request->all(), [
        'nome' => 'required',
        'site' => 'nullable|url',
    ]);                         

    if ($validacao->fails()) {
        return redirect('livros/create')
                    ->withErrors($validacao, 'editora');
    }       

    $request->session()->flash('alert-success', 'Editora cadastrada corretamente');
    return redirect()->route('livros.index');
} 
```
- Crie uma nova rota para o novo método
```php
Route::post('editoras', 'LivroController@storeEditora');
```
- Crie um novo formulário na página
```php
<h3>Editora</h3>
@if ($errors->editora->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->editora->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
<form name="editora" method="post" action="{{url('editoras')}}">
    {{csrf_field()}}
    <div class="form-group row">
        <label for="nome" class="col-md-4 col-form-label text-md-right">Nome da Editora</label>
        <div class="col-md-6">
            <input id="nome" class="form-control" name="nome" type="text">
        </div>
    </div>
    <div class="form-group row">
        <label for="site" class="col-md-4 col-form-label text-md-right">Site</label>
        <div class="col-md-6">
            <input id="site" class="form-control" name="site" type="text">
        </div>
    </div>
    <div class="form-group row mb-0">
        <div class="col-md-6 offset-md-4">
            <button type="submit" name="buttonEditora" class="btn btn-primary">
                Salvar
            </button>
        </div>
    </div>
</form>
```

### Códigos completos, com as alterações necessárias
- routes/web.php
```php
<?php
Route::get('/', 'LivroController@create'); 
Route::resource('livros', 'LivroController');
Route::post('editoras', 'LivroController@storeEditora');
```
 - app/Http/Controllers/LivroController.php
 ```php
<?php

namespace App\Http\Controllers;

use App\Livro;
use Illuminate\Http\Request;
use Validator;

class LivroController extends Controller
{
    public function index()
    {
        $livros = Livro::all();
        return view('livros.index', compact('livros'));
    }

    public function create()
    {
        return view('livros.create');
    }

    public function store(Request $request)
    {
        $validacao = Validator::make($request->all(), [
            'titulo' => 'required',
            'autor' => 'required',
            'edicao' => 'required',
            'isbn' => 'nullable|numeric',
        ]);
        
        if ($validacao->fails()) {
            return redirect('livros/create')
                        ->withErrors($validacao, 'livro');
        }

        $livro = new Livro;
        $livro->titulo = $request->titulo;
        $livro->autor = $request->autor;
        $livro->edicao = $request->edicao;
        $livro->isbn = $request->isbn;

        $livro->save();

        $request->session()->flash('alert-success', 'Livro adicionado com sucesso!');
        return redirect()->route('livros.index');
    }

    public function storeEditora(Request $request)
    {
        $validacao = Validator::make($request->all(), [
            'nome' => 'required',
            'site' => 'nullable|url',
        ]);
        
        if ($validacao->fails()) {
            return redirect('livros/create')
                        ->withErrors($validacao, 'editora');
        }

        $request->session()->flash('alert-success', 'Editora cadastrada corretamente');
        return redirect()->route('livros.index');
    }

    public function show(Livro $livro)
    {
        return view('livros.show', compact('livro'));
    }

    public function edit(Livro $livro)
    {
        $action = action('LivroController@update', $livro->id);
        return view('livros.edit', compact('livro', "action"));
    }

    public function update(Request $request, Livro $livro)
    {
        $validacao = Validator::make($request->all(), [
            'titulo' => 'required',
            'autor' => 'required',
            'edicao' => 'required',
            'isbn' => 'nullable|numeric',
        ])->validate();

        $livro->titulo = $request->titulo;
        $livro->autor = $request->autor;
        $livro->edicao = $request->edicao;
        $livro->isbn = $request->isbn;

        $livro->save();

        $request->session()->flash('alert-success', 'Livro alterado com sucesso!');
        return redirect(route('livros.index'));
    }

    public function destroy(Request $request, Livro $livro)
    {
        $livro->delete();
        $request->session()->flash('alert-success', 'livro apagado com sucesso!');
        return redirect()->back();
    }
}
 ```

 - resources/views/forms/livro.blade.php
```php
@extends('layouts.app')
@section('content')
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card card-default">
                <div class="card-header">
                    <h3>Livro</h3>
                    @if ($errors->livro->any())
                        <div class="alert alert-danger">
                            <ul>
                                @foreach ($errors->livro->all() as $error)
                                    <li>{{ $error }}</li>
                                @endforeach
                            </ul>
                        </div>
                    @endif
                </div>
                <div class="card-body">
                    <form name="livro" method="post" action="{{ $action or url('livros') }}">
                        {{csrf_field()}}
                        @isset($livro) {{method_field('patch')}} @endisset
                        <div class="form-group row">
                            <label for="titulo" class="col-md-4 col-form-label text-md-right">Título</label>
                            <div class="col-md-6">
                                <input id="titulo" class="form-control" name="titulo" type="text" value="{{ $livro->titulo or ''}}">
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="titulo" class="col-md-4 col-form-label text-md-right">Autor</label>
                            <div class="col-md-6">
                                <input id="autor" class="form-control" name="autor" type="text" value="{{ $livro->autor or ''}}">
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="edicao" class="col-md-4 col-form-label text-md-right">Edição</label>
                            <div class="col-md-6">
                                <input id="edicao" class="form-control" name="edicao" type="text" value="{{ $livro->edicao or ''}}">
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="isbn" class="col-md-4 col-form-label text-md-right">ISBN</label>
                            <div class="col-md-6">
                                <input id="isbn" class="form-control" name="isbn" type="text" value="{{ $livro->isbn or ''}}">
                            </div>
                        </div>
                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" name="buttonLivro" class="btn btn-primary">
                                    Salvar
                                </button>
                            </div>
                        </div>
                    </form>
                    <h3>Editora</h3>
                    @if ($errors->editora->any())
                        <div class="alert alert-danger">
                            <ul>
                                @foreach ($errors->editora->all() as $error)
                                    <li>{{ $error }}</li>
                                @endforeach
                            </ul>
                        </div>
                    @endif
                    <form name="editora" method="post" action="{{url('editoras')}}">
                        {{csrf_field()}}
                        <div class="form-group row">
                            <label for="nome" class="col-md-4 col-form-label text-md-right">Nome da Editora</label>
                            <div class="col-md-6">
                                <input id="nome" class="form-control" name="nome" type="text">
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="site" class="col-md-4 col-form-label text-md-right">Site</label>
                            <div class="col-md-6">
                                <input id="site" class="form-control" name="site" type="text">
                            </div>
                        </div>
                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" name="buttonEditora" class="btn btn-primary">
                                    Salvar
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
@endsection
```

## Fonte de estudo - saiba mais
[Laravel 5.6 - Manually Creating Validations](https://laravel.com/docs/5.6/validation#manually-creating-validators)
