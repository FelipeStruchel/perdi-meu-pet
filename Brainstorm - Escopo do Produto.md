---
tipo: brainstorm
status: aprovado
criado: 2026-10-01
atualizado: 2026-10-01
tags:
  - perdi-meu-pet
  - escopo
  - produto
---

# Brainstorm — Escopo do Produto

> [!info] Contexto
> Brainstorm de **produto e escopo** a partir de [[Escopo]]. Arquitetura fica de fora por enquanto.
> Estratégia: começar em **região específica** e escalar para **nível nacional**.

## Visão geral

Plataforma para reencontrar pets perdidos com dois tipos de post — **Perdi** e **Encontrei** — e uma comunidade que ajuda a cruzar os dois através de avistamentos, sugestões de vínculo e um heatmap que esfria com o tempo.

Fase 1: plataforma web. Fase 2: app mobile React Native (Android/iOS) com foto nativa + geolocalização embutida — mesmo fluxo.

## Escopo do MVP — resumo

| Entra no MVP | Fica no Roadmap |
|---|---|
| Posts **Perdi** e **Encontrei** (*Está comigo* / *Vi na rua*) | App mobile React Native |
| Matching por proximidade + sugestão da comunidade | Parceiros (ONGs, clínicas, pet shops) |
| Avistamentos (logado e anônimo) + heatmap com decaimento | Notificação por WhatsApp/SMS |
| Score oculto + selos por tier | Doações → parceiros verificados → premium |
| Chat interno com avisos antigolpe | Blacklist e checagem por IA |
| Login social + e-mail/senha | |
| Notificações: plataforma, e-mail, Web Push | |
| Alertas de vizinhança opt-in | |
| Denúncias + painel admin + ocultação automática | |
| Ciclo de vida do post + História do Reencontro | |
| Localização pública aproximada (~500 m) | |

## Tipos de post

### Perdi
- Foto, nome a que atende, última localização (mapa).
- Recebe **avistamentos** da comunidade → alimentam o **heatmap** do animal, que esfria com o tempo.
- Dono é notificado a cada avistamento/sugestão (ver [[#Notificações]]).

#### Campos do Perdi
**Obrigatórios**
- Espécie (cão / gato / outro)
- Foto (mín. 1, até 5)
- Nome a que atende
- Última localização (mapa) + data/hora do último avistamento

**Opcionais** (ajudam no matching)
- Porte (P/M/G), cor/pelagem predominante, sexo, raça ou SRD
- Sinais particulares (cicatriz, mancha, coleira, microchip sim/não)
- Temperamento (ex.: "foge de estranhos", "não chegue perto")
- Condição de saúde / medicação urgente
- Descrição livre
- **Recompensa**: apenas *"Há recompensa"* (sim/não) — **valor nunca exibido** (evita atrair golpistas)

> [!note] Matching
> Espécie, porte, cor e sexo também existem no post de Encontrei e servem como **filtros extras** além da distância.

### Encontrei
Dois subtipos:
- **Está comigo** — a pessoa resgatou e está com o animal (ou em abrigo/clínica).
- **Vi na rua** — animal aparentemente perdido, sem custódia. Também gera heatmap próprio.

#### Campos do Encontrei
Mesmos campos do [[#Campos do Perdi]], **exceto** "nome a que atende" e "recompensa".
- Obrigatórios: subtipo, espécie, foto (mín. 1, até 5), localização onde viu/achou + data/hora.
- Opcionais: porte, cor/pelagem, sexo, raça/SRD, sinais particulares, temperamento, condição de saúde, descrição livre.

#### Vínculo de um "Vi na rua" com um Perdi
- O local do Vi na rua e seus avistamentos **migram para o heatmap do Perdi**.
- Seguem as mesmas regras já definidas: peso por confiabilidade da fonte ([[#Heatmap]]), dono pode remover avistamentos (conta como rejeição), exibição pública só via heatmap ([[#Privacidade de localização]]).

## Fluxo do "Encontrei" (matching)

1. Pessoa cria o post (subtipo) e marca a **geolocalização** de onde viu/achou.
2. Plataforma lista os posts de **Perdi** mais próximos, **ordenados por distância**, com foco na foto (primeiro filtro para facilitar o match).
3. Reconheceu → **vincula** ao post de Perdi.
4. Não reconheceu → o registro fica **solto**.
5. Registro solto: a **comunidade pode sugerir vínculo** com algum post de Perdi.
6. Sugestão vai para o **dono do post de Perdi**, que **confirma** ("é meu") ou **rejeita**.

> [!important] Regra de ouro
> Só o dono do post de Perdi efetiva um vínculo. Terceiros apenas **sugerem**.

## Heatmap

- Cada avistamento é um ponto de calor com **decaimento exponencial**: muito quente logo após o registro, esfria rápido nas primeiras horas, deixa resíduo fraco por mais tempo.
- **Intensidade inicial por confiabilidade da fonte**:
  - Tier alto → começa mais quente.
  - Tier baixo → começa mais frio.
  - Anônimo (aprovado pelo dono) → mais frio.
  - **Com foto** pesa mais que sem foto.
- Objetivo: mostrar onde o animal **provavelmente está**, não só onde alguém clicou.
- Meia-vida e pesos são **ajuste fino** — calibrar com dados reais.

## Reputação e comunidade

- **Score oculto** de **0 a 1000** por perfil.
- Usuário **não vê o score** — vê apenas um **selo por tiers** que vai subindo.
  - Nomes de tiers a definir (ex.: Novato → Colaborador → Farejador → Guardião).
- Tiers funcionam como **feedback** e reforçam o senso de **comunidade**.
- Sobe score: sugestões de vínculo **confirmadas** pelo dono; participação em **reencontros** (quem encontrou, quem sugeriu, avistamentos relevantes).
- Desce score: sugestões **rejeitadas** em excesso, avistamentos **removidos** pelo dono, denúncias procedentes contra o perfil, denúncias improcedentes recorrentes feitas pelo perfil.

### Antitroll / antispam
- Só o dono confirma vínculos.
- Rejeições pesam no score → sugestões de perfis com score baixo perdem prioridade ou são limitadas.
- **Limite diário de sugestões**, maior para tiers altos.
- Sugestões de tiers altos aparecem **primeiro** para o dono.

## Moderação e denúncias

- Qualquer usuário logado pode **denunciar** posts, mensagens de chat e perfis.
- **Painel admin** simples para revisar denúncias.
- **Ocultação automática**: conteúdo denunciado por **N usuários distintos** fica oculto até revisão.
  - Denúncias de usuários com **tier alto pesam mais**.
- Denúncia **procedente** → reduz o score do denunciado.
- Denúncia **improcedente** recorrente → reduz o score do denunciante. *(validar)*

## Acesso e login

| Ação | Login? |
|---|---|
| Navegar / ver posts e mapas | Não |
| Registrar avistamento | Não (anônimo permitido) |
| Sugerir vínculo | **Sim** |
| Criar post (Perdi / Encontrei) | **Sim** |

- **Avistamento anônimo** entra como **sugestão pendente**: o dono do post avalia antes de entrar no heatmap.
- **Avistamento logado** entra **direto** no heatmap; o dono pode **remover** qualquer avistamento do seu heatmap — a remoção conta como **rejeição** no score de quem registrou.
- Login: **social (Google/Apple)** + **tradicional (e-mail e senha)**.

## Contato entre as partes

- **Chat interno** da plataforma é o padrão — ninguém vê telefone de ninguém.
- Dono pode **opcionalmente exibir WhatsApp** no post.
  - Exige leitura de **aviso de segurança obrigatório** com **confirmação dupla** antes de ativar.
  - Quem **visualiza** o WhatsApp no post **não** passa por confirmação dupla.
- **Ao abrir qualquer chat**, aparece por padrão um aviso de "cuidado com golpes" (ex.: "nunca pague antes de ver o animal").
- **Detecção no chat**: se uma mensagem contém **número de telefone** ou **pedido para migrar de plataforma** (WhatsApp, Instagram, etc.), o destinatário vê um **aviso com confirmação dupla** antes de exibir/seguir.
- Histórico do chat pode embasar **denúncias** e impactar o score.

> [!warning] Risco principal
> Golpes de extorsão ("estou com seu pet, manda um Pix") e exposição de dados pessoais.

## Privacidade de localização

- Mapa público mostra **área aproximada** (~**500 m**, ponto deslocado — cobre algumas quadras), nunca o ponto exato.
  - Vale para a última localização do **Perdi** e para o local do **Encontrei**.
- **Ponto exato** só visível para as partes envolvidas (após vínculo confirmado ou dentro do chat).
- **Avistamentos** são armazenados com ponto exato, mas exibidos publicamente apenas via **heatmap** (que já agrega/difunde a área).
- Área ampla = **área de busca**: motiva a comunidade a procurar na região, em vez de ir a um ponto só.

## Notificações

Canais do MVP:
- **Na plataforma** (central de notificações).
- **E-mail** (garantia/fallback).
- **Web Push** (tempo real, gratuito) → vira **push nativo** quando o app mobile chegar.

Eventos que notificam o dono do post de Perdi:
- Novo avistamento (logado ou anônimo pendente).
- Nova sugestão de vínculo.
- Nova mensagem no chat.

Eventos que notificam o autor do post de Encontrei / quem sugeriu:
- Vínculo confirmado ou rejeitado pelo dono.
- Nova mensagem no chat.

Usuários com alertas ativos: novos posts na área (ver [[#Alertas para a comunidade]]).

> [!tip] Futuro
> WhatsApp/SMS ficam para depois (custo por mensagem + templates aprovados) — candidato a recurso premium.

## Alertas para a comunidade

- **Opt-in configurável** pelo usuário logado:
  - Liga/desliga alertas.
  - **Raio**: 1 / 3 / 5 / 10 km.
  - **Tipo**: Perdi, Encontrei ou ambos.
  - **Múltiplas áreas** (ex.: casa, trabalho).
- Alertas chegam pelos mesmos canais de [[#Notificações]].

## Ciclo de vida do post

- **Dono encerra** o post: *Reencontrado* ou *Desisti/Outro*.
- **Expiração por inatividade**: após ~60 dias sem atualização, a plataforma pergunta "ainda procurando?". Sem resposta → **arquivado** (pode ser reativado).
- **Reencontro via vínculo fecha o ciclo**:
  - Encerra também o post de **Encontrei** vinculado.
  - Dá **score** a quem ajudou (quem encontrou, quem sugeriu o vínculo, avistamentos relevantes).
  - Convida o dono a publicar uma **História do Reencontro** (opcional, com foto) → vitrine/divulgação e marketing orgânico.

## Lançamento

- **Região-foco**: **Bauru (SP)** e cidades próximas.
- **Cadastro aberto para todo o Brasil** — pet perdido em outra cidade não fica sem ajuda.
- Divulgação e esforço de comunidade concentrados na região-foco.
- **Métrica de expansão**: volume de posts/usuários por cidade → expandir para onde houver tração orgânica.

## Sustentabilidade financeira

Evolução em etapas, condicionada ao sucesso de cada fase:

1. **MVP — gratuito, sem monetização.** Bancado pelo projeto; serve como **prova de conceito**.
2. **Doações** (Pix, Apoia.se ou similar) — possivelmente com selo de apoiador. → *Roadmap*
3. **Parceiros pagantes** (clínicas, pet shops, etc.) com **selo de verificado** — marketing indireto para eles. → *Roadmap*
4. **Recursos premium para o dono** — só se justificar um custo real nosso (ex.: WhatsApp/SMS). Pode ser "apoio" simbólico em vez de vantagem. → *Roadmap, última prioridade, em aberto*

> [!warning] Princípio
> Nada pago pode **esconder ou rebaixar** posts gratuitos — o produto é de causa.

## Roadmap (pós-MVP)

- **App mobile** React Native (Android/iOS) — foto nativa com geolocalização, push nativo.
- **Parceiros** (ONGs, clínicas veterinárias, pet shops) com perfil especial que recebe todos os alertas da sua região — alavanca de lançamento regional.
- **WhatsApp/SMS** como canal de notificação (possível recurso premium).
- **Monetização em etapas**: doações → parceiros verificados pagantes → (talvez) premium. Ver [[#Sustentabilidade financeira]].
- **Filtros automáticos na criação**: blacklist de palavras e checagem por IA (ex.: foto é de um animal?).

## Em aberto

- [x] ~~Avistamento logado entra direto no heatmap?~~ Sim; dono pode remover (conta como rejeição).
- [x] ~~Campos do post de Encontrei~~ — ver [[#Campos do Encontrei]].
- [x] ~~Vi na rua vinculado migra para o heatmap do Perdi?~~ Sim — ver [[#Vínculo de um "Vi na rua" com um Perdi]].
- [x] ~~A confirmação dupla vale também para quem visualiza o WhatsApp?~~ Não — vale para contato detectado no chat.
- [ ] Nomes e faixas de score de cada tier.
- [x] ~~Encerramento do post~~ — ver [[#Ciclo de vida do post]].
- [ ] Prazo exato de expiração (60 dias é sugestão) e regras para posts de Encontrei.
- [x] ~~Canais de notificação~~ — ver [[#Notificações]].
- [x] ~~Moderação e denúncias~~ — ver [[#Moderação e denúncias]].
- [ ] Meia-vida do heatmap e pesos por fonte (calibrar com dados reais).
- [ ] Valor de **N** para ocultação automática e peso por tier.
- [x] ~~Estratégia de lançamento regional~~ — ver [[#Lançamento]].

## Decisões
- 2026-10-01 — Arquitetura fora deste brainstorm.
- 2026-10-01 — Posts **Perdi** e **Encontrei** separados; Encontrei com subtipos *Está comigo* / *Vi na rua*.
- 2026-10-01 — Matching por proximidade + sugestão da comunidade com confirmação do dono.
- 2026-10-01 — Score oculto 0–1000 com selo visível por tiers.
- 2026-10-01 — Avistamento anônimo permitido (entra como pendente); sugestão e post exigem login.
- 2026-10-01 — Chat interno padrão; WhatsApp opcional com aviso + confirmação dupla.
- 2026-10-01 — Aviso antigolpe ao abrir chat; confirmação dupla quando o chat detecta telefone/pedido de contato externo.
- 2026-10-01 — Post encerra pelo dono ou expira por inatividade; reencontro via vínculo fecha o ciclo, pontua ajudantes e gera História do Reencontro.
- 2026-10-01 — Notificações: plataforma + e-mail + Web Push (base para push nativo no app).
- 2026-10-01 — Alertas de vizinhança opt-in configuráveis no MVP; parceiros (ONGs/clínicas/pet shops) no roadmap.
- 2026-10-01 — Campos do Perdi definidos; recompensa só como sim/não, sem valor.
- 2026-10-01 — Localização pública aproximada (~500 m); ponto exato só para partes vinculadas; avistamentos públicos só via heatmap.
- 2026-10-01 — Moderação: denúncias + painel admin + ocultação automática com peso por tier; blacklist e IA no roadmap.
- 2026-10-01 — Heatmap com decaimento exponencial e intensidade inicial por confiabilidade (tier, anônimo, foto).
- 2026-10-01 — Lançamento com foco em Bauru e região; cadastro aberto para todo o Brasil.
- 2026-10-01 — MVP gratuito (prova de conceito); depois doações, depois parceiros verificados; premium em aberto, última prioridade.
- 2026-10-01 — Avistamento logado entra direto no heatmap; dono pode remover (conta como rejeição no score).
- 2026-10-01 — Campos do Encontrei = Perdi sem nome/recompensa; Vi na rua vinculado migra local e avistamentos para o heatmap do Perdi, com as mesmas regras.
