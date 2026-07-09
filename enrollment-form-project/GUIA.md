# Guia de Técnicas — Formulário de Matrícula

Roteiro de estudo para replicar as técnicas usadas neste projeto em futuros formulários.

---

## 1. Estrutura HTML Semântica

### Divisão de página em duas colunas
```html
<div id="app">
  <main>...</main>
  <aside>...</aside>
</div>
```
`<main>` recebe o formulário. `<aside>` recebe o painel decorativo. O CSS depois os coloca lado a lado com Grid.

### Agrupando campos com fieldset + legend
```html
<fieldset>
  <legend>Informações da criança</legend>
  <!-- campos relacionados -->
</fieldset>
```
`<fieldset>` agrupa campos que pertencem a uma seção. `<legend>` é o título visível do grupo. No CSS, o `border: none` remove a borda padrão do fieldset.

### Vinculando label ao campo
```html
<label for="fullname">Nome completo</label>
<input id="fullname" name="fullname" type="text">
```
O `for` do label deve ser idêntico ao `id` do input. Isso faz o clique no label focar o campo.

### Upload de arquivo
```html
<form enctype="multipart/form-data">
  <input type="file" id="birth-file" name="birth-file">
```
`enctype="multipart/form-data"` é obrigatório quando o form envia arquivos.

### Campos desabilitados (preenchimento automático por CEP)
```html
<input type="text" id="street" value="Rua das Flores" disabled>
```
`disabled` bloqueia a edição. No CSS, o wrapper recebe `opacity: .5` para indicar visualmente o estado.

---

## 2. CSS: Variáveis e Tipografia

### Custom Properties no :root
```css
:root {
  --font-family: 'Poppins', sans-serif;
  --text: 400 1rem/1.5 var(--font-family);
  --text-sm: 400 0.875rem/1.4 var(--font-family);

  --text-primary: #292524;
  --text-secondary: #57534E;
  --stroke-highlight: #F3541C;
  --surface-secondary: #FEE7D6;
}
```

- `:root` — seletor que representa o elemento `<html>`. Variáveis definidas aqui ficam disponíveis em todo o CSS.
- `--nome-da-variavel: valor` — sintaxe para criar uma variável CSS (Custom Property).
- `var(--nome)` — sintaxe para usar a variável em qualquer propriedade.

Para mudar o tema do projeto inteiro, basta alterar o `:root`.

### Shorthand de font
```css
font: 600 1.5rem/1.25 var(--font-family);
/* peso  tamanho/altura-da-linha  família */
```

- `font-weight: 600` — espessura do texto (100 a 900). 400 = normal, 700 = bold.
- `font-size: 1.5rem` — tamanho da fonte.
- `line-height: 1.25` — altura da linha em relação ao tamanho da fonte. 1.25 significa 25% maior que o tamanho.
- `font-family` — família tipográfica. `sans-serif` é o fallback caso a fonte não carregue.

### Conversão px → rem
```
valor em px ÷ 16 = valor em rem
64px = 4rem | 32px = 2rem | 14px = 0.875rem
```
Sempre usar `rem` para tamanhos de fonte e espaçamentos. `rem` é relativo ao tamanho base do navegador (geralmente 16px), respeitando configurações de acessibilidade do usuário.

---

## 3. Reset CSS (global.css)

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

- `*` — seletor universal, aplica em todos os elementos da página.
- `margin: 0` — remove a margem padrão que navegadores adicionam em elementos como `<body>`, `<h1>`, `<p>`.
- `padding: 0` — remove o espaçamento interno padrão.
- `box-sizing: border-box` — faz com que `padding` e `border` sejam incluídos no tamanho total do elemento, não somados por fora. Sem isso, um `width: 100%` com `padding` ultrapassa o container.

```css
img {
  max-width: 100%;
  height: auto;
}
```

- `max-width: 100%` — impede que a imagem ultrapasse a largura do container pai.
- `height: auto` — mantém a proporção original da imagem ao redimensionar.

---

## 4. Layout com CSS Grid

### Duas colunas fixas (main + aside)
```css
#app {
  display: grid;
  grid-template-columns: 51.25% 48.75%;
  height: 100vh;
  overflow: hidden;
}
```

- `display: grid` — ativa o modo Grid no elemento. Os filhos diretos se tornam itens do grid.
- `grid-template-columns: 51.25% 48.75%` — define duas colunas com larguras percentuais. Os valores vieram do Figma: `656px / 1280px = 51.25%` e `624px / 1280px = 48.75%`.
- `height: 100vh` — faz o elemento ocupar 100% da altura da viewport (janela visível do navegador). `1vh = 1%` da altura da tela.
- `overflow: hidden` — esconde qualquer conteúdo que ultrapasse os limites do container. Combinado com `overflow: auto` nos filhos, cria scroll independente em cada coluna.

### Scroll independente por coluna
```css
main {
  overflow: auto; /* só o main tem rolagem */
}

aside {
  overflow: auto; /* só o aside tem rolagem */
}
```

- `overflow: auto` — adiciona barra de scroll apenas quando o conteúdo ultrapassar o tamanho do elemento.

### Conteúdo centralizado dentro da coluna
```css
main {
  padding: 4rem;

  & .main-container {
    max-width: 33rem;
    margin-left: auto;
  }
}
```

- `padding: 4rem` — espaçamento interno em todos os lados (cima, baixo, esquerda, direita).
- `max-width: 33rem` — limita a largura máxima do conteúdo. Valor veio do Figma: `528px / 16 = 33rem`.
- `margin-left: auto` — empurra o container para a direita, alinhando visualmente com o painel aside.

### Grid responsivo para radio buttons
```css
.radio-wrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(7.5rem, 1fr));
  gap: 1rem;
}
```

- `repeat(auto-fit, ...)` — cria automaticamente quantas colunas couberem na largura disponível.
- `minmax(7.5rem, 1fr)` — cada coluna tem no mínimo `7.5rem` e no máximo `1fr` (fração igual do espaço restante).
- `gap: 1rem` — espaço entre os itens do grid (linhas e colunas).

Resultado: os cards de radio se reorganizam sozinhos sem media queries.

---

## 5. CSS Nesting

Recurso moderno nativo do CSS (sem precisar de Sass) que aninha seletores filhos dentro do pai:

```css
main {
  padding: 4rem;

  & .back {
    display: flex;
    gap: .5rem;
  }

  & h1 {
    font: 600 1.5rem/1.25 var(--font-family);
  }
}
```

- `&` — representa o seletor pai. `& .back` equivale a `main .back` escrito fora do bloco.
- Evita repetir `main` em cada regra e mantém estilos relacionados agrupados no mesmo bloco.

---

## 6. Estilizando Inputs Nativos

### Reset do estilo nativo
```css
input, textarea, select {
  appearance: none;
  width: 100%;
  border-radius: .5rem;
  border: 2px solid transparent;
  outline: 1px solid var(--stroke-default);
  padding: .75rem 1rem;
  font: var(--text);
}
```

- `appearance: none` — remove o estilo visual que o sistema operacional aplica nativamente (a "cara" do Windows/Mac/Android no input). Necessário para ter controle total do design.
- `width: 100%` — faz o input ocupar toda a largura do container pai.
- `border-radius: .5rem` — arredonda os cantos do elemento.
- `border: 2px solid transparent` — cria uma borda invisível de 2px. Ela existe mas não aparece — assim, ao aplicar cor no `:focus`, o layout não "pula" (o espaço já estava reservado).
- `outline: 1px solid var(--stroke-default)` — linha fina cinza ao redor do campo. `outline` não ocupa espaço no layout (diferente de `border`).
- `padding: .75rem 1rem` — espaçamento interno: `.75rem` em cima/baixo e `1rem` nas laterais.
- `font: var(--text)` — por padrão, inputs não herdam a fonte da página. É preciso declarar explicitamente.

### Diferença entre border e outline

| Propriedade | Ocupa espaço no layout | Pode ter border-radius | Usado para |
|---|---|---|---|
| `border` | Sim | Sim | Borda visual do elemento |
| `outline` | Não | Não (exceto em navegadores modernos) | Indicador de foco, anel externo |

### Efeito de dupla borda no focus
```css
input:focus {
  outline: .25rem solid var(--surface-secondary);
  border-color: var(--stroke-highlight);
  outline-offset: .1px;
}
```

- `outline: .25rem solid var(--surface-secondary)` — anel laranja claro externo ao campo.
- `border-color: var(--stroke-highlight)` — muda só a cor da borda (já existia com 2px transparent).
- `outline-offset: .1px` — distância entre o outline e a borda do elemento. Valor pequeno para ficarem quase juntos.

### Ícone customizado no input de data
```css
input[type="date"] {
  position: relative;

  &::-webkit-calendar-picker-indicator {
    opacity: 0;
  }

  &::before {
    content: "";
    position: absolute;
    background: url(../../assets/icons/calendar-03.svg) center/contain;
    right: 1rem;
    width: 1.25rem;
    height: 1.25rem;
    top: 50%;
    transform: translateY(-50%);
  }
}
```

- `input[type="date"]` — seletor de atributo. Aplica apenas em inputs com `type="date"`.
- `::-webkit-calendar-picker-indicator` — pseudo-elemento que representa o ícone nativo do calendário. `opacity: 0` o torna invisível sem removê-lo (mantém a funcionalidade de abrir o calendário ao clicar).
- `::before` — pseudo-elemento CSS que insere um conteúdo visual antes do elemento.
- `content: ""` — obrigatório para `::before` e `::after` aparecerem. Pode ser texto ou vazio.
- `position: absolute` — posiciona o pseudo-elemento em relação ao pai que tem `position: relative`.
- `background: url(...) center/contain` — define uma imagem SVG como fundo, centralizada e redimensionada para caber sem cortar.
- `right: 1rem` — distância de 1rem da borda direita do input.
- `top: 50%` + `transform: translateY(-50%)` — centraliza verticalmente. `top: 50%` move o topo do elemento ao centro; `translateY(-50%)` sobe metade da sua própria altura para ficar perfeitamente no meio.

### Ícone customizado no select
```css
select {
  background: url(../../assets/icons/arrow-down-01.svg) no-repeat calc(100% - 1rem);
}
```

- `background: url(...)` — define imagem de fundo.
- `no-repeat` — impede que a imagem se repita (o padrão do CSS é repetir o background).
- `calc(100% - 1rem)` — posição horizontal calculada: 100% da largura menos 1rem. Coloca o ícone a 1rem da borda direita.

---

## 7. Propriedades de Posicionamento

```css
position: relative; /* no pai */
position: absolute; /* no filho */
inset: 0;
```

- `position: relative` — o elemento permanece no fluxo normal, mas serve de referência para filhos com `position: absolute`.
- `position: absolute` — o elemento sai do fluxo normal e se posiciona em relação ao ancestral mais próximo com `position: relative`.
- `inset: 0` — shorthand para `top: 0; right: 0; bottom: 0; left: 0`. Faz o elemento cobrir completamente o pai.

Usado nos controles customizados para o input invisível cobrir toda a área clicável.

---

## 8. Flexbox

```css
display: flex;
align-items: center;
justify-items: center;
gap: .5rem;
flex: 1;
flex: 2;
```

- `display: flex` — ativa o Flexbox. Os filhos diretos se alinham em linha (por padrão horizontal).
- `align-items: center` — centraliza os itens no eixo cruzado (vertical quando a direção é horizontal).
- `justify-items: center` — centraliza os itens no eixo principal. No Grid é mais comum; no Flex usa-se `justify-content`.
- `gap: .5rem` — espaço entre os itens flex/grid, sem precisar de margin.
- `flex: 1` — o item ocupa 1 parte do espaço disponível.
- `flex: 2` — o item ocupa 2 partes do espaço disponível. Com `flex-1` e `flex-2` juntos, o maior ocupa o dobro do espaço (ex.: campo Rua + campo Número).

---

## 9. Controles Customizados (Radio, Checkbox, Dropzone)

A técnica é sempre a mesma: **esconder o input nativo e criar um elemento visual no HTML** que reflete os estados via CSS.

### Radio Button Customizado
```html
<div class="radio-inner">
  <input type="radio" name="turno" id="manha">
  <div class="radio-image"></div>
  <img src="assets/icons/sun.svg">
  <label for="manha">Manhã</label>
</div>
```

```css
.radio-inner {
  position: relative;

  & input, & input:focus, & input:hover {
    all: unset;
    position: absolute;
    inset: 0;
  }

  .radio-image {
    background-image: url(../../assets/icons/radio-default.svg);
    width: 1.5rem;
    height: 1.5rem;
    position: absolute;
    top: .5rem;
    left: .5rem;
  }

  &:hover,
  &:focus-within {
    border-color: var(--stroke-highlight);
    & .radio-image { background-image: url(../../assets/icons/radio-hover.svg); }
  }

  &:has(:checked) {
    border: 2px solid var(--stroke-highlight);
    & .radio-image { background-image: url(../../assets/icons/radio-checked.svg); }
  }
}
```

- `all: unset` — zera absolutamente todos os estilos do elemento (aparência, borda, cor, tamanho). Usado para fazer o input nativo desaparecer visualmente, mas manter a funcionalidade.
- `background-image: url(...)` — aplica uma imagem SVG como fundo do `.radio-image`.
- A troca de `background-image` nos estados `:hover`, `:focus-within` e `:has(:checked)` simula os três estados visuais do radio sem JavaScript.

### Checkbox Customizado
Mesma lógica do radio:
```css
.checkbox-wrapper {
  position: relative;

  & [type="checkbox"] {
    all: unset;
    position: absolute;
    inset: 0;
  }

  .checkbox-image {
    flex: 0 0 1.5rem; /* não cresce, não encolhe, largura fixa de 1.5rem */
    height: 1.5rem;
    background-image: url(../../assets/icons/checkbox-default.svg);
  }

  &:hover, &:focus-within {
    .checkbox-image { background-image: url(../../assets/icons/checkbox-hover.svg); }
  }

  &:has(:checked) .checkbox-image {
    background-image: url(../../assets/icons/checkbox-checked.svg);
  }
}
```

- `flex: 0 0 1.5rem` — shorthand de `flex-grow: 0` (não cresce), `flex-shrink: 0` (não encolhe), `flex-basis: 1.5rem` (tamanho fixo de 1.5rem). Garante que o checkbox não distorça ao lado de texto longo.

### Dropzone (área de upload)
```css
.dropzone {
  border: 2px dashed transparent;
  outline: 1px dashed var(--stroke-default);
  position: relative;

  & input {
    position: absolute;
    inset: 0;
    opacity: 0;
  }

  &:hover, &:has(input:focus) {
    outline-width: 0;
    border-color: var(--stroke-highlight);
    background-color: var(--surface-secondary);

    & svg path { stroke: var(--stroke-highlight); }
  }
}
```

- `border: 2px dashed transparent` — borda tracejada invisível reservada (mesmo truque do input: espaço já ocupado para não pular no hover).
- `outline: 1px dashed` — linha tracejada fina visível no estado padrão.
- `opacity: 0` — torna o input invisível mas mantém clicável (diferente de `display: none` que remove da interação).
- `outline-width: 0` — remove o outline no hover (a borda `dashed` laranja o substitui).
- `& svg path { stroke: ... }` — muda a cor do traço do SVG inline diretamente pelo CSS.

---

## 10. Pseudo-classes Essenciais

| Pseudo-classe | Quando ativa | Exemplo de uso |
|---|---|---|
| `:focus` | Campo está focado (clicado ou tabulado) | Mudar borda do input |
| `:hover` | Mouse sobre o elemento | Mudar cor do botão |
| `:focus-within` | Qualquer filho do elemento está focado | Realçar o card quando o input dentro dele recebe foco |
| `:has(:checked)` | Elemento pai contém um input marcado | Estilizar o card do radio quando está selecionado |
| `:has([disabled])` | Elemento pai contém um input desabilitado | Aplicar opacidade no wrapper inteiro |
| `:invalid` | Input com valor inválido (ex: email malformado) | Mostrar borda vermelha |
| `:not(:focus):valid` | Input válido E sem foco | Esconder mensagem de erro após o campo ser preenchido |

### Por que usar :focus-within no pai em vez de :focus no input?

Se você colocar `:focus` no input diretamente, só o input muda de aparência. Com `:focus-within` no container pai, você pode estilizar o card inteiro (borda, fundo, ícone) quando o input dentro dele recebe foco.

---

## 11. Mensagem de Erro com Validação Nativa

```html
<input type="mail" id="mail" name="mail" required>
<div class="error">
  <img src="assets/icons/alert-circle.svg">
  <span>Insira um e-mail válido</span>
</div>
```

```css
input[required] + .error {
  display: flex;
  align-items: center;
  gap: .25rem;
  color: var(--semantic-error);
}

input:not(:focus):valid + .error {
  display: none;
}
```

- `input[required]` — seletor de atributo: seleciona inputs que têm o atributo `required`.
- `+` — seletor de irmão adjacente: aplica estilo no elemento que vem imediatamente após. `.error` deve estar logo depois do `input` no HTML.
- `input:not(:focus):valid` — input que é válido (`valid`) E não está focado (`not(:focus)`). Esconde o erro quando o usuário preencheu corretamente e saiu do campo.

Fluxo do erro:
1. Página carrega → `.error` aparece (input required existe).
2. Usuário foca o campo → `.error` continua visível.
3. Usuário digita e-mail válido → ainda visível (está em foco).
4. Usuário sai do campo → `:not(:focus):valid` se torna verdadeiro → `.error` some.

---

## 12. Buttons

```css
button {
  all: unset;
  font-weight: 500;
  padding: .75rem 1.5rem;
  border-radius: .5rem;
  cursor: pointer;

  &.btn-primary {
    color: white;
    background-color: #E43A12;

    &:hover, &:focus {
      background-color: #F3541C;
    }
  }

  &.btn-secondary {
    margin-left: auto;
    border: 1px solid var(--text-highlight);

    &:hover, &:focus {
      outline: .25rem solid var(--surface-secondary);
    }
  }
}
```

- `all: unset` — remove o estilo nativo do botão (fundo cinza, borda, etc.).
- `cursor: pointer` — muda o cursor para mãozinha ao passar sobre o botão. Necessário após `all: unset` porque o cursor padrão do botão também é removido.
- `margin-left: auto` — no `.btn-secondary`, empurra o botão para a esquerda dentro do container flex, deixando o `.btn-primary` à direita.
- `&.btn-primary` — nesting com seletor de classe. Equivale a `button.btn-primary` (o elemento é `button` e tem a classe `btn-primary`).

---

## 13. Campo Desabilitado com Opacidade no Wrapper

```css
.input-wrapper:has([disabled]) {
  opacity: .5;
}

input[disabled] {
  background-color: var(--surface-disabled);
  border: 1px solid var(--stroke-default);
  color: var(--text-primary);
}
```

- `.input-wrapper:has([disabled])` — seleciona o wrapper pai quando qualquer filho tiver o atributo `disabled`. Assim o `label` e o `input` ficam esmaecidos juntos com um só seletor.
- `opacity: .5` — 50% de transparência aplicada no elemento e em tudo dentro dele.
- `[disabled]` — seletor de atributo sem valor: seleciona qualquer elemento com o atributo `disabled`, independentemente do seu valor.

---

## 14. Seletor de Irmão Adjacente (+) e input + small

```css
input + small {
  font: var(--text-sm);
  color: #78716C;
  display: inline-block;
  margin-top: .25rem;
}
```

- `input + small` — seleciona o `<small>` que vem imediatamente após um `<input>` no HTML. Usado para exibir o texto de ajuda abaixo do campo de nome do responsável.
- `display: inline-block` — permite aplicar `margin-top` em um elemento inline como `<small>`.

---

## 15. Organização dos Arquivos CSS

```
styles/
├── index.css        ← importa tudo com @import
├── global.css       ← reset, variáveis, utilitários (.flex, .flex-1)
├── layout.css       ← main, aside, #app
├── forms.css        ← form, fieldset, legend, actions-wrapper
└── fields/
    ├── index.css    ← importa todos os fields
    ├── input.css    ← input, label, select, textarea
    ├── radio.css    ← radio customizado
    ├── checkbox.css ← checkbox customizado
    ├── droparea.css ← dropzone de arquivo
    └── buttons.css  ← btn-primary, btn-secondary
```

- `@import url(fields/index.css)` — importa outro arquivo CSS dentro do CSS. O HTML só precisa linkar `styles/index.css`; todos os outros são carregados em cadeia.
- Separar por responsabilidade facilita encontrar e editar estilos sem procurar em um arquivo gigante.

---

## Checklist para o próximo projeto

- [ ] Criar variáveis de cor e tipografia no `:root`
- [ ] Aplicar reset com `* { margin: 0; padding: 0; box-sizing: border-box }`
- [ ] Converter todos os valores de px para rem (÷16)
- [ ] Usar `fieldset` + `legend` para agrupar seções do form
- [ ] Declarar `font` explicitamente nos inputs (eles não herdam)
- [ ] Aplicar `appearance: none` nos inputs para resetar o visual nativo
- [ ] Criar controles customizados com `all: unset` + `position: absolute; inset: 0`
- [ ] Usar `:has(:checked)` para estilizar o estado marcado
- [ ] Usar `:focus-within` no container para detectar foco em filhos
- [ ] Reservar espaço de borda com `border: 2px solid transparent` antes do foco
- [ ] Separar CSS em arquivos por responsabilidade com `@import`
