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
- Não estamos usando a versão mais atual do Ziggy Pydust por conta de um erro ainda sem solução.

# O que é Zig?

Da [documentação](https://ziglang.org/), Zig é:

> uma linguagem de programação de propósito geral e um conjunto de ferramentas
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

```sh
$ zig build-exe hello.zig
$ ./hello
Olá, pessoas!
```

## "Olá, pessoas!" em... C?

```c
#include <stdio.h>

int main() {
    puts("Olá, Pessoas!");
    return 0;
}
```

Sim! E nós podemos compilar isso com Zig!!

### Compilando

```sh
$ zig build-exe hello.c -lc
$ ./hello
Olá, pessoas!
```

Nesse caso, tivemos que passar a flag `-lc` para dizer ao Zig para usar a libc (estamos utilizando o módulo `stdio` da libc).

### Como o Zig consegue compilar a própria linguagem e também C?

Isso acontece porque o binário de Zig contém o clang (isso deve mudar em versões futuras). E tão interessante quanto, é que Zig consegue fazer _cross-compiling_, isto é, compilar o código com arquiteturas e sistemas operacionais alvo diferentes da que o compilador está rodando.

Seguindo o exemplo inicial, nós podemos rodar:

```sh
$ zig build-exe hello.zig -target aarch64-macos
```

Vamos analisar o binário gerado:

```sh
$ file hello
hello: Mach-O 64-bit arm64 executable, flags:<NOUNDEFS|DYLDLINK|TWOLEVEL|NO_REEXPORTED_DYLIBS|PIE|HAS_TLV_DESCRIPTORS>
```

Olha que incrível!! Geramos um binário que vai rodar na linha M da apple mesmo eu usando GNU/Linux e um Ryzen 5.

# Por que Zig me interessou?

Eu gosto muito da linguagem Python, não só pelas features, mas pela comunidade e filosofia também. E nesse quesito, eu vejo que Python e Zig combinam bastante. Em ambas, a fundação mantenedora se preocupa com a qualidade do projeto em si, mas também com as pessoas que o usam. Exemplo: Zig mudando para o [codeberg](https://codeberg.org/ziglang), Python rejeitando dinheiro do governo dos EUA para poder continuar apoiando projetos de inclusão e diversidade.

Eu pretendo expandir esse assunto em um artigo futuro.

Mas como podemos programar usando Python e Zig nos nossos projetos?

# Ziggy Pydust

O Ziggy Pydust é um framework desenvolvido pela SpiralDB para escrever extensões Python em Zig. E já vem com algumas coisas bem legais como:

* Integração com pyproject.toml
* Plugin Pytest que roda os testes Python e Zig

## Iniciando um projeto Ziggy Pydust

Neste exemplo, vamos construir uma biblioteca `fastfibo`, baseada na excelente [live de Cython](https://www.youtube.com/watch?v=wfjJWf-jebI) do nosso amigo da comunidade Python [dunossauro](dunossauro.com).

Nossa biblioteca terá um função que recebe uma posição e calcula o número de sequência de [Fibonacci](https://pt.wikipedia.org/wiki/Sequ%C3%AAncia_de_Fibonacci) daquela posição. Vamos aprender mais na implementação.

Vamos criar o nosso projeto usando [poetry](https://python-poetry.org/):

```sh
poetry new fastfibo --flat
```

E logo de cara, eu vou editar o campo `requires-python` no `pyproject.toml` para

```toml
requires-python = ">=3.13,<3.14"
```

Isso vai nos permitir adicionar o `Ziggy Pydust` como uma dependência de desenvolvimento.


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

def test_ola():
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
Legal, agora temos nossa implementação de fibonacci em Zig com um teste.

Só falta adicionar esse módulo no nosso arquivo de build com:

```zig
    _ = pydust.addPythonModule(.{
        .name = "fastfibo.fibo",
        .root_source_file = b.path("src/fibo.zig"),
        .limited_api = true,
        .target = target,
        .optimize = optimize,
    });
```

> O arquivo `build.zig` completo fica assim:
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

    _ = pydust.addPythonModule(.{
        .name = "fastfibo.fibo",
        .root_source_file = b.path("src/fibo.zig"),
        .limited_api = true,
        .target = target,
        .optimize = optimize,
    });
}
```

Vamos rodar os testes Python para essa função?

Em `tests/test_fibo.py`, escreva:

```python
from fastfibo.fibo import fibo


def test_fibo():
    result = fibo(3)
    expected_result = 2

    assert result == expected_result
```

Se tudo estiver correto, nós devemos ver os dois testes passando quando rodamos `pytest -vv` de dentro do ambiente virtual:
```sh
===================== test session starts =====================
platform linux -- Python 3.13.7, pytest-9.0.1, pluggy-1.6.0
rootdir: /home/ivan/Documents/fastfibo
configfile: pyproject.toml
plugins: ziggy-pydust-0.25.1
collected 2 items

tests/test_fibo.py .                                    [ 50%]
tests/test_ola.py .                                     [100%]

====================== 2 passed in 0.31s ======================
```

## Ok, mas por que usar Ziggy Pydust?

Existem alguns motivos para desenvolver um módulo Python em outra linguagem. Você pode estar buscando usar algo que o Python só entrega pela ABI C, você pode querer fazer uso do ecossistema específico de outra linguagem ou buscar um ganho de performance significativo. 

No caso trabalho nesse texto, o ganho de performance é o ponto chave para usar Ziggy Pydust, para isso, vamos fazer uma comparação rápida entre uma implementação de da função para calcular a sequência de Fibonacci em Python puro e a que fizemos agora em Zig.

Para isso, nós escrevemos uma simples implementação da função para calcular Fibonacci em Python puro.

Crie o arquivo `fastfibo/fib.py` com o seguinte conteúdo:

```python
def fib(n: int):
    a = 0
    b = 1

    for i in range(n):
        a, b = a + b, a

    return a
```

E um arquivo chamado `run.py`, que é o que vai calcular a diferença de performance entre as duas funções:

```python
from timeit import timeit

py = timeit("fib(93)", number=1_000_000, setup='from fastfibo.fib import fib')
zig = timeit("fibo(93)", number=1_000_000, setup="from fastfibo.fibo import fibo")

print("Python puro", py)
print("Ziggy Pydust", zig)

print(f"{py/zig=}")
```

O código acima:

1. Conta o tempo que levou para executar a função fib (em Python) com o parâmetro `93` um milhão de vezes.
2. Conta o tempo que levou para executar a função fibo (em Zig) com o parâmetro `93` um milhão de vezes.
3. Calcula quantas vezes o a implementação em Zig é mais rápida.

Os resultados na minha máquina mostram que a implementação em Zig pode ser 7 vezes mais rápida do que a em Python puro:

```
Python puro 2.0440780429998995
Ziggy Pydust 0.2800135359975684
py/zig=7.299925825791686
```

Esses resultados mostram que somente por escrever a extensão em Zig, já tivemos um ganho de performance bem significativo em relação a implementação em Python.

## Vale a pena usar o Ziggy Pydust?

Mais cedo eu pontuei que escrever extensões Python vale a pena em alguns casos. Vamos analisar se Zig e Ziggy pydust se encaixa nesses casos?

1. Fazer proveito do ecossistema de outra linguagem

    Zig ainda não é uma linguagem com um ecossistema tão grande, a linguagem é jovem, mantida por uma fundação sem fins lucrativos. Nesse quesito, C/C++, Cython, Rust serão opções muito melhores.

2. Ter ganhos de performance significativos
    
    Na live do dunossauro que eu citei anteriormente, os resultados mostrados são bem diferentes. Apesar de esses números não serem considerados benchmarks reais, existem muitas variáveis nos ambiente, etc. Entretanto, usar C ou Cython mostrou ganhos de performance muito maiores. Veja os resultados das comparações entre PYthon, Cython e C do Dunossauro (rodando na mesma máquina da comparação entre Python e Zig:

```
Python Puro 2.8343024810019415
Cython/Python 0.06522368900186848
Cython Puro 0.05452507299196441
C Puro 0.055832105994340964
py/cy=43.45510847938617
py/px=51.98163570399337
py/cc=50.76474244566775
cy/cc=1.1682111545009501
px/cc=0.9765899390843499
```

## Conclusão

C e Cython (e provavelmente Rust com [PyO3](https://pyo3.rs)) oferecem maiores ganhos de performance e, certamente, um ecossistema maior e mais desenvolvido do que Zig com Ziggy Pydust. Mas isso não é demérito algum. Zig é uma linguagem altamente instável, as coisas quebram constantemente, e o Ziggy Pydust é novo, com comunidade pequena. Tudo isso torna mais difícil fazer otimizações para os ganhos de performance serem ainda mais impressionantes.

Por isso, eu diria para avaliar bem se você deve usar Ziggy Pydust no seu trabalho ou projeto importante. E se você é um entusiasta Zig e Python, vamos conversar e tentar construir algo para unir as vantagens das duas linguagens de forma interessante!

# Referências

1. Site oficial do [Zig](https://ziglang.org/) com documentações, notícias, etc.
2. [Introduction to Zig](https://pedropark99.github.io/zig-book/) - Livro brasileiro (mas em inglês) sobre Zig
3. Documentação do [Ziggy Pydust](https://pydust.fulcrum.so/latest/)
4. Documentação do [sistema de build](https://ziglang.org/learn/build-system/) do Zig
