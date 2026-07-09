# Formulário de Matrícula — Estrelas do Amanhã

Formulário de matrícula para a escola de educação infantil **Estrelas do Amanhã**, desenvolvido como projeto prático de HTML e CSS.

## Protótipo

[Visualizar no Figma](https://www.figma.com/proto/uslu3ZoIQ0i6T1bHEtXVDS/Formul%C3%A1rio-de-matr%C3%ADcula--Community-?node-id=3-811&p=f&t=IqeZX4IagO5ASMLj-1&scaling=min-zoom&content-scaling=fixed&page-id=3%3A376)

## Sobre o projeto

Interface de cadastro que permite matricular uma criança na escola, preenchendo informações pessoais, endereço, dados do responsável e opções de matrícula (turno e esporte).

## Funcionalidades

- Campos de texto, data, select e textarea com estilos customizados
- Upload de arquivo (certidão de nascimento) com área de drag and drop
- Seleção de turno (Manhã / Tarde) com radio buttons customizados
- Seleção de esporte com radio buttons e ícones ilustrativos
- Checkbox de aceite dos Termos e Condições com estados visual (default, hover, checked)
- Validação nativa de e-mail com mensagem de erro
- Campo de endereço com campos desabilitados (preenchimento automático por CEP)
- Layout de duas colunas: formulário à esquerda e painel ilustrativo à direita

## Tecnologias

- HTML5
- CSS3 (CSS Nesting, Custom Properties, Grid, Flexbox)
- Google Fonts — Poppins

## Estrutura de arquivos

```
enrollment-form-project/
├── index.html
├── assets/
│   ├── logo.svg
│   ├── illustration.svg
│   └── icons/
├── styles/
│   ├── index.css
│   ├── global.css
│   ├── layout.css
│   ├── forms.css
│   └── fields/
│       ├── index.css
│       ├── input.css
│       ├── radio.css
│       ├── checkbox.css
│       ├── droparea.css
│       └── buttons.css
```

## Como visualizar

Abra o arquivo `index.html` diretamente no navegador. Não requer servidor ou dependências externas.
