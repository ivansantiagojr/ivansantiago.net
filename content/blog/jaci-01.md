+++
title = 'Quero fazer minha linguagem de programação'
template = 'blog/article.html'

[extra]
summary = 'Meus pensamentos iniciais sobre como construir minha linguagem (e como vou aprender a fazer isso).'
+++

## Introdução

Acredito que toda pessoa que acabou de aprender a programar tem aquela sensação de que algo "clicou", e sente que consegue aprender qualquer linguagem. Disso, vemos devs pulando de linguagem em linguagem e exibindo seus [GitHub Readme Stats](https://github.com/anuraghazra/github-readme-stats) com diversas linguagens. Na minha visão, isso é normal. Temos vontade de ter contato com coisas diferentes, fazer comparações, entender os diferentes aspectos de cada linguagem.

E eu espero fortemente que eu não tenha descrito nas linhas acima algo que somente eu acredite que seja normal, e acabei desabafando algo que somente eu passei hahaha (duvido muito). 

Comigo, me interessei por diversas linguagens, mas a que mais chamou minha atenção e eu foquei por mais tempo foi *Python*. Eu realmente gosto MUITO de Python, gosto de como podemos fazer tantas coisas diferentes com ela, gosto da maneira com que a linguagem é dirigida, amo a comunidade e a forma única como Python permite começar o aprendizado de forma extremamente fácil com scripts fazendo tarefas simples até entender conceitos complexos como programação assíncrona, programação paralela, **tipagem gradual**, etc.

## Motivação

Sim, Python pode ficar bem complexo, acredito que isso seja até uma barreira de entrada às vezes — falo isso como alguém que já sofreu com _ambientes virtuais_ e arquivos de dependências (obrigado por facilitar nossas vidas,`pyproject.toml`). E agora, com a complexidade que a tipagem do Python ganha a cada lançamento, imagino que essa barreira esteja ainda maior, principalmente porque está cada vez mais difícil encontrar código Python sem anotações de tipos. Eu mesmo sou um dos culpados por isso, e gosto de usar e estudar os tipos da linguagem, mas é inegável a complexidade que isso adiciona.

Então, nesse assunto de linguagens, em uma live na Twitch do [Dunossauro,](https://dunossauro.com) ele comentou como se interessou por Python por que viu em Python a expressividade que ele tinha no Bash, mas com um poder extra de C. E foi aí que parei para pensar: já que Python ficou tão complexo, qual linguagem ocupa esse lugar hoje em dia? Simples como Bash, poderoso como C. Na minha cabeça, veio uma outra linguagem que eu acho interessante, mas não uso muito: Lua.

## Lua, a linguagem brasileira

[Lua](https://www.lua.org/) é uma linguagem brasileira feita e mantida por uma equipe na PUC-Rio (acesse o site oficial de Lua para conhecer melhor) e eu acho que ela é a linguagem que ocupa esse espaço. Ela é expressiva como Bash, e poderosa como C. E ela não adicionou uma complexidade descaracterizante com o tempo.

Eu verdadeiramente acredito que Lua é uma das melhores linguagens para começar na programação. Lua é simples, pequena — tanto em armazenamento computacional quanto mental.

Pelos motivos listados acimas, e por Lua ser uma linguagem planejada para ser [embarcada/incoporada](https://pt.wikipedia.org/wiki/Linguagem_de_script#Linguagens_de_extens%C3%A3o/incorporadas) em outros programas, Lua é amplamente usada em [jogos](https://en.wikipedia.org/wiki/Category:Lua_(programming_language)-scriptable_game_engines), [servidores web](https://openresty.org/en/), até no editor de texto que estou usando para escrever essa postagem, o [Neovim](https://neovim.io/).
Entretanto, pelo fato de Lua ser uma linguagem feita para ser embarcada, ela compartilha alguns problemas com outras linguagens do tipo. Assim como o JavaScript, Lua tenta fazer coisas para garantir que o programa nunca quebre. Por exemplo, em Lua, se eu tenho a seguinte tabela:

```lua
local animal = {
    nome = 'cachorro',
    cor = 'caramelo',
}
```

E eu tento acessar:

```lua
animal.comprimento
```

Eu não tenho um erro dizendo que a eu não tenho a chave `comprimento` na tabela, a linguagem retorna o valor `nil`, que é um valor completamente válido.

Algumas conversões automáticas também ocorrem:

```lua
> "1" + 1 == 2
true
>
```

Essas características, com o fato de Lua ter uma biblioteca padrão mínima, fazem, na minha opinião, a linguagem não ser a melhor para uso _standalone_, isto é, o uso como um interpretador indepedente por si só, para fazer aplicações "sozinhas", como Python, Ruby, Node.JS, e afins são usados para construir CLIs, aplicações em servidores, aplicações desktop e muito mais. E, caso não tenha ficado claro, isso não é um problema, Lua foi planejada para outro uso. Por isso, eu quero fazer minha própria linguagem, quero usar tudo que Lua tem de bom e adaptar para um outro uso. Ainda mais importante, eu vou poder me divertir e aprender coisas completamente novas nessa jornada. Estou animado com esse projeto.

## Meu plano para construir a linguagem

Obviamente, eu ainda preciso aprender muita coisa para poder fazer um interpretador, por isso, não vou sair escrevendo código do nada. Eu não estou com pressa, vou tomar meu tempo para realmente aprender com esse processo e, com alguma esperança, ter um projeto legal de ser usado. Ainda assim, se eu esperar saber tudo que preciso para começar, eu vou desistir antes de escrever uma linha sequer. Então, meu plano vai ser o seguinte

### Passo 1 - Usar Lua

Eu já fiz [alguns projetos pequenos](https://github.com/ivansantiagojr?tab=repositories&q=&type=&language=lua&sort=) com Lua, mas eu quero fazer algo que me ajude a entender melhor a linguagem, seu sistema de módulos, o modelo de dados, e qualquer característica interessante. Então, eu vou tentar fazer algo em campos que já conheço e/ou tenho interesse.

Nesse sentido, existem 3 projetos que eu gostaria de fazer com Lua:

1. Um framework web pequeno (que eu já comecei a desenvolver, se chama [Hiper](https://github.com/ivansantiagojr/hiper)).

    A ideia desse framework não é fazer tudo do zero, mas pegar um servidor de web em Lua, escolhi o [pegasus.lua](https://github.com/EvandroLG/pegasus.lua/) e fazer uma API legal em cima disso. Talvez inspirado por coisas que já uso no trabalho, como Django e FastAPI. Não vou fazer algo complexo demais, e provavelmente você não deveria usar esse framework, mas eu acho importante publicar um pacote em alguma linguagem para entendê-la bem, e esse projeto eu achei divertido. Então é isso, sem motivos profundos.

2. Um programa de linha de comando de teste de carga em API inspirado pelo [oha](https://github.com/hatoo/oha). 

    Esse projeto é interessante, minha ideia aqui é me manter em web e fazer um programa, como já fiz alguns programas de linha de comando, vou me manter em um ambiente familiar enquanto uso ferramentas diferentes. Além disso, quero explorar as capacidades de concorrência e paralelismo de Lua, talvez até fazendo alguma pequena extensão em [Zig](https://ziglang.org/). A escolha de Zig, e não C, é porque Zig é a outra linguagem além de Python e Lua que eu me interesso, e planejo fazer o interpretador todo em Zig. Sem muitos motivos, é um gosto e uma escolha.

3. Um jogo com Defold (não-bloqueante)

    Aqui a ideia é sair da minha zona de conforto e usar Lua como uma linguagem embarcada em outro software, no caso, a _game engine_ Defold, que usa Lua como sua principal linguagem de script. Eu quero conhecer o uso de Lua para o que ela foi feita, ver quais bibliotecas são adicionadas e mais características da linguagem. Além disso, fazer jogos é divertido (mesmo que seja trabalhoso), precisa de mais explicação?

### Passo 2 - Aprender a fazer interpretadores em Zig

Agora que já sei um pouco de Lua, eu preciso aprender como os interpretadores funcionam, se não, nunca vou conseguir construir um. Para isso, eu vou estudar o curso de como construir um shell do [Blau Araújo (debxp)](https://debxp.org/). Eu comprei o curso dele anteriormente, mas não consegui estudar ao vivo. Vou tentar recuperar os vídeos e materiais e estudar "sozinho" (as aspas estão aqui porque o Blau tem comunidades no Telegram e YouTube que são muito colaborativas, então, sei que posso tirar dúvidas se precisar).

A outra fonte de estudo vai ser o livro [Crafting Interpreters](https://craftinginterpreters.com/) gratuitamente online e fazer as interpretações em Zig, sempre ganhando mais familiaridade com a linguagem. Com o livro, eu espero ter a base teórica necessária para construir Jaci, é forma como escolhi nomear a minha linguagem, devo falar sobre o motivo em um artigo futuro.

### Passo 3 - Fork de Lua com foco no uso standalone: Jaci

Aqui eu realmente começo a fazer a Jaci. A estratégia que estou escolhendo é fazer um fork de Lua e alterar as coisas onde quero adicionar meu próprio design (teremos artigos dedicados sobre o design da Jaci).

O principal motivo de eu ter escolhido fazer um _fork_ de Lua é para me manter motivado.

Como pode ver, eu tracei um longo caminho até chegar à construção de Jaci de fato. Fazendo o fork, eu consigo trabalhar em paralelo e me manter motivado. Eu já consigo alterar algumas coisas e ter um executável `jaci` em uma semana, mesmo que ainda seja exatamente igual à Lua. Em mais uma semana ou duas, eu espero conseguir mudar algum outro componente pequeno que leve o projeto em direção à Jaci. Além disso, eu vou poder fazer alterações pequenas, reescrever partes em Zig, etc., conforme avanço nos meus projetos em Lua e estudos de interpretadores. Com o fork, eu vejo resultados desde o dia 1 e não desisto no meio (espero).

## Ressalvas

Eu sei, talvez esse projeto já exista, talvez seja o [Luau](https://luau.org/), talvez [LuaJIT](https://luajit.org/) já resolva alguns dos problemas que citei, ou até mesmo algum outro projeto de uma pessoa que teve a mesma ideia que eu (se existir, por favor me avise), mas eu quero fazer o meu próprio para aprender. Eu, certamente, ficarei feliz se o projeto ganhar tração e tiver uma comunidade de usuários, mas eu realmente não acho que isso vá acontecer.

Sim, parece ser difícil demais fazer esse projeto, ou que o meu plano coloca muitas coisas na frente do meu objetivo real, mas a minha ideia é me divertir no caminho. Talvez durante algum dos passos eu descubra que nada faz sentido, que eu não gosto de Lua, que eu não gosto de interpretadores, nem de Zig. Tudo bem, eu só quero passar por esse processo. Acho que vou levar aprendizados de alguma forma ou de outra.

## Acompanhe

Eu não sei uma forma fácil de te ajudar a acompanhar o blog. Esse site é gerado com meu próprio SSG, o [hipertexto](https://github.com/artefatto/hipertexto), e ele ainda não suporta RSS ou algo parecido. Talvez dar `watch` no repositório do GitHub ajude por agora: [ivansantiagojr/ivansantiago.net](https://github.com/ivansantiagojr/ivansantiago.net).
