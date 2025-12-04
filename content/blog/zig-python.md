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


## O que é Zig?

Da [documentação](https://ziglang.org/), Zig é:

> uma linguagem de programação de proposito geral e um conjunto de ferramentas
> para manter software robusto, otimizado e reutilizável.

Na prática, isso quer dizer que Zig não é só uma linguagem. Vamos explorar isso
mais pra frente. Antes, vamos ver a cara da linguagem.

## "Olá, pessoas!" em Zig

A seguir, vamos ver um dos "Hello, world!" mais feios da história. Esta preparado?

```zig
const std = @import("std");

pub fn main() !void {
    try std.fs.File.stdout().writeAll("Olá, Pessoas!\n");
}
```

Quanta coisa, eu só queria dizer "oi"...

### Compilando

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

* Integração com Pyproject
* Plugin Pytest que roda os testes Python e Zig


# Referências
1. Site oficial do [Zig](https://ziglang.org/) com documentações, notícias, etc.
2. [Introduction to Zig](https://pedropark99.github.io/zig-book/) - Livro brasileiro (mas em inglês) sobre Zig
3. Documentação do [Ziggy Pydust](https://pydust.fulcrum.so/latest/)
