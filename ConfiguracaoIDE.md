# Configuração IDEs

As configurações aqui estão baseadas dos documentos "Boas Praticas" e "Guia de Estilo Pascal". As definições desses documentos devem ser utilizadas como base para as definições delineadas aqui.

Estamos disponibilizando também o arquivo que vai ajudar a configurar a IDE.

## Sumário

## Geral

## Delphi

- [Propriedades Explicit*](https://www.projetoacbr.com.br/forum/topic/49532-propriedades-explicit/)
- [Integrando o Beyond Compare Lite (bcompare) com o TortoiseSVN](https://www.projetoacbr.com.br/forum/topic/70843-integrando-o-beyond-compare-lite-bcompare-com-o-tortoisesvn/#comment-459885)

## Lazarus

### Instalando

- Recomendamos usar o [fcpupdeluxe](https://github.com/LongDirtyAnimAlf/fpcupdeluxe/). Ele permite fazer instalação de várias versões na mesma máquina e facilita a instalação dos compiladores e requisitos para crosscompiling.

### Migrando configurações para uma nova versão

- Você pode mover os arquivos de configuração da "Primary config path" (PCP) para uma nova instalação. Veja `Menu: View > Ide Internals > About IDE` a localização exata. Mas se você usou o fcpupdeluxe vai ficar na pasta `\Lazarus\config_lazarus`.
- Os seguintes [são arquivos de configuração](https://wiki.lazarus.freepascal.org/pcp) que você pode querer aproveitar:
  - *jcfsettings.cfg* - Jedi Code Format settings
  - *codeexploreroptions.xml* - Code Explorer Options
  - *codetoolsoptions.xml* - Code Tools Options
  - *lazarus.dci* - stores the Lazarus Code Templates
  - *editoroptions.xml* - Editor Options - information about colors, fonts and other editor options
  - *miscellaneousoptions.xml* - packages information
  - *environmentoptions.xml* - Environment Options - stores the history of opened projects and files (Recent Opened Files and Projects)

- Ao mover os arquivos citados acima, talvez seja necessário ainda ajustar o seu conteúdo. Dentro de alguns desses arquivos existem caminhos absolutos (por exemplo o arquivo environmentoptions.xml). Uma maneira de corrigir é ajustar para [utilizar macros](https://wiki.lazarus.freepascal.org/IDE_Macros_in_paths_and_filenames#Paths_and_Parts) como `$(LazarusDir)/` e utilizar caminhos relativos.

## Referências

- [Tópico área fechada fórum](https://www.projetoacbr.com.br/forum/topic/18125-formata%C3%A7%C3%A3o-e-layout-do-c%C3%B3digo-do-acbr/)
