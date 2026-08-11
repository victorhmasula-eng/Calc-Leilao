# Leilões Caixa — Painel (AP302 + análise de imóveis)

Resumo do que existe neste pacote, para continuar numa conversa nova com o Claude.

## Arquivos

- `index.html` — o app completo (single-file, HTML+CSS+JS, mobile-first). Abra direto no navegador ou peça para o Claude reabrir/editar.
- `setup_supabase.sql` — SQL para rodar uma vez no Supabase (Project → SQL Editor) para criar a tabela usada pelo app. Já foi rodado no projeto atual; só precisaria rodar de novo se trocar de projeto Supabase.

## O que o app faz

- **Aba Imóvel**: calculadora de cenário (SAC, entrada, reforma, prazo, venda, IR) para os imóveis ainda em análise (`gran`, `ap102`, `gran302`, `colorado`, `loja`, `belo`).
- **Aba Lista**: tabela comparativa de todos os imóveis (avaliação, mínimo, classificação, status).
- **Aba AP302** (tela inicial do app): dados do imóvel já arrematado (Gran Acrópolis IV, Bloco 07, Apto 302) — dados do arremate, endereço/GPS/Waze, comparáveis de mercado, dívidas (IPTU/condomínio), contatos (síndico/portaria/corretor), calculadora própria (100% à vista, gastos itemizados por quem pagou, resumo de dívida Victor x Ciro), documentos (upload de PDF em memória).

## Persistência de dados (todas as abas)

Mais de uma pessoa usa o app (Victor e Ciro), então tudo que é editado precisa ficar salvo num lugar que os dois leem — não só no navegador de quem editou. O app usa o Supabase como fonte de verdade compartilhada, de forma **silenciosa** (sem card de status na tela — isso já foi tentado antes e ficou poluindo a interface):

- Projeto: `https://eujbnljckzgbsxqqbbqq.supabase.co`, tabela `leiloes_ap302_dados` (`id text primary key, data jsonb`), RLS aberta.
- Chave client-side embutida no `index.html`: **Publishable key** (`sb_publishable_...`, em Project Settings → API Keys). Se parar de conectar, o primeiro lugar a checar é se essa chave ainda bate com a do projeto.
- Duas linhas na tabela: `id='ap302'` (tudo da aba AP302 — arremate, dívidas, contatos, gastos, docs, calculadora) e `id='app_state'` (calculadora da aba Imóvel por imóvel — preço, entrada, reforma, prazo, venda, premissas avançadas — mais o status de cada imóvel na aba Lista).
- Cada edição salva local (`localStorage`, cache instantâneo, evita tela travando esperando rede) e manda pro Supabase ~0,5s depois de parar de digitar (debounce). Ao abrir a página, primeiro usa o cache local (não pisca em branco) e, assim que a resposta do banco chega, re-renderiza com a versão mais atual — assim, se a outra pessoa editou de outro navegador, a tela se atualiza sozinha.
- Trava contra condição de corrida: nada é salvo no banco antes da primeira leitura da nuvem terminar, pra não sobrescrever por engano uma edição que a outra pessoa acabou de fazer.
- Documentos (PDFs) da aba AP302 continuam **só na sessão do navegador** (não entram nesse sync) — ficariam grandes demais em base64 dentro do JSON.
- Card "💾 Backup dos dados" (aba AP302) com "Baixar cópia (.json)" / "Restaurar cópia" continua existindo como reserva extra, independente do Supabase.

## Regra permanente (lembrar sempre)

Toda vez que o usuário mandar um link novo de imóvel da Caixa para analisar, **perguntar o valor do condomínio** antes de finalizar a análise.

## Pendências / possíveis próximos passos

- `condoMes` da aba AP302 está marcado como não confirmado (`unconfirmed: ['condoMes']`) — valor estimado em R$220/mês, mesma administradora do AP102, mas não confirmado oficialmente.
