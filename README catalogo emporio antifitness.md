# Empório Antifitness — Catálogo Online

Catálogo web de produtos naturais com sacola de compras e envio do pedido pelo WhatsApp. Feito para uma loja real, com mais de 2.000 produtos, para ser usado principalmente no celular.

**Online:** https://emporio-antifitness.vercel.app

<!-- Adicione aqui um print da tela: ![Catálogo](docs/screenshot.png) -->

## Funcionalidades

- **Catálogo com 2.122 produtos em 11 categorias**, com filtro por categoria e busca instantânea (ignora acentos e maiúsculas, e aceita várias palavras).
- **Sacola de compras** com quantidades editáveis, subtotal por item e total geral.
- **Pedido pelo WhatsApp:** gera uma mensagem pronta com itens, quantidades, valores, total, nome e observação do cliente.
- **Sacola salva no navegador** (`localStorage`): o cliente não perde o pedido ao recarregar a página.
- **Carregamento progressivo** da lista (60 produtos por vez) para manter a página leve.
- **Tema claro e escuro** automático, conforme a preferência do aparelho.
- **Mobile first:** barra da sacola fixa, respeito às áreas seguras do celular (notch e barra inferior) e categorias roláveis na horizontal.
- **Acessibilidade:** foco visível, rótulos ARIA, contagem de resultados anunciada para leitores de tela e suporte a `prefers-reduced-motion`.
- **Produtos sem preço** aparecem como "Consultar".

## Tecnologias

- HTML, CSS e JavaScript puros, sem framework e sem etapa de build
- Node.js (apenas para o script de conversão da planilha)
- Hospedagem na [Vercel](https://vercel.com)

## Estrutura do projeto

```
emporio-antifitness/
├── index.html            # estrutura da página
├── css/style.css         # visual, temas claro/escuro e responsividade
├── js/app.js             # busca, filtros, sacola e geração do link do WhatsApp
├── data/produtos.json    # catálogo (id, nome, categoria, preço)
├── scripts/converter.mjs # converte planilha CSV <-> produtos.json
└── vercel.json           # configuração de deploy
```

## Como rodar localmente

Os produtos são carregados com `fetch`, então é preciso servir a pasta por HTTP (abrir o `index.html` com duplo clique não funciona):

```bash
npx serve
```

Depois abra o endereço mostrado no terminal.

## Configuração da loja

No topo de `js/app.js`:

```js
const SHOP = "Empório Antifitness"; // nome exibido no cabeçalho e no título da página
const PHONE = "55DDDNUMERO";        // WhatsApp que recebe os pedidos (DDI + DDD + número)
const ARQUIVO_PRODUTOS = "data/produtos.json";
const FOTOS = {};                   // {"ID_DO_PRODUTO": "fotos/arquivo.jpg"}
```

## Formato dos produtos

```json
{"id": "9SzrkQDG56iMziQnovfsXY", "nome": "ACUCAR DE COCO 100G", "categoria": "AÇÚCARES", "preco": 7.45}
```

- `id`: identificador único e fixo (a sacola e as fotos dependem dele).
- `preco`: em reais. Preço `0` exibe "Consultar".
- As categorias (com emoji) ficam na lista `categorias` do mesmo arquivo.

## Atualizando produtos e preços por planilha

O catálogo é mantido numa planilha. O script `scripts/converter.mjs` faz a conversão nos dois sentidos, sem dependências (Node.js 18+):

```bash
node scripts/converter.mjs exportar   # gera planilha/produtos.csv
node scripts/converter.mjs importar   # lê a planilha e atualiza data/produtos.json
```

O script aceita preços como `7,45`, `7.45` ou `R$ 1.234,56`, cria ids para produtos novos, valida os dados (nome, categoria, preço e ids repetidos) sem alterar nada em caso de erro, mostra um resumo do que mudou (novos, removidos, preços alterados) e guarda a versão anterior em `scripts/produtos.anterior.json`.

## Deploy

O projeto é um site estático. Na Vercel, basta importar o repositório com o *Framework Preset* em "Other", sem comando de build. Cada alteração enviada ao GitHub publica o site automaticamente.

## Decisões técnicas

- **Sem framework:** o catálogo é pequeno em lógica e grande em dados, então JavaScript puro entrega carregamento rápido e zero dependências para manter.
- **Dados separados do código:** os produtos ficam em JSON, o que permite atualizar preços sem tocar na lógica e abre caminho para trocar a fonte de dados no futuro.
- **Renderização paginada:** exibir 2.000+ itens de uma vez deixaria a página lenta em celulares simples; a lista cresce sob demanda.
- **Pedido via link `wa.me`:** dispensa servidor e pagamento online, combinando com a forma como a loja já atende os clientes.

## Próximos passos

- Fotos dos produtos (o suporte já existe em `FOTOS`)
- Ordenação por preço e por nome
- Domínio próprio

## Autor

Projeto desenvolvido para o Empório Antifitness.
