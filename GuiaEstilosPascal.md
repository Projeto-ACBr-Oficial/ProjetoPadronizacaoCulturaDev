# Guia de Estilo Pascal ACBr

Esse guia de estilo sobrepõe o "Delphi’s Object Pascal Style Guide". Veja também em [Referências](#referências).

Se o ponto não for abordado aqui, então deve-se seguir o recomendado pelo documento da Embarcadero.

## Sumário

<!-- - [Boas Práticas de Programação](#boas-práticas-de-programação)
  - [Sumário](#sumário) -->
  
- [Upper Camel Case (PascalCase)](#upper-camel-case-pascalcase)
- [Nomes de constantes](#nomes-de-constantes)
  - [Tratamento de Siglas](#tratamento-de-siglas)
- [Arquivo .pas](#arquivo-pas)
  - [Cabeçalho](#cabeçalho)
  - [Uses](#uses)
  - [Bloco de declaração](#bloco-de-declaração)
- [Classes](#classes)
  - [1. Nomenclaturas](#1-nomenclaturas)
  - [2. Escopo de Visibilidade](#2-escopo-de-visibilidade)
  - [3. Atributos ou fields](#3-atributos-ou-fields)
  - [4. Métodos](#4-métodos)
  - [5. Variables locais](#5-variables-locais)
  - [6. Corpo de uma classe](#6-corpo-de-uma-classe)

---
---

## Upper Camel Case (PascalCase)

É uma convenção de nomenclatura utilizada em programação para nomear identificadores (como nomes de classes, interfaces, tipos, etc.) que são compostos por mais de uma palavra.

A regra principal é que a primeira letra de **cada palavra** no identificador deve ser escrita em maiúscula, sem espaços ou outros separadores.

**Por exemplo:**

- `CalculadoraDeJuros`
- `GerenciadorDeConexao`
- `RelatorioAnualDeVendas`

## Nomes de constantes

Devem ser escritas totalmente em maiúsculas, com palavras separadas por sublinhados (_). Isso ajuda a distinguir constantes de variáveis comuns.

**Por exemplo:**

- `MAX_TAMANHO = 100;`
- `TAXA_JUROS_ANUAL = 0.05;`
- `TEMPO_ESPERA_PADRAO = 5000;`

## Tratamento de Siglas

Uma regra importante dentro desta convenção é como lidar com siglas (como HTTP, XML, ID). Para manter a clareza e o reconhecimento da sigla, é comum que ela permaneça **inteiramente em maiúsculas**.

**Por exemplo:**

- `ConexaoHTTP` (em vez de `ConexaoHttp`)
- `AnalisadorXML` (em vez de `AnalisadorXml`)
- `GerarNotaFiscalPDF` (em vez de `GerarNotaFiscalPdf`)
- `IDDoUsuario` (em vez de `IdDoUsuario`)

Essa abordagem torna o código mais legível, pois destaca a sigla como um termo conhecido e indivisível.

## Arquivo .pas

A maior parte do seu código em Pascal residirá em arquivos `.pas`.

### Cabeçalho

É necessário ter um cabeçalho em formato cometário indicando direitos autorais.

### Uses

Preferencialmente, coloque cada unit da seção uses em uma linha. A ordem deve seguir a ordem de dependência. A seguir das units mais básicas para as mais abrangentes.

### Bloco de declaração

Preferencialmente a ordem dentro do bloco de declarações é a seguinte:

`const`, `type`, `var`, e métodos.

## Classes

### 1. Nomenclaturas

Nomes de classes devem seguir a convenção Upper Camel Case (PascalCase), onde cada palavra começa com letra maiúscula e não há espaços ou sublinhados. Devem ser prefixdas com a letra "T".

Classes que herdam de Exception devem ser nomeadas com o prefixo "E", seguindo a convenção de nomenclatura PascalCase.

Da mesma maneira, Interfaces devem iniciar com o prefixo "I".

**Por exemplo:**

- `TCalculadoraDeJuros`
- `TGerenciadorDeConexao`
- `TRelatorioAnualDeVendas`
- `ECalculadoraErro`
- `EConexaoFalhou`
- `ERelatorioInvalido`

### 2. Escopo de Visibilidade

Os escopos de visibilidade declarados em uma classe devem estar, sempre que possível, ordenados do escopo mais restritivo ao menos restritivo.

```pascal

TCliente = 
class
  strict private
    // Escopo estritamente privado (Não funciona no Delphi 7)
  private
    // Escopo privado
  strict protected
    // Escopo estritamente protegido (Não funciona no Delphi 7)
  protected
    // Escopo protegido
  public
    // Escopo público
  published
    // Escopo publicável
  end;
```

### 3. Atributos ou fields

Devemos obrigatóriamente declar atributos de uma classe ao escopo `private` ou `strict private` (escopo preferencial).

Todos os atributos devem ser prefixados com a letra `F`, indicando que são fields (atributos) da classe. Isso ajuda a distinguir atributos de variáveis locais ou parâmetros de métodos.

Rotinas utilizadas para a leitura e escrita de propriedades devem ser nomeadas de forma que o nome do método comece com o prefixo `Get` para leitura e `Set` para escrita, seguido pelo nome da propriedade.

**Por exemplo:**

```pascal
type
  TMinhaClasse = class
  private
    FNome: string;
    FAge: Integer; 
  public
    constructor Create(const ANome: string; AAge: Integer);
    function GetNome: string;
    function GetAge: Integer;
  end;
```

### 4. Métodos

- Devemos nomear métodos seguindo a convenção PascalCase.
- Métodos devem receber nomes significativos que descrevam claramente sua funcionalidade.
- O verbo que representa a ação deve ser sempre no infinitivo.
- Parâmetros:
  - Devem ser prefixados com a letra `A` maiúscula, seguido pelo nome da propriedade. Isso ajuda a distinguir parâmetros de atributos e variáveis locais. Após o prefixo devemos seguir o padrão PascalCase.
  - Sempre que a regra do método permitir, parametros devem ser declarados como `const`
  - Parâmetros do tipo interface devem ser, preferencialmente, declarados sem a diretiva
    const. Esta medida visa evitar memory leaks em decorrência da construção do objeto
    sendo referenciada diretamente pelo parâmetro.

Sempre que a regra de uma function permitir, seu resultado deve
ser escrito diretamente dentro da variável Result, evitando o uso
de variáveis auxiliares.

**Por exemplo:**

```pascal
function MontarNomeCompleto(const ANome, ASobrenome: string): string;
begin
  Result := ANome + ' ' + ASobrenome;
end;
```

### 5. Variables locais

Variáveis locais devem ser prefixadas com a letra "L" minúscula, como em `l`.
Depois de prefixadas, as variáveis devem ser batizadas
seguindo a convenção PascalCase.

### 6. Corpo de uma classe

Dentro do corpo da declaração de uma classe, deve-se seguir preferencialmente a ordem:

 1. Constantes
 2. Atributos / Campos (Fields)
 3. Métodos (incluindo `constructor` e `destructor`)
 4. Propriedades
 5. (class vars, class methods e class properties)

Métodos que implementam uma Interface específica, podem ser colocados juntos e prefixado com o nome da Interface em uma linha de comentário.

**Por exemplo:**

```pascal
    { IOTAThreadNotifier160 }
    procedure EvaluateComplete(); overload;
    { IOTANotifier }
    procedure BeforeSave();
    procedure AfterSave();
    procedure Destroyed();
    procedure Modified();
```

## Referências

 Algumas referências em caso de dúvidas.

- [Delphi’s Object Pascal Style Guide](https://docwiki.embarcadero.com/RADStudio/en/Delphi’s_Object_Pascal_Style_Guide)
- [RADProgrammer Style Guide](https://github.com/radprogrammer/radteam/wiki/RADProgrammer-Style-Guide)
- [Castle Engine Coding Conventions](https://castle-engine.io/coding_conventions)
- [Delphi Language Coding Standards Document](https://web.archive.org/web/20170607183644/http://kodu.ut.ee/~jellen/delphi/cs.html)
- [Project JEDI Delphi Language Style Guide](https://wiki.delphi-jedi.org/wiki/Style_Guide)
