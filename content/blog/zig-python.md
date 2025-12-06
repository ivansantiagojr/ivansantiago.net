+++
title = 'Construindo bibliotecas Python com Zig'
template = 'blog/article.html'
+++

# Construindo Bibliotecas Python com Zig - Introdução a Ziggy Pydust

# Índice
1. O que é Zig?
2. "Olá, pessoas!" em Zig
3. Por que Zig me interessou?
4. Ziggy Pydust
5. Referências

# Premissas
- Eu não entrarei em detalhes sobre Python ou Zig nesse artigo, só irei mostrar como unir os dois. Caso precise se aprofundar, consulte as referências no final da página.
- Não estamos usando a versão mais atual do Ziggy Pydust por conta de um erro.

# O que é Zig?

Da [documentação](https://ziglang.org/), Zig é:

> uma linguagem de programação de proposito geral e um conjunto de ferramentas
> para manter software robusto, otimizado e reutilizável.

Na prática, isso quer dizer que Zig não é só uma linguagem. Vamos explorar isso
mais pra frente. Antes, vamos ver a cara da linguagem.

# "Olá, pessoas!" em Zig

A seguir, vamos ver um dos "Hello, world!" mais feios da história. Esta preparado?

```zig
const std = @import("std");

pub fn main() !void {
    try std.fs.File.stdout().writeAll("Olá, Pessoas!\n");
}
```

Quanta coisa, eu só queria dizer "oi"...

## Compilando

```shell
$ zig build-exe hello.zig
$ ./hello
Olá, pessoas!
```

## "Olá, pessoas!" em... C?

```c
#include <stdio.h>

int main() {
    puts("Salve, simpatia!");
    return 0;
}
```

Sim! E nós podemos compilar isso com Zig!!

### Compilando

```shell
$ zig build-exe hello.c -lc
$ ./hello
Olá, pessoas!
```

Nesse caso, tivemos que passar a flag `-lc` para dizer ao Zig para usar a libc (estamos utilizando o módulo `stdio` da libc.

### Como o Zig consegue compilar a própria linguagem e também C?

Isso acontece porque o binário de Zig contém o clang (isso deve mudar versões futuras). E tão interessante quanto é que Zig consegue fazer _cross-compiling_, isto é, compilar o código com arquiteturas e sistemas operacionais alvo diferentes da que o compilador está rodando.

Seguindo o exemplo inicial, nós podemos rodar:

```shell
$ zig build-exe hello.zig -target aarch64-macos
```

Vamos analisar o binário gerado:

```shell
$ file hello
hello: Mach-O 64-bit arm64 executable, flags:<NOUNDEFS|DYLDLINK|TWOLEVEL|NO_REEXPORTED_DYLIBS|PIE|HAS_TLV_DESCRIPTORS>
```

Olha que incrível!! Geramos um binário que vai rodar na linha M da apple mesmo eu usando GNU/Linux e um Ryzen 5.

# Por que Zig me interessou?

Eu gosto muito da linguagem Python, não só pelas features, mas pela comunidade e filosofia também. E nesse quesito, eu vejo que Python e Zig combinam bastante. Em ambas, a fundação mantenedora se preocupa com a qualidade do projeto em si, mas também com as pessoas que o usam. Exemplo: Zig mudando para o [codeberg](https://codeberg.org/ziglang), Python rejeitando dinheiro do governo dos EUA para poder continuar apoiando projetos de inclusão e diversidade.

Mas como podemos programar usando Python e Zig nos nossos projetos?

# Ziggy Pydust

O Ziggy Pydust é um framework desenvolvido pela SpiralDB para escrever extensões Python em Zig. E já vem com algumas coisas bem legais como:

* Integração com pyproject.toml
* Plugin Pytest que roda os testes Python e Zig

## Iniciando um projeto Ziggy Pydust

Neste exemplo, vamos construir uma biblioteca `fastfibo`, baseada na excelente [live de Cython](https://www.youtube.com/watch?v=wfjJWf-jebI) do nosso amigo da comunidade Python [dunossauro](dunossauro.com).

Vamos criar o nosso projeto usando [poetry](https://python-poetry.org/):

```sh
poetry new fastfibo --flat
```

E logo de cara, eu vou editar o campo `requires-python` no `pyproject.toml` para

```toml
requires-python = ">=3.13,<3.14"
```

Isso vai nos permitir adicionar o `Ziggy Pydust` como uma dependencia de desenvolvimento.


```sh
poetry add -G dev ziggy-pydust==0.25.1
```

O Ziggy Pydust é uma dependencia de desenvolvimento pois a biblioteca final não depende de nada que o Ziggy Pydust contém, a compilação será nativa. Vamos continuar desenvolvendo o projeto para entender.


## Configurando o Ziggy Pydust

Para o Ziggy Pydust saber como fazer a build do nosso projeto, vamos ter que fazer algumas configurações.

Antes, vamos criar um script de build especial na raiz do nosso projeto (mesma altura do arquivo `pyproject.toml`). Escreva o seguinte conteúdo num arquivo chamado `build.py`
```python
from pydust.build import build

build()
```

E, em seguida, vamos alterar o `pyproject.toml` com as configurações relacionadas ao Ziggy Pydust.
As alterações serão:

1. Dizer ao poetry para incluir o pacote:
```toml
[tool.poetry]
include = [
    { path = "src/", format = "sdist" },
    { path = "fastfibo/*.so", format = "wheel" }
]
```

2. Dizer ao poetry para usar nosso script de build customizado:
```toml
[tool.poetry.build]
script = "build.py"
```

3. Incluir o ziggy-pydust nas nossas dependências de build:
```toml
[build-system]
requires = ["poetry-core>=2.0.0,<3.0.0", "ziggy-pydust==0.25.1"]
build-backend = "poetry.core.masonry.api"
```

4. E, por último, configurar o Ziggy Pydust no modo `self-managed`:
```toml
[tool.pydust]
self_managed = true
```
Isso vai comunicar ao Ziggy Pydust que nós queremos gerenciar o arquivo de build do zig manualmente.



O arquivo `pyproject.toml` final deverá se parecer com:

```toml
[project]
name = "fastfibo"
version = "0.1.0"
description = ""
authors = [
    {name = "ivansantiagojr",email = "ivansantiago.junior@gmail.com"}
]
readme = "README.md"
requires-python = ">=3.13,<4.0"
dependencies = [
]

[dependency-groups]
dev = [
    "ziggy-pydust (>=0.25.1)"
]

[tool.poetry]
include = [
    { path = "src/", format = "sdist" },
    { path = "fastfibo/*.so", format = "wheel" }
]

[tool.poetry.build]
script = "build.py"

[tool.pydust]
self_managed = true

[build-system]
requires = ["poetry-core>=2.0.0,<3.0.0", "ziggy-pydust==0.25.1"]
build-backend = "poetry.core.masonry.api"
```

Ufa..., setup feito. Agora vamos escrever o nosso primeiro módulo em Zig!




### "Olá, pessoas!" em Zig, mas para o Python

Antes de já começar com o código de fibonacci, vamos ver se um simples print funciona.

Seguindo o passo a passo:

1. Crie o arquivo `src/fastfibo/ola.zig`
2. Adicione o seguinte código no arquivo criado no passo anterior:
```zig
const py = @import("pydust");

const root = @This();

pub fn ola() !py.PyString(root) {
    return try py.PyString(root).create("Olá, pessoas!");
}

comptime {
    py.rootmodule(root);
}
```

#### Testando o módulo em Zig

Para podermos saber se o módulo Zig funciona do Python, vamos escrever testes. Para isso, instale o pytest com:

```sh
poetry add -G dev pytest
```

E podemos escrever nosso test em `tests/test_ola.py`:

```python
from fastfibo.ola import ola

def test_ola()
    assert ola() == "Olá, pessoas!"
```

Maaas, antes de rodar o test, precisamos dizer para o Zig como compilar tudo isso. Para isso, vamos aprender sobre scripts de build.

### O `build.zig`

Até agora a gente só usou o comando build-exe, e até passamos alguns parâmetros para incluir bibliotecas ou mudar o alvo de compilação, mas quando um projeto fica suficientemente grande, é preferível definir a forma como o projeto deve ser compilado em um arquivo. No Python, tempos o `pyproject.toml` para definir como fazer a build do projeto, em Zig, temos o script `build.zig`.

> Assim como compilamos o `hello.c` com Zig, o build.zig também pode ser usado para definir compilar projetos C/C++. Aliás, até o próprio interpretador do Python já foi compilado com Zig. Confira em [github.com/allyourcodebase/cpython](https://github.com/allyourcodebase/cpython).

O `build.zig` deve ser criado na raiz do nosso projeto com o seguinte código:

```zig
const std = @import("std");
const py = @import("./pydust.build.zig");

pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptionsQueryOnly(.{});
    const optimize = b.standardOptimizeOption(.{});

    const test_step = b.step("test", "Run library tests");

    const pydust = py.addPydust(b, .{
        .test_step = test_step,
    });

    _ = pydust.addPythonModule(.{
        .name = "fastfibo.ola",
        .root_source_file = b.path("src/ola.zig"),
        .limited_api = true,
        .target = target,
        .optimize = optimize,
    });
}
```

### Rodando os testes

Agora, dentro do ambiente virtual, que pode ser ativado com `poetry shell` (caso você tenha o [plugin shell](https://github.com/python-poetry/poetry-plugin-shell) instalado), nós podemos rodar os testes com

```shell
pytest . -vv
```

E, se tudo deu certo nossos testes passaram!!!

Mas, caso você queira algo mais visual, você pode rodar o shell do Python de dentro do ambiente virtual, e rodar:

```python
>>> from fastfibo.ola import ola
>>> ola()
'Olá, pessoas!'
```


## Desenvolvendo a função de fibonacci

Agora, vamos desenvolver uma função que é pesada em CPU para comparar a performance do Python com a do módulo me zig.

O primeiro passo é criar o arquivo `src/fibo.zig` com a função:

```zig
const std = @import("std");
const py = @import("pydust");

const root = @This();

pub fn fibo(args: struct { n: u64 }) u64 {
    if (args.n < 2) return args.n;
    var f0: u64 = 0;
    var f1: u64 = 1;
    var fnth: u64 = f0 + f1;
    for (2..args.n) |_| {
        f0 = f1;
        f1 = fnth;
        fnth = f0 + f1;
    }
    return fnth;
}

comptime {
    py.rootmodule(root);
}

const testing = std.testing;
test "fibonacci iterative" {
    py.initialize();
    defer py.finalize();

    try testing.expectEqual(@as(u64, 34), fibo(.{ .n = 9 }));
}
```

# Referências

1. Site oficial do [Zig](https://ziglang.org/) com documentações, notícias, etc.
2. [Introduction to Zig](https://pedropark99.github.io/zig-book/) - Livro brasileiro (mas em inglês) sobre Zig
3. Documentação do [Ziggy Pydust](https://pydust.fulcrum.so/latest/)
4. Documentação do [sistema de build](https://ziglang.org/learn/build-system/) do Zig
