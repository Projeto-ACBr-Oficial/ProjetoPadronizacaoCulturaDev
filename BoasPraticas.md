# Boas Práticas de Programação

## Sumário

- [Constantes e Variáveis Globais](#constantes-e-variáveis-globais)
- [Tratamento de Exceções: Boas Práticas](#tratamento-de-exceções-boas-práticas)
  - [1. Liberação Segura de Recursos com `try...finally`](#1-liberação-segura-de-recursos-com-tryfinally)
    - [Princípio Fundamental: Um bloco `try...finally` para cada recurso.](#princípio-fundamental-um-bloco-tryfinally-para-cada-recurso)
  - [2. Captura de Erros e a Responsabilidade da Mensagem com `try...except`](#2-captura-de-erros-e-a-responsabilidade-da-mensagem-com-tryexcept)
    - [Princípio Fundamental: Separe a lógica de negócio da interface do usuário.](#princípio-fundamental-separe-a-lógica-de-negócio-da-interface-do-usuário)
- [Commits](#commits)
- [Não use `with`](#não-use-with)
- [Mudanças de comportamento do código](#mudanças-de-comportamento-do-código)
- [Criação de APIs (bibliotecas, classes, endpoints)](#criação-de-apis)
- [Warnings e hints no código](#warnings-e-hints-no-código)
- [Comentários](#comentários)
- [Referências](#referências)

---
---

## Constantes e Variáveis Globais

O uso de constantes globais é desaconselhado. Sempre que possível constantes devem ser declaradas dentro da classe ou método apropriado.

Variaveis globais não devem ser implementadas a menos que nenhuma outra construção seja possível. Uma variável faz mais sentido quando é declarada junto ao código que a manipula, seja em uma classe, módulo ou função. Variáveis globais podem levar a dependências ocultas e dificultar a manutenção do código.

## Tratamento de Exceções: Boas Práticas

O tratamento de exceções é um pilar fundamental para a construção de aplicações robustas e confiáveis. Seu objetivo principal não é apenas "corrigir" erros, mas sim garantir que o sistema possa se recuperar de estados inesperados de forma controlada ou, no mínimo, encerrar sua execução de maneira segura, sem corromper dados ou deixar recursos alocados.

## 1. Liberação Segura de Recursos com `try...finally`

O bloco `try...finally` garante que um determinado trecho de código (o bloco `finally`) seja **sempre** executado, tenha ocorrido uma exceção ou não. Sua principal finalidade é a liberação de recursos (memória, conexões de banco, arquivos, etc.).

### Princípio Fundamental: Um bloco `try...finally` para cada recurso

A prática de alocar múltiplos recursos e liberá-los em um único bloco `finally` é perigosa. Se a alocação do segundo recurso falhar, o primeiro nunca será liberado, causando um vazamento.

```pascal
var
  ObjA, ObjB: TObject;
begin
  ObjA := TObject.Create;
  ObjB := TObject.Create; // E se esta linha gerar uma exceção? ObjA nunca será liberado.
  try
    // ... usa ObjA e ObjB ...
  finally
    ObjB.Free;
    ObjA.Free;
  end;
end;
```

**Correto - Liberação garantida com blocos aninhados:**

```pascal
var
  ObjA, ObjB: TObject;
begin
  ObjA := TObject.Create;
  try
    ObjB := TObject.Create;
    try
      // ... usa ObjA e ObjB ...
    finally
      ObjB.Free;
    end;
  finally
    ObjA.Free;
  end;
end;
```

## 2. Captura de Erros e a Responsabilidade da Mensagem com `try...except`

O bloco `try...except` serve para capturar uma exceção e executar um código de tratamento específico para aquele erro.

### Princípio Fundamental: Separe a lógica de negócio da interface do usuário

A afirmação de que "não devemos usar `try...except` para exibir mensagens" aponta para uma prática de arquitetura muito importante: a **separação de responsabilidades**. Módulos de baixo nível (como classes de acesso a dados ou regras de negócio) não deveriam ser responsáveis por exibir mensagens ao usuário.

Em vez de capturar uma exceção apenas para mostrar um `ShowMessage` e "engolir" o erro, a melhor abordagem é:

1. **Deixar a exceção propagar:** Permita que a exceção suba na pilha de chamadas até um manipulador global, centralizado, que é responsável por apresentar o erro ao usuário de forma padronizada.
2. **Capturar, tratar e relançar:** Se você precisar executar uma ação específica quando um erro ocorre (como registrar um log ou reverter uma transação), capture a exceção, execute sua lógica e, em seguida, **relance-a** usando o comando `raise;` para que os níveis superiores da aplicação ainda saibam que o erro ocorreu.

**Incorreto - "Engolir" a exceção e misturar responsabilidades:**

```pascal
procedure TCalculadora.CalcularJuros;
begin
  try
    // ... lógica complexa de cálculo ...
    FResultado := FValor / FDivisor; // Risco de divisão por zero
  except
    on EZeroDivide do
      ShowMessage('Erro: Divisão por zero detectada!'); // A classe de cálculo não deveria falar com o usuário.
  end;
end;
```

**Correto - Tratar localmente e relançar para o manipulador global:**

```pascal
procedure TCalculadora.CalcularJuros;
begin
  try
    // ... lógica complexa de cálculo ...
    FResultado := FValor / FDivisor;
  except
    on E: EZeroDivide do
    begin
      Log.Registrar('Tentativa de divisão por zero no cálculo de juros.'); // Ação local apropriada
      raise; // Relança a exceção para que a camada de UI possa tratá-la.
    end;
  end;
end;
```

## Commits

Para manter nosso histórico de versões claro e rastreável, seguimos um padrão simples para todas as mensagens de commit. Uma mensagem bem escrita é fundamental para entendermos a evolução do projeto e o contexto de cada alteração.

A estrutura geral de um commit é a seguinte:

```Text
-- ComponentePrincipal --
[Símbolo] Descrição clara e objetiva da alteração.
[*] Outra descrição, se necessário.
Contexto (Link do Fórum, ID da Task) e Créditos.
```

Vamos detalhar cada parte.

### 1. Cabeçalho do Componente

A primeira linha do commit deve identificar o componente ou a área do projeto que foi modificada, entre duas hifens.

`-- ACBrBal --`

Se a alteração for muito específica, você pode detalhar ainda mais para facilitar a busca futura. Por exemplo, ao invés de um componente genérico, você pode especificar a unit:

`-- ACBrBalToledo.pas --`

### 2. Corpo do Commit

As linhas seguintes descrevem as mudanças realizadas. Elas são compostas por um símbolo e uma descrição.

Os Símbolos
Cada linha de alteração deve começar com um dos seguintes símbolos entre colchetes, indicando a natureza da mudança:

[+] (Adição): Usado para novos recursos, novas funcionalidades ou adição de arquivos.
[*] (Modificação): Usado para alterações de comportamento, refatorações ou melhorias em recursos existentes.
[-] (Correção): Usado para a correção de bugs.
A Descrição: O Quê, e não Como
A descrição deve ser clara, objetiva e focada no objetivo da mudança, não nos detalhes da implementação. Pense em "o que foi feito" para o usuário ou para o sistema, e não em "qual arquivo eu alterei".

Exemplo a ser evitado:

`[+] Adicionada unit ACBrBALWeightechWT3000IR_ABS.pas ❌`

Exemplo ideal:

`[+] Adicionado suporte ao novo modelo de balança Weightech WT3000IR ABS. ✅`

### 3. Contexto e Créditos

Sempre que possível, adicione contexto à sua alteração. Isso ajuda a rastrear a origem da demanda e a discussão técnica por trás da mudança.

Contexto: Inclua o número da Task (TK) ou o link para o tópico no fórum onde o assunto foi discutido.
Créditos: Se a alteração foi feita com a ajuda de outra pessoa ou a partir da contribuição de um membro da comunidade, dê os devidos créditos. Mencionar o nome do contribuidor é uma forma poderosa de agradecer e incentivar a participação contínua.
Exemplo Completo
Juntando tudo, uma mensagem de commit ideal se parece com esta:

```Text
-- ACBrBal --
[+] Adicionado novo modelo de balança Weightech WT3000IR ABS por Leandro Araújo. TK-6260.
[*] Refatorado o método de leitura para otimizar a comunicação com a porta serial.
    Veja: https://www.projetoacbr.com.br/forum/topic/80667-implementacao-para-balanca...
```

## Não use `with`

 O uso de `with` gera código _"fácil de escrever mas difícil de ler"_. Isso porque cria ambiguidades sobre escopo e precedência. Assim dificulta manutenção futura do código. Então não queremos promover essa prática.
 Quando parecer que o `with` deixaria o código mais limpo por conta de nomes de objetos muito longos, use uma variável local.

**Correto:**

 ```pascal
var
  Campo: TField;
begin
  Campo := AqueleDatamoduleComNomeComprido.ComSuaTabelaDeNomeEnorme.FildByName('NomeDoCampo');
  Campo.Value := Campo.Value + 'blabla';
  Showmessage(Campo.AsString);
end;
 ```

 **Obs.:** Apesar disso há muito uso de `with` no código Pascal do ACBr. É necessário refatorar esse código **apenas** se você estiver trabalhando nele.

## Mudanças de comportamento do código

Sempre que precisamos alterar um comportamento, ou  o ideal é o seguinte:

1. Criar tópico de notícias com resumo da alteração juntamente com aviso que "na data X, daqui a Y dias, será feita uma alteração de comportamento". Criar juntamente um tópico que explica os detalhes da modificação para receber possíveis dúvidas.
    - Se possível fazer commits que mostrar que a funcionalidade está se tornando obsoleta. Por exemplo diretivas deprecated no Delphi/Lazarus.
2. Monitorar o tópico com dúvidas, enquanto não chega a data X, avaliando possíveis problemas que poderiam indicar que a mudança precisa ser adiada.
3. Na data X publicar a alteração. Se necessário um novo tópico.
    - Em alguns casos, algum recurso não deve mais funcionar e nao é possível manter a compatibilidade. Nesses casos é preferível que o commit quebre **compilações** que façam uso do recurso. Quebrar na compilação é muito melhor do que deixar um comportamento indesejado sendo executado. Assim o programador pode identificar a alteração necessária de cara. Se isso não acontecer, talvez o programador só perceba o problema tempos depois. Mas aí a possibilidade do comportamento já ter causado danos irreversíveis é real. Veja também o "Fail Fast principle".

## Criação de APIs

APIs aqui se refere a qualquer interface de programação que é criada e se comunica com outras partes por meio de métodos. Inclui bibliotecas, classes, endpoints, webservices, etc...

1. Não use nomes diferentes para a mesma coisa. Use nomes coerentes. Por exemplo, não crie uma classe com método `HabilitaFiltro` e uma função `ExisteAlgumFiltroAtivo` se "Habilitar" e "Ativo" tiverem o mesmo significado nesse contexto. Isso cria confusão. [Veja também esse blog.](https://devblogs.microsoft.com/oldnewthing/20250728-00/?p=111415)
2. Evite métodos com o mesmo nome, por exemplo, usando `overload`. Geralmente isso indica que ou o nome do método não é específico o suficiente ou que ele está fazendo coisas demais.

## Warnings e hints no código

Ao adicionar novo código certifique-se que ele não adicione novos warnings e hints. Ao mexer em códigos antigos, lembre-se da regra do escoteiro: "Deixe o local mais limpo que o encontrou". Queremos, se possível, reduzir a zero o número de wanings e hints que são gerados pelo compilador e ferramentas como FixInsight, SonarCube, etc.

## Comentários

Comments are for describing what the code does, or better, why the code is doing what it’s doing. If you want to say “And here is where you would insert your app-specific business logic”, then don’t use a comment. Call out to a method that is not implemented in the sample.(Veja Referência 1).

## Referências

 Algumas referências em caso de dúvidas:
 1 - https://devblogs.microsoft.com/oldnewthing/20250925-00/?p=111627
