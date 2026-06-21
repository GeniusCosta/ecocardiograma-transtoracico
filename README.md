# Sistema de Laudos de Ecocardiograma — ENCOR

Gerador de laudos de **ecocardiograma transtorácico** em arquivo único, com motores de classificação automática segundo diretrizes (ASE), exportação em PDF e funcionamento **100% offline** como **PWA instalável**.

Desenvolvido para a **Clínica ENCOR** — Dr. Genius Costa (cardiologista e ecocardiografista), Barreiras-BA. Marca **Echo Genius®**.

🔗 **App:** https://geniuscosta.github.io/ecocardiograma-transtoracico/

---

## Visão geral

O sistema monta o laudo a partir de medidas e achados estruturados, gerando automaticamente a **descrição** e as **conclusões** na hierarquia padrão ENCOR. Tudo roda no navegador, sem servidor e sem envio de dados do paciente para fora do dispositivo.

Estrutura do laudo (TTE adulto): cabeçalho → medidas e cálculos → descrição (dimensões e espessuras; aorta torácica; contratilidade do VE; função diastólica do VE; contratilidade do VD; valvas mitral, aórtica, tricúspide e pulmonar; septos; pericárdio; trombos) → conclusões → assinatura.

## Funcionalidades

- **Strain longitudinal global (GLS)** — classificação por *speckle tracking* (preservado / limítrofe / reduzido / gravemente reduzido), com normalização de sinal.
- **Calcificação anular mitral (CAM)** — achado modular parametrizado (discreta / moderada / importante / variante caseosa) com regras de segurança embutidas: estenose degenerativa graduada **somente** por gradiente médio + FC, declaração de que PHT/planimetria/equação de continuidade não se aplicam, e recomendação de TC cardíaca na CAM importante.
- **Valva aórtica** — modelo de seleção pareada (descrição → conclusão): morfologia (tricúspide / bicúspide com rafe), esclerose/estenose (discreta / moderada / importante, com Vmáx, gradiente e AVA) e insuficiência. Consolidação automática como **dupla lesão** quando há estenose + insuficiência.
- **Próteses valvares — motor ASE 2024 (Zoghbi et al.)** — classificação determinística em quatro categorias: função normal · possível disfunção tipo estenose (a esclarecer) · disfunção tipo estenose significativa · gradiente elevado não-estenótico (mismatch paciente-prótese / alto fluxo / recuperação de pressão). Aplica a **regra integrativa** (estenose significativa exige ≥1 parâmetro fluxo-dependente *e* ≥1 fluxo-independente na faixa significativa); gradiente alto isolado nunca conclui estenose. Posições aórtica e mitral independentes.
- **Refluxos** — terminologia padronizada **leve / moderado / grave**, com campos quantitativos (PHT, vena contracta, ORE, volume regurgitante) quando aplicável.
- **Movimento assíncrono do septo interventricular** — chip único que insere a frase na descrição da função sistólica e, na conclusão, sempre na linha abaixo da função ventricular.
- **Modo pediátrico** com referências apropriadas e nomenclatura específica.
- **Exportação em PDF** (A4) via `html2canvas` + `jsPDF`.
- **Salvar / restaurar paciente** localmente, sem sair do dispositivo.
- **Placeholders `[PREENCHER]`** que bloqueiam a conclusão quando faltam valores; o sistema nunca inventa valores clínicos.

## PWA — instalável e offline

Após o primeiro acesso online, o *service worker* faz o *precache* de toda a aplicação. A partir daí o app funciona **sem internet** e pode ser **instalado** como aplicativo:

- **Windows / macOS (Chrome, Edge):** ícone "Instalar" na barra de endereço.
- **Android (Chrome):** menu → "Adicionar à tela inicial" / "Instalar app".
- **iOS (Safari):** Compartilhar → "Adicionar à Tela de Início".

As bibliotecas de PDF são servidas **localmente** (`vendor/`), com *fallback* para CDN apenas se o HTML for aberto solto, fora do deploy.

## Estrutura do projeto

```
index.html                    App (arquivo principal — deve se chamar index.html)
manifest.webmanifest          Manifesto PWA (nome, ícones, cores, standalone)
sw.js                         Service worker (offline-first)
vendor/
  html2canvas.min.js          Captura do laudo para PDF (v1.4.1)
  jspdf.umd.min.js            Geração do PDF (v2.5.1)
icons/
  icon-192.png                Ícone PWA
  icon-512.png                Ícone PWA
  icon-maskable-512.png       Ícone maskable
  apple-touch-icon.png        Ícone iOS
  favicon-32.png              Favicon
```

> Todos os caminhos são **relativos** (`./`), de modo que o app funciona sob a subpasta do GitHub Pages (`usuario.github.io/repositorio/`) sem ajustes.

## Publicação (GitHub Pages)

1. Suba **todos** os arquivos acima para a raiz do branch publicado (`main`), preservando as pastas `vendor/` e `icons/`. O arquivo principal **precisa** se chamar `index.html`.
2. Em **Settings → Pages**, defina *Deploy from a branch* → branch `main` → pasta `/ (root)`.
3. Aguarde a *build* (aba **Actions**) e acesse a URL com *hard refresh* (Ctrl+Shift+R).

> Causa comum de 404: o arquivo principal não estar nomeado `index.html`, ou as pastas `vendor/`/`icons/` não terem sido enviadas.

## Atualizar uma versão publicada

O *service worker* serve a versão em cache. Para publicar mudanças, **incremente** a constante de versão em `sw.js`:

```js
const CACHE_VERSION = 'echo-encor-v1';  // → 'echo-encor-v2'
```

Sem isso, o navegador continuará servindo a versão antiga em cache.

## Desenvolvimento

- Aplicação de **arquivo único** (HTML + CSS + JS embutidos); as únicas dependências externas são as bibliotecas de PDF, vendorizadas localmente.
- Fluxo de validação recomendado: extrair o bloco `<script>` principal e rodar `node --check`; testar os motores de classificação com casos isolados antes de publicar.
- Privacidade: nenhum dado de paciente é transmitido; tudo permanece no navegador/dispositivo.

## Escopo clínico e responsabilidade

Ferramenta de **apoio à elaboração de laudos**. A interpretação, a graduação dos achados e o conteúdo final do laudo são de responsabilidade do médico examinador. Os motores automáticos seguem critérios de referência (ASE / Zoghbi et al.), mas não substituem o julgamento clínico, e os valores não preenchidos são sinalizados, nunca presumidos.

## Autoria e uso

**Dr. Genius Costa** — CRM-BA 23913 — Clínica ENCOR (Núcleo de Endocrinologia e Cardiologia), Barreiras-BA. Marca **Echo Genius®**.

Software proprietário de uso interno da Clínica ENCOR. Todos os direitos reservados.
