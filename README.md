# Leilões Caixa — Painel (AP302 + análise de imóveis)

Resumo do que existe neste pacote, para continuar numa conversa nova com o Claude.

## Arquivos

- `index.html` — o app completo (single-file, HTML+CSS+JS, mobile-first). Abra direto no navegador ou peça para o Claude reabrir/editar.
- `setup_supabase.sql` — SQL para rodar uma vez no Supabase (Project → SQL Editor) para criar a tabela usada pelo app. **Se ainda não rodou, rode antes de continuar usando a aba AP302.**

## O que o app faz

- **Aba Imóvel**: calculadora de cenário (SAC, entrada, reforma, prazo, venda, IR) para os imóveis ainda em análise (`gran`, `ap102`, `colorado`, `loja`, `belo`).
- **Aba Lista**: tabela comparativa de todos os imóveis (avaliação, mínimo, classificação).
- **Aba AP302**: dados do imóvel já arrematado (Gran Acrópolis IV, Bloco 07, Apto 302) — dados do arremate, endereço/GPS/Waze, comparáveis de mercado, dívidas (IPTU/condomínio), contatos (síndico/portaria/corretor), calculadora própria (100% à vista, gastos itemizados por quem pagou, resumo de dívida Victor x Ciro), documentos (upload de PDF em memória).

## Integração com banco de dados (Supabase)

- Projeto: `https://eujbnljckzgbsxqqbbqq.supabase.co`
- Tabela: `leiloes_ap302_dados` (criada pelo `setup_supabase.sql`)
- Chave pública (client-side) embutida no `index.html`: usa o novo formato do Supabase, **Publishable key** (`sb_publishable_...`, em Project Settings → API Keys → "Publishable and secret API keys"). A chave antiga no formato JWT (`anon`, começava com `eyJ...`) parou de funcionar depois que o Supabase migrou para esse novo sistema — se o card "Banco de dados" voltar a dar erro de conexão, o primeiro lugar a checar é se essa Publishable key ainda bate com a que está no arquivo.
- Tudo que é editado na aba AP302 salva automaticamente no banco ~0,8s depois de parar de digitar, e recarrega sozinho ao abrir a página. Card "☁️ Banco de dados" no topo da aba mostra o status.
- Ainda existe um botão de backup manual (baixar/restaurar `.json`) como reserva.
- **Segurança**: a tabela usa uma política RLS aberta (`using (true) / with check (true)`) porque o app não tem login — qualquer um com a anon key (visível no HTML) pode ler/escrever nessa tabela específica. Risco considerado baixo (dados não são financeiros sensíveis tipo senha/cartão), mas não tem autenticação de verdade. Se quiser evoluir isso, é o próximo passo natural.

## Regra permanente (lembrar sempre)

Toda vez que o usuário mandar um link novo de imóvel da Caixa para analisar, **perguntar o valor do condomínio** antes de finalizar a análise.

## Pendências / possíveis próximos passos

- Confirmar que o SQL foi rodado e que a gravação no Supabase está funcionando de verdade (não deu para testar no ambiente sandbox anterior por falta de acesso à internet externa).
- `condoMes` da aba AP302 está marcado como não confirmado (`unconfirmed: ['condoMes']`) — valor estimado em R$220/mês, mesma administradora do AP102, mas não confirmado oficialmente.
- Se quiser, dá para adicionar autenticação real ao Supabase no lugar da política aberta.
