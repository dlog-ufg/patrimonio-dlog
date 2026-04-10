# Projeto Passa a Régua · Painel DLOG/UFG

Painel público do projeto de regularização patrimonial da Universidade Federal de Goiás, conduzido pela CPAT/DLOG com base no Decreto nº 12.785/2025. Este README serve **dois propósitos**:

1. Documentar o estado atual do projeto atual e onde cada coisa vive
2. Servir de **template reutilizável** para projetos futuros da DLOG — para que o próximo gerente de projetos coloque um painel semelhante no ar com o mínimo de esforço

---

## 1. Estado atual do projeto

### Assets no ar

| O quê | Onde | Observações |
|---|---|---|
| **Painel público** | https://dlog-ufg.github.io/patrimonio-dlog/ | Estático, alimentado ao vivo pelo Trello via API pública |
| **Metodologia** | https://dlog-ufg.github.io/patrimonio-dlog/metodologia.html | Página explicando as 4 etapas do ciclo de regularização |
| **Board Trello** | https://trello.com/b/iGVO6GYV | Nome: "Projeto Passa a Régua · Regularização Patrimonial UFG" |
| **Repo GitHub** | https://github.com/dlog-ufg/patrimonio-dlog | Público, hospedado na org `dlog-ufg` |
| **Drive do projeto** | Pasta pessoal do diretor no Google Drive | Plano B enquanto o Shared Drive oficial do CERCOMP não sai |

### Estrutura do repositório

```
patrimonio-dlog/
├── index.html         Painel principal (HTML + CSS + JS inline, tudo em 1 arquivo)
├── metodologia.html   Página da metodologia das 4 etapas
└── README.md          Este documento
```

Não há build step, framework ou backend. É HTML estático puro, servido pelo GitHub Pages.

### Como o painel puxa dados do Trello

O painel faz chamadas client-side à API pública do Trello com uma chave e token embutidos no JavaScript. As credenciais ficam expostas no fonte, então o token deve ser **read-only** e o board de uso interno.

**Listas especiais do board** (detectadas pelo sufixo no nome):
- `[ATIVO]` → fase em andamento (bolinha amarela pulsante na timeline)
- `[CONCLUIDO]` → fase concluída (bolinha verde)
- `[EQUIPE]` → lista tratada como equipe do projeto (renderizada na seção "Equipe Responsável", não aparece como fase)

**Datas de conclusão/início** das fases são puxadas do histórico de ações do board (endpoint `/boards/:id/actions?filter=updateList`) — quando você renomeia uma lista adicionando `[CONCLUIDO]`, a data da ação vira a data do marco na linha do tempo.

### Fases do board atual (Projeto Passa a Régua)

1. **Fase 1 · Planejamento** — escopo, priorização, orçamento, decisões metodológicas
2. **Fase 2 · Contratação e Capacitação** — bolsistas (2 perfis: campo e conciliação) + treinamento
3. **Fase 3 · Coleta em Campo** — levantamento físico bruto nas unidades prioritárias
4. **Fase 4 · Conciliação** — cruzamento entre bens sem plaqueta e bens não inventariados
5. **Fase 5 · Incorporação e Tombamento** — registro SEI + plaquetas novas + re-coleta
6. **Fase 6 · Encerramento** — relatório final, prestação de contas, documentação para órgãos de controle

### Equipe

A lista `[EQUIPE]` contém 1 card por pessoa:
- Nome do card = nome da pessoa
- Descrição = linha 1 com o cargo, linha 2 com o e-mail institucional

Para adicionar/remover membros, basta mexer direto na lista no Trello — o site reflete na próxima visita.

### Links úteis no painel

- **Menu "Arquivos ↗"** — aponta pra pasta do Drive do projeto. Troca de link é 1 linha em `index.html` (busca por `drive.google.com`).

---

## 2. Template para projetos futuros

Esta seção descreve o passo a passo para colocar um painel semelhante no ar para um projeto novo da DLOG. Estimativa: **1 hora** para ter tudo funcionando.

### Pré-requisitos

- Conta GitHub com acesso à organização `dlog-ufg` (ou outra org da Diretoria)
- Conta Trello (gratuita serve)
- Chave e token da API do Trello (https://trello.com/app-key) — token **read-only**, é só usar escopo mínimo
- Google Workspace `@ufg.br`

### Passo a passo (1 hora)

#### 1. Copiar o repositório como template

No GitHub, clica no botão **"Use this template"** no repo `dlog-ufg/patrimonio-dlog` (ou faça fork manual). Dê um nome ao novo repo, ex: `projeto-X-dlog`.

Alternativa via CLI:
```bash
gh repo create dlog-ufg/projeto-X --template dlog-ufg/patrimonio-dlog --public
git clone https://github.com/dlog-ufg/projeto-X
cd projeto-X
```

#### 2. Criar o board no Trello

Crie um board novo com 6 listas iniciais (ou quantas o projeto exigir). Ao final de cada nome, não adicione sufixo ainda — eles vão sendo adicionados conforme o projeto avança:
- `Fase 1 · [nome]`
- `Fase 2 · [nome]`
- ...

Crie também uma lista `[EQUIPE]` e popule com os membros (nome = nome, descrição = cargo + e-mail).

Copie o **ID do board** da URL (o trecho entre `/b/` e o nome) — você vai precisar no próximo passo.

#### 3. Ajustar credenciais e ID no `index.html`

Abra `index.html` e localize o bloco de configuração (topo do `<script>`):

```js
const TRELLO_KEY   = 'SUA_CHAVE_AQUI';
const TRELLO_TOKEN = 'SEU_TOKEN_AQUI';
const BOARD_ID     = 'ID_DO_BOARD';
```

Substitua pelos valores reais. Token **read-only**.

#### 4. Personalizar textos do painel

Edite apenas o que for específico do novo projeto. Busque no `index.html` por:

| Busca | O que trocar |
|---|---|
| `<title>` | Título da aba do browser |
| `class="lt"` | Título compacto no header (ex: "Passa a Régua") |
| `class="hero"` → `<h1>` | Título grande do hero |
| `class="hdesc"` | Descrição do projeto no hero |
| `class="hsv"` (dentro de `.hstats`) | Métricas do cartão lateral (valor, meta, status) |
| Link do Drive | Busca por `drive.google.com` e substitua pelo novo link |
| `<footer>` | Texto do rodapé |

A `metodologia.html` pode ser apagada ou reescrita conforme o projeto.

#### 5. Publicar no GitHub Pages

```bash
git add index.html metodologia.html README.md
git commit -m "feat: inicializa painel do projeto X"
git push

# Ativar Pages
gh api -X POST /repos/dlog-ufg/projeto-X/pages \
  -f 'source[branch]=main' \
  -f 'source[path]=/'
```

Aguarde 1 a 2 minutos. O painel estará em `https://dlog-ufg.github.io/projeto-X/`.

**Importante:** o repo precisa ser **público** (GitHub Pages privado exige plano pago para organizações Free).

#### 6. Criar a pasta do Drive e integrar

Crie uma pasta no seu Drive pessoal (ou peça ao CERCOMP um Shared Drive institucional — [veja "Pedindo Shared Drive"](#pedindo-shared-drive) abaixo).

Pegue o ID da pasta (trecho depois de `/folders/` na URL) e troque no `index.html` no link do menu e no card "Arquivos do projeto".

#### 7. Rodar o Apps Script para estrutura de pastas (opcional, mas recomendado)

Cole o script abaixo em https://script.google.com → Novo projeto → cola → Salva → Executa `criarEstrutura`. Gera as 9 pastas padrão e um documento LEIA-ME explicativo.

```javascript
const PASTA_RAIZ_ID = 'COLE_O_ID_DA_PASTA_AQUI';

function criarEstrutura() {
  const raiz = DriveApp.getFolderById(PASTA_RAIZ_ID);
  const pastas = [
    '00 - Normativos',
    '01 - Planejamento',
    '02 - Contratação e equipe',
    '03 - Execução',
    '04 - Resultados e regularização',
    '05 - Encerramento',
    'Atas e reuniões',
    'Apresentações',
    'Comunicação externa'
  ];
  pastas.forEach(nome => {
    if (!raiz.getFoldersByName(nome).hasNext()) {
      raiz.createFolder(nome);
      Logger.log('✓ ' + nome);
    }
  });
}
```

Os nomes são genéricos e servem para qualquer projeto. Para o Passa a Régua especificamente, as pastas têm nomes mais específicos (Coleta, Conciliação, etc.) — adapte conforme o projeto.

---

## 3. Checklist mínimo para lançar um novo projeto

Copie esta lista para o primeiro card da Fase 1 do novo projeto:

- [ ] Criar repo a partir do template `dlog-ufg/patrimonio-dlog`
- [ ] Criar board no Trello com as fases iniciais e lista `[EQUIPE]`
- [ ] Gerar chave/token read-only do Trello e preencher em `index.html`
- [ ] Personalizar textos do hero, footer e título
- [ ] Criar pasta do Drive (ou abrir chamado no CERCOMP para Shared Drive)
- [ ] Rodar o Apps Script de estrutura de pastas
- [ ] Ativar GitHub Pages (`gh api -X POST ... /pages`)
- [ ] Atualizar o link do Drive no `index.html`
- [ ] Commitar e pushar
- [ ] Verificar URL final em ~2 min
- [ ] Compartilhar o link com a equipe e com a gestão

---

## 4. Decisões arquiteturais e por que foram feitas assim

| Decisão | Razão |
|---|---|
| **HTML estático, sem framework** | Zero manutenção. Funciona em qualquer navegador, sobrevive a anos sem build, sem dependência, sem quebra por upgrade de lib. |
| **GitHub Pages em vez de servidor próprio** | Grátis, rápido, com HTTPS automático, sem conta de hospedagem para pagar ou renovar. |
| **Trello como fonte de dados** | A equipe já trabalha no Trello — não duplica planilha nem exige novo sistema. Qualquer mudança no board aparece no painel na próxima visita. |
| **Token read-only embutido no JS** | Aceitável porque o board é de uso interno e o token só lê. Alternativa seria um backend proxy, que adicionaria complexidade desnecessária. |
| **Drive no Google Workspace UFG** | Todo mundo já tem login institucional `@ufg.br`. Gratuito, integra com Docs/Sheets, permissões simples. |
| **Apps Script para estrutura de pastas** | Única forma de criar pastas programaticamente sem dar acesso de API a ninguém — o usuário executa com sua própria conta. |
| **Organização GitHub `dlog-ufg`** | Tira projetos do perfil pessoal do diretor e dá continuidade institucional quando houver troca de gestão. |
| **Board Trello privado por padrão** | Só quem a gente convidar vê. O painel público consome via token, então o usuário final nunca precisa de acesso ao board. |
| **Metodologia como página HTML no site** | Disponível na hora, sem depender de autenticação ou de acesso ao Drive. Para equipes que precisam editar colaborativamente, use Google Doc paralelamente. |

---

## 5. Integrações possíveis (não implementadas neste projeto)

Ideias para evolução quando fizer sentido:

- **KPIs reais** alimentados por uma planilha Google pública (ex: % unidades concluídas, bens levantados, discrepância remanescente)
- **Notificações automáticas** via Trello webhooks (Slack/WhatsApp quando uma fase muda de status)
- **Galeria de fotos de campo** puxando de uma pasta do Drive via API
- **Relatórios periódicos** gerados por Apps Script e depositados no Drive como PDF
- **Seção "Atas de reunião"** linkando para uma subpasta específica do Drive

---

## 6. Credenciais usadas neste projeto (para referência do gerente atual)

> **Nunca comitar estas credenciais no repo.** Elas estão embutidas no `index.html` apenas porque o board é privado e o token é read-only.

- **Trello Key**: gerada pelo próprio diretor em https://trello.com/app-key
- **Trello Token**: read-only, gerado a partir da key acima
- **GitHub org**: `dlog-ufg` (plano Free, criada em 10/04/2026)
- **Board Trello ID**: `69d875d797d5c8a08a12e368`

Quando houver troca de gerente:
1. Gerar nova key/token no Trello
2. Atualizar as 3 linhas no `index.html`
3. Revogar o token antigo em https://trello.com/u/[username]/account → Applications

---

## 7. Pedindo Shared Drive institucional ao CERCOMP

Modelo de chamado para abrir em https://suporte.cercomp.ufg.br:

> **Assunto:** Criação de Drive Compartilhado institucional — DLOG
>
> Solicito a criação de um Drive Compartilhado (Shared Drive) no Google Workspace institucional para a Diretoria de Logística (DLOG).
>
> **Nome sugerido:** `DLOG · Diretoria de Logística`
>
> **Gerentes (acesso total):**
> - dlog@ufg.br (Diretor)
> - Outros membros institucionais conforme o caso
>
> **Justificativa:** centralizar documentação da Diretoria e dos projetos conduzidos pela DLOG, garantindo continuidade institucional independente de contas pessoais.
>
> Prazo típico de resposta: 1 a 3 dias úteis.

---

## 8. Contato e suporte

Dúvidas sobre este template ou sobre o projeto atual:

- **Diretoria de Logística**: dlog@ufg.br
- **Coordenadoria de Patrimônio**: cpat.dlog@ufg.br

Este documento é vivo. Quando algo mudar no projeto ou no template, atualize aqui e commite.
