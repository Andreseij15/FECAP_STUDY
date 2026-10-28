# FecapStudy — Plataforma de Apoio ao Estudo

Mini-Projeto da **Aula 9 de Programação Orientada a Objetos (FECAP)** — Oficina Prática PBL.
Frontend em **JavaScript puro (HTML5 + CSS3 + DOM)** com as classes de domínio orientadas a objetos, conectado ao **Backend C# + MySQL** (`BackendEstudoAPI`) fornecido na aula.

**Autor:** André Seiji Shiroma

---

## Sumário

1. [O que o projeto faz](#o-que-o-projeto-faz)
2. [Como baixar e executar](#como-baixar-e-executar)
3. [Como usar a tela](#como-usar-a-tela)
4. [Estrutura de pastas](#estrutura-de-pastas)
5. [Conceitos de POO por missão](#conceitos-de-poo-por-missão)
6. [Comunicação com o Backend](#comunicação-com-o-backend)
7. [Problemas comuns](#problemas-comuns)

---

## O que o projeto faz

O FecapStudy ajuda três perfis durante o semestre:

- **Discente:** registra sessões de estudo (Pomodoro, Exercícios ou Leitura), ganha XP e acompanha a meta semanal.
- **Responsável:** define a meta semanal de minutos e recebe alertas de progresso (E-mail, WhatsApp ou Painel).
- **Administrador:** monta trilhas de disciplinas (demonstrado na página de testes).

Cada sessão registrada na tela é calculada pelos objetos em memória **e** gravada no banco MySQL pelo backend em `http://localhost:5080`.

---

## Como baixar e executar

### Pré-requisitos

| Item | Para quê | Observação |
|---|---|---|
| Navegador moderno (Edge, Chrome, Firefox) | Abrir a tela | Nada para instalar |
| Pasta `Backend` da Aula 9 (`BackendEstudoAPI.exe`) | Gravar os dados | Fornecida pelo professor em `Aula_09_Exercicios_Projeto_PI/Backend/` |
| MySQL local (porta 3306) | Persistência real | **Opcional:** sem MySQL o backend salva em `dados_estudo_local.json` |

Nenhum passo exige senha de administrador.

### Passo 1 — Baixar o projeto

1. Baixe o arquivo `FecapStudy.zip`.
2. Clique com o botão direito → **Extrair tudo…**
3. Escolha uma pasta local, de preferência fora do OneDrive (ex.: `C:\Users\seu_usuario\ProjetosPOO\FecapStudy`).

### Passo 2 — Iniciar o Backend C#

1. Abra a pasta `Aula_09_Exercicios_Projeto_PI/Backend/`.
2. Dê dois cliques em **`iniciar_backend.bat`** (ou em `BackendEstudoAPI.exe`).
3. Uma janela de terminal mostra o servidor em `http://localhost:5080`:
   - `Modo de Dados : MySQL Local Conectado (fecap_estudo)` → gravando no MySQL;
   - `Modo de Dados : Fallback Local (dados_estudo_local.json)` → MySQL não encontrado, gravando em arquivo JSON (as rotas funcionam igual).
4. **Deixe essa janela aberta** enquanto usa o sistema.

> Se o MySQL tiver senha no usuário `root`, edite o arquivo `mysql_config.txt` (ao lado do `.exe`) colocando `Pwd=sua_senha;`, ou acesse `http://localhost:5080` e use o botão **Testar e Conectar ao MySQL**.
> O banco `fecap_estudo` e as 4 tabelas são criados automaticamente.

### Passo 3 — Abrir a plataforma

Dê dois cliques em **`index.html`**. O selo no card **Servidor** deve mostrar `online · MySQL` ou `online · JSON local`.

> A tela também funciona **sem** o backend: os cálculos acontecem normalmente e o selo mostra `offline`. Apenas a gravação no banco e o relatório ficam indisponíveis.

### Passo 4 (opcional) — Ver os testes das missões 1 a 8

Abra **`testes.html`** (ou clique em *ver testes das missões 1 a 8* no topo da tela). Cada missão tem uma seção com os resultados dos testes de mesa; os mesmos resultados aparecem no console (F12).

---

## Como usar a tela

| Área | O que fazer | O que acontece por trás |
|---|---|---|
| **Cartões de perfil** | Apenas leitura | `discente.gerarPainelResumo()` e `responsavel.gerarPainelResumo()` (polimorfismo); o XP sobe a cada estudo |
| **Nova sessão de estudo** | Escolha disciplina, modalidade, duração (1 a 480 min) e o dado extra (ciclos / questões e acertos / páginas e resumo). Clique em **Registrar Estudo** | Cria `SessaoPomodoro`, `SessaoExercicios` ou `SessaoLeitura`; chama `meta.registrarAvanco()` e `discente.registrarEstudo()`; envia `POST /api/sessoes` |
| **Meta semanal** | Acompanhe a barra. Para mudar a meta, digite o valor e clique em **Salvar** | `meta.redefinirMeta()` trava entre 30 e 3000 min; envia `POST /api/metas` |
| **Histórico** | Lista as sessões da tela, mais recente primeiro | `sessao.gerarFeedback()` de cada sessão |
| **Notificar Responsável** | Escolha o canal e clique | `ServicoAlertaResponsavel.avaliarDiscente()` usa o `INotificador` escolhido e mostra um aviso na tela |
| **Servidor** | Clique em **Carregar Histórico do Servidor / Relatório** | `GET /api/relatorio/{nome}`: totais de sessões, minutos, XP, % da meta e últimas sessões gravadas no banco |

Valores inválidos (duração 0, negativa ou acima de 480; campos extras negativos) são recusados com mensagem em vermelho, e nada é registrado.

---

## Estrutura de pastas

```
FecapStudy/
├── index.html                 # Tela principal (Missões 9 e 10)
├── testes.html                # Testes de mesa das Missões 1 a 8
├── style.css                  # Estilos da tela
├── README.md
└── js/
    ├── Interfaces.js          # Contratos INotificador e IExportavelJson (Missão 6)
    ├── Disciplina.js          # Missão 1
    ├── MetaEstudo.js          # Missão 2
    ├── SessaoEstudo.js        # Missão 4 (+ paraJson da Missão 6)
    ├── Usuario.js             # Missões 3, 5 e 7 (Usuario, Administrador, Responsavel, Discente)
    ├── Notificadores.js       # Missão 6 (E-mail, WhatsApp, Painel)
    ├── TrilhaEstudo.js        # Missão 7
    ├── PlanoEstudo.js         # Missão 8 (PlanoEstudo + ItemCronograma)
    ├── ServicoAlertaResponsavel.js  # Missão 8
    ├── ClienteBackendEstudo.js      # Missão 10
    ├── ui.js                  # Missão 9: eventos e atualização da tela
    └── testes.js              # Saídas da página de testes
```

A ordem dos `<script>` no HTML importa: classes mãe antes das filhas e `ui.js` por último.

---

## Conceitos de POO por missão

| Missão | Conceito | Onde está no código |
|---|---|---|
| 1 | Abstração, Classe e Objeto | `Disciplina`: campos privados `#codigo`, `#nome`…; carga ≤ 0 vira 2h; `calcularCargaEmMinutos()` e `exibirResumo()` |
| 2 | Encapsulamento | `MetaEstudo`: sem setter de minutos; `registrarAvanco()` aceita só 1 a 480; `redefinirMeta()` trava entre 30 e 3000 e é chamado no construtor |
| 3 | Classe Abstrata e Herança | `Usuario` bloqueia `new Usuario()` com `new.target`; senha sem getter; `Administrador`, `Responsavel` e `Discente` com `extends` / `super` |
| 4 | Polimorfismo Dinâmico | `SessaoEstudo` abstrata; cada modalidade sobrescreve `calcularXp()` e `obterModalidade()`; `gerarFeedback()` reaproveita `super.gerarFeedback()` |
| 5 | Polimorfismo Estático (Sobrecarga) | `Discente.registrarEstudo()` com 3 formas, identificadas por `instanceof` e pela quantidade de argumentos |
| 6 | Interfaces | `INotificador` (3 notificadores) e `IExportavelJson` (`SessaoEstudo` e `MetaEstudo`), verificados por duck typing |
| 7 | Associação e Agregação | `Responsavel.vincularDiscente()` (sem duplicar) + meta por discente; `TrilhaEstudo` recebe disciplinas criadas fora |
| 8 | Composição e Dependência | `PlanoEstudo` cria os `ItemCronograma` internamente e não expõe a lista; `ServicoAlertaResponsavel` não guarda campos |
| 9 | Interface Gráfica | `index.html` + `ui.js`: a tela só lê campos e cria objetos; fórmulas e validações ficam nas classes |
| 10 | Integração com o Backend | `ClienteBackendEstudo` (dependência) usa `fetch` com tempo limite para falar com a API |

Fórmulas de XP:

- **Pomodoro:** `duração × 2 + ciclos × 15`
- **Exercícios:** `duração + acertos × 10`
- **Leitura:** `duração + páginas × 3 + 25` (se fez resumo)

---

## Comunicação com o Backend

Rotas usadas pela tela (`ClienteBackendEstudo.js`):

| Método e rota | Quando é chamada |
|---|---|
| `GET /api/status` | Ao abrir a tela e após cada gravação (selo online/offline) |
| `GET /api/metas?discente={nome}` | Ao abrir a tela, para saber se a meta do discente já existe |
| `POST /api/metas` | Ao abrir (se não houver meta) e ao clicar em **Salvar** |
| `POST /api/sessoes` | A cada **Registrar Estudo** |
| `GET /api/relatorio/{nome}` | No botão **Carregar Histórico do Servidor / Relatório** |

Exemplo do JSON enviado em `POST /api/sessoes` (gerado por `sessao.paraJson()`):

```json
{
  "discenteNome": "André Shiroma",
  "disciplina": "Banco de Dados Avançado",
  "tipoModalidade": "Pomodoro",
  "duracaoMinutos": 50,
  "xpGanho": 130,
  "resumoFeedback": "Pomodoro em Banco de Dados Avançado (50 min) -> +130 XP | 2 ciclo(s) concluído(s)"
}
```

Para conferir o que foi gravado, abra no navegador:

- `http://localhost:5080/api/sessoes`
- `http://localhost:5080/api/relatorio/André%20Shiroma`

---

## Problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| Selo **offline** / "Servidor indisponível" | Backend não está rodando | Execute `iniciar_backend.bat` e recarregue a página (F5) |
| Terminal do backend mostra **Fallback Local** | MySQL parado ou senha diferente | Inicie o serviço MySQL ou ajuste `Pwd=` em `mysql_config.txt`. O sistema continua funcionando com o JSON local |
| A porta 5080 já está em uso | Outro backend aberto | Feche a outra janela do backend |
| Ao redefinir a meta, o relatório continua com a meta antiga (só no modo MySQL) | Limitação do backend fornecido: `POST /api/metas` sempre insere uma linha nova em vez de atualizar | Defina a meta antes de registrar as sessões. No modo JSON local a atualização funciona normalmente |
| Nada aparece na tela | Algum `.js` fora da pasta `js/` | Mantenha a estrutura de pastas do zip; veja erros no console (F12) |
