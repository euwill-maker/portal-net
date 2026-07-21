# contratar.html — Redesign para conversão em agendamento

**Data:** 2026-07-21
**Arquivo afetado:** `contratar.html` (único; `index.html` não é tocado)
**Objetivo:** aumentar a taxa de leads que concluem o agendamento da instalação.

## Diagnóstico da versão atual

| # | Problema | Impacto |
|---|---|---|
| 1 | CPF obrigatório no passo 2 | Maior ponto de abandono; documento pedido antes do compromisso |
| 2 | Nenhum plano pré-selecionado | Decisão do zero em vez de confirmação |
| 3 | CPF/telefone sem máscara | Erro e frustração no mobile |
| 4 | `alert()` nativo para validação | Aparência de site quebrado |
| 5 | Sem persistência | Sair da página = perder tudo |
| 6 | Sem tela de sucesso | Se o WhatsApp não abrir, o lead se perde |
| 7 | Sem prova social nem urgência | Nada sustenta a decisão |
| 8 | Sem eventos de tracking | Impossível saber onde o funil vaza |
| 9 | Fonte Segoe UI (site usa Nunito) | Parece outro site |

## Decisões tomadas

- **CPF:** vira opcional, rotulado "opcional — pode enviar depois".
- **Ordem dos passos:** invertida. Agendamento antes dos dados pessoais (compromisso primeiro).
- **Prazo:** "Instalação rápida — normalmente em até 24h" (hedgeado, não é promessa dura).
- **Escassez:** NÃO usar "poucos horários restantes" (seria inventado). Usar apenas o selo factual "Mais rápido" no primeiro dia disponível.
- **Prova social:** "+5 mil clientes em Aparecida de Goiânia" + link real do Google — números já usados no `index.html`.

## Fluxo novo

```
1. Plano          → "Mais vendido" pré-selecionado
2. Instalação     → dia (1º marcado "Mais rápido") + período
3. Seus dados     → nome, WhatsApp, endereço, bairro | CPF e referência opcionais
4. Confirmar      → revisão + enviar pelo WhatsApp
5. Sucesso        → confirmação + reabrir WhatsApp
```

## Componentes

**Persistência (`localStorage`)**
Chave `pn_contratar_v1`. Salva estado a cada interação e a cada `input`. Ao carregar, se existir estado com plano ou campos preenchidos, mostra faixa *"Quer continuar de onde parou?"* com botões Continuar / Recomeçar. Limpa após envio bem-sucedido.

**Máscaras e validação**
- CPF: `000.000.000-00`, aceita vazio (opcional); se preenchido, exige 11 dígitos.
- WhatsApp: `(00) 00000-0000`, exige 10-11 dígitos.
- Nome: mínimo 2 palavras.
- Erros: faixa vermelha no topo do card + destaque no campo + scroll até o primeiro erro. Nenhum `alert()`.

**Datas**
8 dias úteis a partir de amanhã, domingos excluídos. O primeiro recebe a etiqueta "Mais rápido".

**Tela de sucesso**
Substitui o card. Mostra plano, dia e período escolhidos, botão "Abrir WhatsApp de novo" e link de volta ao site. Limpa o `localStorage`.

**Botão flutuante de ajuda**
Fixo no canto inferior. Abre WhatsApp com o que o lead já preencheu ("estou contratando, parei no passo X"). Converte abandono em contato.

**Tracking**
`window.dataLayer.push({event: ...})` em: `contratar_iniciado`, `plano_escolhido`, `data_escolhida`, `dados_preenchidos`, `pedido_enviado`. Sem dependência externa — funciona mesmo sem GTM instalado.

**Visual**
Fonte Nunito (igual `index.html`). Paleta atual mantida. Alvos de toque ≥ 44px. Revisão mobile-first.

## Fora de escopo

Preços, planos, número do WhatsApp, `index.html`, backend/CRM.

## Critério de sucesso

Lead consegue ir de "abriu a página" a "WhatsApp enviado" sem digitar CPF, sem ver um `alert()`, e sem perder dados se sair no meio.
