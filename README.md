# Esmaltale Landings - Repositório de Landing Pages

Este repositório armazena e gerencia as landing pages de campanha para as diversas unidades da rede **Esmaltale Estética e Beleza** (Shopping centers em Salvador/Lauro de Freitas, Bahia). 

O deploy do repositório é realizado de forma estática e automatizada através do **Cloudflare Workers**.

---

## Estrutura de Pastas e Convenção

Para evitar conflitos de estilo, mistura de imagens e sobrescrita de arquivos entre diferentes unidades, adotamos um padrão de isolamento absoluto sob o diretório `public/`.

Cada unidade possui sua própria pasta contendo o arquivo HTML da landing page e a pasta de assets (imagens e logos) associados:

```text
/ (raiz do repositório)
├── wrangler.jsonc            # Configuração do Cloudflare Workers Assets
├── README.md                 # Esta documentação
└── public/                   # Pasta raiz de arquivos públicos servidos pelo Cloudflare
    └── [slug-da-unidade]/    # Diretório isolado para a unidade (Ex: parque-shopping-bahia)
        ├── index.html        # Landing page (com CSS/JS embutidos e isolados)
        └── assets/           # Pasta de imagens e logotipos da unidade
            ├── logo_branca.png
            ├── Logo_cor.png
            ├── Lojas/        # Fotos reais da loja
            ├── Servicos/     # Imagens dos serviços prestados
            └── Galeria/      # Fotos de resultados (prova social)
```

---

## Como Adicionar uma Nova Unidade

Para criar uma landing page para uma nova unidade, siga o passo a passo:

1. **Criar a pasta da unidade:**
   Crie uma nova pasta dentro de `public/` com o slug da unidade (Ex: `public/salvador-shopping/`).

2. **Organizar as imagens:**
   Crie uma pasta `assets/` dentro do diretório da nova unidade, com as seguintes subpastas:
   * `assets/Lojas/` - Fotos do ambiente físico da loja.
   * `assets/Servicos/` - Imagens das especialidades (unhas, podologia, etc.).
   * `assets/Galeria/` - Fotos de trabalhos realizados (prova social).
   * Adicione os logos `logo_branca.png` e `Logo_cor.png` na raiz da pasta `assets/`.

3. **Criar o HTML:**
   Adicione o arquivo `index.html` na raiz da pasta da unidade (ex: `public/salvador-shopping/index.html`).
   * **Importante:** Todos os caminhos de imagem no HTML **devem** ser relativos ao próprio arquivo HTML (Ex: `src="assets/logo_branca.png"`, `src="assets/Lojas/loja.jpg"`). Como o HTML e os assets estão no mesmo nível da pasta da unidade, isso garante compatibilidade perfeita local e remota.
   * **CSS e JS Escopados:** Lembre-se de encapsular todo o CSS e JS sob a ID correspondente à raiz do HTML para evitar que estilos do WordPress ou do tema herdeiro vazem ao colar o fragmento no construtor de páginas (Ex: `#esmaltale-landing`).

---

## Desenvolvimento Local

Para visualizar e testar as páginas em seu computador:

1. Abra o terminal na raiz deste projeto.
2. Inicie um servidor HTTP local simples (Ex: usando Python):
   ```bash
   python3 -m http.server 8080
   ```
3. Acesse a landing page da unidade desejada em seu navegador pelo caminho correspondente:
   * **Parque Shopping Bahia:** `http://localhost:8080/parque-shopping-bahia/`

---

## Deploy e Publicação

* **Cloudflare Workers:** O projeto está configurado no `wrangler.jsonc` para servir a pasta `./public`.
* **CI/CD Automatizado:** A branch `main` do repositório remoto **hupcreative-ag/landingsesmaltale** está vinculada diretamente ao deploy automático no painel do Cloudflare Workers.
* **Como publicar:** Qualquer modificação ou nova pasta que receber `git push` na branch `main` será automaticamente compilada e disponibilizada em produção em segundos.
