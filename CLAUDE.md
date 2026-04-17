# SalárioNet — Notas do Projeto

## O que é
Calculadora de salário líquido para Portugal 2025. Single-page app em HTML/CSS/JS puro, sem frameworks. Deployada no Vercel via GitHub.

## Repo & Deploy
- **GitHub:** https://github.com/Gavino2003/salarionet
- **Deploy:** Vercel (ligar em vercel.com/new → importar repo)
- **Ficheiro principal:** `index.html` (toda a app num único ficheiro)
- **Config Vercel:** `vercel.json` (clean URLs, security headers)

## Stack
- HTML + CSS puro (sem frameworks, sem npm, sem build step)
- Fonte: Inter (Google Fonts — única dependência externa)
- JS vanilla ES6+, `'use strict'`
- Dark mode por defeito, toggle light/dark com `localStorage`

## Design System
- Cores principais: `--green-dark: #1a472a`, `--green-mid: #2d6a4f`, `--green-accent: #52b788`
- Background dark: `--bg-primary: #0f1512`, cards: `--bg-card: #1c2820`
- Fonte: Inter
- Mobile-first, breakpoint principal: 520px (formulário), 768px (sticky bottom ad), 1100px (rails laterais)

## Lógica de Cálculo

### Conta de Outrem
- **SS trabalhador:** 11% do bruto
- **SS patronal:** 23,75% do bruto (custo empresa)
- **IRS:** tabelas de retenção na fonte AT 2025 (ver array `ESCALOES` no JS)
- **Escalões IRS 2025 mensais** (solteiro, sem dependentes):
  - Até €792: 0%
  - €792–€1.007: 13%
  - €1.007–€1.739: 18,5% (parcela: €55,39)
  - €1.739–€2.155: 28% (parcela: €233,52)
  - €2.155–€3.471: 35% (parcela: €384,37)
  - €3.471–€5.765: 37% (parcela: €453,80)
  - €5.765–€7.716: 39,5% (parcela: €598,17)
  - >€7.716: 40% (parcela: €636,75)
- **Casado 1 titular:** divide bruto por 2, calcula IRS, multiplica por 2
- **Casado 2 titulares:** usa mesmos escalões de solteiro
- **Dependentes:** -€21,40 por dependente ao IRS calculado
- **Duodécimos:** adiciona (bruto/12)×2 ao líquido mensal (com SS e IRS proporcionais)
- **Líquido anual sem duodécimos:** ×14 meses; com duodécimos: ×12

### Recibo Verde
- **SS:** 21,4% do bruto (coeficiente 0,70 × taxa SS)
- **IRS:** 25% fixo (taxa geral atividade profissional liberal)
- Sem subsídios de férias/Natal
- Sem SS patronal

### Subsídio de Alimentação
- Configurável: valor/dia (default €6,00) × dias úteis (default 22)
- Considerado totalmente isento (dentro do limite legal)
- Anual: ×11 meses

## Zonas de Anúncios (10 total)
Todos marcados com `<!-- Google AdSense: substituir pelo teu código -->`.

| ID / Posição | Formato | Notas |
|---|---|---|
| `#ad-sticky-top` | 728×90 / 320×50 | Sticky, z-index 200, topo da página |
| `.rail-left` + `.rail-right` | 160×600 | Fixo lateral, visível ≥1100px |
| `.ad-strip` (abaixo header) | 728×90 | Primeira posição visível |
| Mid-form (dentro do card) | Responsivo 100%×50px | Entre dependentes e toggles |
| `.ad-leader` (entre form e resultados) | 728×90 | Zona quente pós-calcular |
| Após hero result | 336×280 | Imediatamente abaixo do valor principal |
| Entre stats e barra visual | 728×90 | Scroll pause point |
| Antes da tabela de detalhes | 300×250 | |
| Após tabela de detalhes | 300×250 | |
| Pré-footer | 728×90 | |
| `#ad-sticky-bottom` | 320×50 | Fixo bottom, mobile only (≤768px), botão X para fechar |

## Funcionalidades
- **Partilhar resultado:** gera URL com params (`?bruto=1500&tipo=coe&sit=solteiro&dep=0&alim=0&duo=0`)
- **Auto-fill:** ao carregar com params na URL, preenche e calcula automaticamente
- **Limpar:** reset completo + remove params da URL
- **Toast:** feedback visual para ações (copiar link, erros)
- **Enter key:** calcular ao pressionar Enter no campo bruto
- **Tooltips `?`** em todos os campos com explicação

## Resultados Mostrados
1. Salário líquido mensal (hero)
2. Desconto SS (€ e %)
3. Retenção IRS (€ e %)
4. Custo total para a empresa
5. Salário líquido anual
6. Subsídio de alimentação (se ativo)
7. Barra visual: proporção líquido / IRS / SS
8. Tabela breakdown detalhado

## SEO
- `<title>`, meta description, keywords, Open Graph completo
- Sem sitemap (página única)

## Para atualizar tabelas IRS
Editar o array `ESCALOES` no JavaScript (linha ~linha 270 do index.html):
```js
const ESCALOES = [
  [limite, taxa, parcela_abater],
  ...
];
```

## Próximos passos possíveis
- Adicionar Google Analytics / tag AdSense real
- Tabelas IRS para Madeira e Açores (taxas diferentes)
- Comparador lado-a-lado (conta outrem vs recibo verde)
- Calculadora inversa (líquido → bruto)
- PWA (manifest + service worker para uso offline)
