# Dashboard — Alvarás e Acordos

Painel de recebimentos e repasses do escritório Borges Macedo Advocacia, alimentado em
tempo real pela planilha **"Alvarás e Acordos"** do Google Sheets.

**Acesso exclusivo das lideranças.** Só os perfis **Administração** e **Lideranças** têm
cofre neste painel — a credencial do perfil Equipe não decifra nada aqui.

## Acesso

O ID da planilha e a chave da API não existem em texto puro no código: ficam cifrados com
**AES-256-GCM** dentro de `COFRES`, um cofre por perfil autorizado, com a chave derivada
por **PBKDF2-SHA256 (310.000 iterações)** do login + senha. Senha errada faz a decifragem
falhar — não existe verificação para pular.

O painel é acessado pela **Central de Dashboards**
(https://borgesmacedoadvocacia.github.io/); o acesso direto é remanejado para lá.

## O que é exibido

| Seção | Conteúdo |
|---|---|
| Resumo Executivo | Valor creditado, honorários do escritório com take rate, repasse a parceiros, valor devido ao cliente, ticket médio, diferença solicitado × creditado, repasses em aberto e prazo médio de repasse. Filtro por exercício (2024/2025/2026/todos) |
| Para Onde Vai o Dinheiro | Rateio do valor creditado entre escritório, parceiros e cliente, com a parcela ainda sem rateio informado; take rate por tipo de honorários (Êxito, Êxito + Sucumbencial, Sucumbencial) |
| Evolução Mensal | Valor creditado e honorários mês a mês, com a linha de take rate no eixo secundário |
| Comparativo Anual | Tabela ano a ano com alvarás, creditado, honorários, take rate, ticket médio e repasses — sempre sobre a base completa |
| Pendências Financeiras e Prazos | Honorários a receber, valores a repassar ao cliente, prazo médio e receita fora do fluxo de honorários, com tabela filtrável |
| Concentração de Clientes | Maiores clientes por valor creditado e distribuição por faixa de ticket |
| Pontos de Atenção | Alertas automáticos de leitura executiva (ver abaixo) |

## Leitura executiva embutida

Os "Pontos de Atenção" são gerados a partir dos números, na ótica de quem responde pelo
resultado do escritório:

- **Alvarás creditados sem destino do dinheiro informado** — linhas com valor creditado
  mas sem honorários/parceiro/cliente preenchidos, quebradas por ano. É o principal ponto
  de conciliação da planilha, e contamina o take rate enquanto não for resolvido.
- **Honorários ainda não repassados ao BM** — receita reconhecida que não entrou no caixa.
- **Valores a repassar aos clientes** — além do risco reputacional, retenção prolongada
  expõe o escritório a questionamento ético.
- **Prazo de repasse acima do recomendável** — referência de 5 dias úteis.
- **Creditado abaixo do solicitado** — possíveis glosas, erro de cálculo ou lançamento
  incompleto. Calculado só sobre alvarás que têm os **dois** valores lançados.
- **Receita fora do fluxo de honorários** — subavaliação do resultado no financeiro.
- **Concentração da carteira** — participação do maior cliente e dos cinco maiores.
- **Take rate** — quanto de cada R$ 100 creditados fica no escritório.
- **Indicação e avaliação** — o repasse é o pico de satisfação do cliente e hoje esse
  momento não é aproveitado (colunas praticamente sem preenchimento).

## Como funciona

- Lê as abas **2024**, **2025** e **2026** pela **Sheets API v4** (`batchGet`).
- Toda leitura é **por nome de cabeçalho**, com fallback pela letra da coluna: a aba de
  2024 tem menos colunas que as demais (não tem "Repasse Tatiana" nem "Lançado no Fluxo"),
  e mesmo assim é lida corretamente.
- Repasse é considerado quitado quando está como **Pago**, **Dispensado** ou **Não há**.
- Atualização automática a cada 5 minutos e ao voltar para a aba do navegador.
- Arquivo único `index.html`, sem build e sem servidor.

> A leitura **não** usa o endpoint `gviz`: nesta planilha ele trunca o resultado.
> A Sheets API v4 devolve a base completa.
