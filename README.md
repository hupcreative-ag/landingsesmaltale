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

---

## Padrão de Tracking (Checklist Obrigatório para Novas Landings)

Toda nova landing page do projeto **deve** incluir as duas ferramentas de analytics (Google Analytics 4 e Meta Pixel) seguindo rigorosamente as regras abaixo antes de ir para produção:

### Regras de Não Interferência
1. **Não editar, remover, mover ou reordenar** nenhuma linha do bloco GA4 existente (`G-G651HHSCPV`).
2. O bloco do Meta Pixel (`ID 798964139939818`) entra como um `<script>` adicional e independente, logo após a tag `</script>` do bloco do GA4.
3. Dentro dos handlers de clique já existentes (`addEventListener`), a chamada `fbq('track', ...)` é **adicionada após** as chamadas `gtag('event', ...)`, nunca substituindo nem em um `addEventListener` separado. Se o Meta Pixel for bloqueado por adblock, o `fbq(...)` falha silenciosamente sem afetar o `gtag(...)`.
4. Manter carregamento `async` no Pixel igual ao GA4 para evitar bloquear o parse do `<head>`.

### Bloco do Meta Pixel (Inserir no `<head>` após o GA4)
```html
<!-- Meta Pixel Code -->
<script>
!function(f,b,e,v,n,t,s)
{if(f.fbq)return;n=f.fbq=function(){n.callMethod?
n.callMethod.apply(n,arguments):n.queue.push(arguments)};
if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
n.queue=[];t=b.createElement(e);t.async=!0;
t.src=v;s=b.getElementsByTagName(e)[0];
s.parentNode.insertBefore(t,s)}(window, document,'script',
'https://connect.facebook.net/en_US/fbevents.js');
fbq('init', '798964139939818');
fbq('track', 'PageView');
</script>
<noscript><img height="1" width="1" style="display:none"
src="https://www.facebook.com/tr?id=798964139939818&ev=PageView&noscript=1"
/></noscript>
<!-- End Meta Pixel Code -->
```

### Tabela de Mapeamento de Eventos

| Gatilho do Clique | Chamadas GA4 (`gtag`) | Chamada Meta Pixel (`fbq`) |
| :--- | :--- | :--- |
| **Clique no WhatsApp** | `gtag('event', 'generate_lead', ...);`<br>`gtag('event', 'click_whatsapp', ...);` | `fbq('track', 'Contact');` |
| **Clique no Trinks (Agendamento)** | `gtag('event', 'begin_checkout', ...);`<br>`gtag('event', 'click_trinks', ...);` | `fbq('track', 'Schedule');` |
| **Download de Cupom** | `gtag('event', 'select_promotion', ...);` | `fbq('track', 'Lead');` |
| **Card de Loja (`/promocoes`)** | `gtag('event', 'select_item', ...);` | `fbq('trackCustom', 'SelectStore', { unit: unit });` |

