# Leitor de ISBN (protótipo)

Página única (`index.html`) para testar a leitura de ISBN antes de montar o sistema completo. Não tem servidor nem banco: tudo roda no navegador.

## O que ela faz

- **Câmera ao vivo:** lê o código de barras EAN-13 atrás do livro. Usa o leitor nativo do navegador (`BarcodeDetector`, presente no Chrome do Android) e, quando ele não existe (iPhone, Firefox), carrega a biblioteca ZXing.
- **Tirar foto:** abre a câmera do celular, tira a foto e procura o código nela. Funciona mesmo sem HTTPS.
- **Digitação e leitor USB:** aceita ISBN de 10 ou 13 dígitos, com ou sem traços. ISBN-10 é convertido para ISBN-13. Leitor USB funciona com o foco no campo ou fora dele.
- **Validação:** só aceita códigos que começam com 978 ou 979 e cujo dígito verificador fecha. A câmera só aceita depois de ver o mesmo número em 2 quadros seguidos.
- **Dados do livro:** consulta cada fonte da BrasilAPI separadamente (CBL, Mercado Editorial, Google Books, Open Library); se a BrasilAPI estiver fora do ar, vai direto à Open Library. Livro não encontrado fica na fila para completar à mão.
- **Fontes que discordam:** o mesmo ISBN às vezes aparece como livros diferentes em cada fonte (editoras antigas reaproveitavam números). Quando isso acontece, a ficha fica como **Conferir título** e mostra as opções para escolher. A BrasilAPI sozinha devolveria a fonte que respondesse primeiro, por isso o título errado aparecia antes.
- **Exemplares:** ler de novo um título que já está na fila oferece "+1 exemplar" em vez de duplicar.
- **Planilha:** exporta CSV (separado por `;`, abre direto no Excel) com **uma linha por exemplar** e as colunas `tombo`, `categoria` e `origem` em branco.
- A fila fica guardada no navegador (localStorage) até ser limpa.

## Como testar

A câmera ao vivo só funciona em **HTTPS** ou em `localhost`. Sem isso, a página avisa e os modos foto, digitação e USB continuam funcionando.

**No celular (recomendado): GitHub Pages**

1. O código já está neste repositório (`index.html` na raiz).
2. Em *Settings → Pages*, escolha *Deploy from a branch*, branch `main`, pasta `/ (root)`.
3. Espere 1 ou 2 minutos e abra no celular **https://marcos-heitor-as.github.io/leitor/**

**No PC com webcam**

```bash
npx serve .
```

Abra `http://localhost:3000`. `localhost` conta como seguro para o navegador.

Abrir pelo IP do PC no celular (`http://192.168.x.x:3000`) **não** libera a câmera ao vivo, porque não é HTTPS. Nesse caso, use *Tirar foto*.

## Roteiro de teste com livros reais

Separe uns 20 livros da biblioteca, metade novos e metade doados, e anote:

1. Qual leitor apareceu em "Leitura:" (nativo ou ZXing) e em qual celular.
2. Quantos foram lidos pela câmera de primeira, quantos precisaram de foto e quantos precisaram ser digitados.
3. Quantos a BrasilAPI ou a Open Library encontraram e quantos ficaram para completar à mão.
4. Se a lanterna apareceu e ajudou.

Esses números dizem quanto do acervo vai cair no cadastro manual, e isso define quanto investir nessa tela no sistema de verdade.

## Limitações conhecidas

- O CSV é a saída do protótipo; no sistema, a fila vai para o banco e aparece no PC em tempo real.
- Capas vêm das fontes externas e às vezes não existem.
- Livros sem ISBN (comuns em doações antigas e edições governamentais) não passam por aqui; vão para o cadastro manual do sistema.
