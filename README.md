# 📊 Dashboard de Vendas Angola — Power BI

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Medidas%20Avançadas-1F3864?style=flat)
![Status](https://img.shields.io/badge/Status-Concluído-4ADE80?style=flat)
![Registos](https://img.shields.io/badge/Dados-500%20Transações-00C2D4?style=flat)

> Análise completa do desempenho comercial de uma empresa de tecnologia com operações em 5 províncias angolanas. Modelo Star Schema com 4 tabelas relacionadas e medidas DAX avançadas.

---

## 📸 Pré-visualização

```
┌─────────────────────────────────────────────┐
│  📊 VISÃO GERAL   │ 📦 PRODUTOS             │
│  245M Kz Vendas   │ +18.5% Crescimento YoY  │
│  ────────────────────────────────────────── │
│  [Gráfico Linhas — Evolução Mensal]         │
│  [Gráfico Rosca — Quota por Categoria]      │
├──────────────────┬──────────────────────────┤
│  👤 VENDEDORES   │  🗺️ REGIÕES              │
│  Maria João #1   │  Luanda 58% da receita   │
└──────────────────┴──────────────────────────┘
```

---

## 🎯 Objetivos do Projeto

- Monitorizar o desempenho de vendas em tempo real
- Identificar os produtos e vendedores mais rentáveis
- Analisar a distribuição geográfica das vendas por província angolana
- Calcular crescimento Year-over-Year (YoY)

---

## 📁 Estrutura de Ficheiros

```
PBI1_Vendas/
├── README.md                    ← Este ficheiro
├── PBI1_Dados_Vendas.xlsx       ← Fonte de dados (importar no Power BI)
│   ├── fVendas                  (500 linhas — tabela de factos)
│   ├── dProdutos                (12 produtos — dimensão)
│   ├── dVendedores              (8 vendedores — dimensão)
│   └── dRegioes                 (5 regiões — dimensão)
└── GUIA_POWER_BI.md             ← Instruções passo a passo
```

---

## 🗂️ Modelo de Dados — Star Schema

```
         dProdutos (12)
              │  1
              │  *
dVendedores ──┼──► fVendas (500) ◄── dRegioes (5)
    (8)       │  *              *
              │
         dCalendário (opcional)
```

**Relacionamentos:**
- `fVendas[ID_Produto]` → `dProdutos[ID_Produto]` (N:1)
- `fVendas[ID_Vendedor]` → `dVendedores[ID_Vendedor]` (N:1)
- `fVendas[ID_Região]` → `dRegioes[ID_Região]` (N:1)

---

## 📐 Medidas DAX

```dax
-- Total de Vendas
Total Vendas = SUM(fVendas[Valor_Total])

-- Ticket Médio por Transação
Ticket Médio = DIVIDE([Total Vendas], COUNTROWS(fVendas))

-- Crescimento Year-over-Year
Crescimento YoY =
    VAR VendasAnoAtual =
        CALCULATE([Total Vendas], YEAR(fVendas[Data]) = YEAR(TODAY()))
    VAR VendasAnoAnterior =
        CALCULATE([Total Vendas], YEAR(fVendas[Data]) = YEAR(TODAY()) - 1)
    RETURN
        DIVIDE(VendasAnoAtual - VendasAnoAnterior, VendasAnoAnterior)

-- Margem Média
Margem Total = AVERAGEX(fVendas, fVendas[Margem_Pct])

-- Ranking de Vendedores
Rank Vendedor =
    RANKX(
        ALL(dVendedores[Nome]),
        [Total Vendas],
        ,
        DESC,
        DENSE
    )

-- % do Total de Vendas
% do Total =
    DIVIDE([Total Vendas], CALCULATE([Total Vendas], ALL(fVendas)))
```

---

## 🖥️ Como Abrir no Power BI Desktop

### Pré-requisitos
- [Power BI Desktop](https://powerbi.microsoft.com/pt-br/desktop/) (gratuito) instalado
- Ficheiro `PBI1_Dados_Vendas.xlsx` desta pasta

### Passo a Passo

**1. Importar os dados**
```
Power BI Desktop → Início → Obter Dados → Excel
→ Seleccionar PBI1_Dados_Vendas.xlsx
→ Marcar TODAS as sheets: fVendas, dProdutos, dVendedores, dRegioes
→ Clique Carregar
```

**2. Verificar tipos de dados no Power Query**
```
Início → Transformar Dados → Power Query Editor
→ fVendas[Data]: alterar para tipo "Date"
→ fVendas[Valor_Total]: alterar para "Decimal Number"
→ fVendas[Desconto_Pct]: alterar para "Decimal Number"
→ Fechar e Aplicar
```

**3. Criar relacionamentos (Model View)**
```
Vista → Modelo
→ Arrastar fVendas[ID_Produto] para dProdutos[ID_Produto]
→ Arrastar fVendas[ID_Vendedor] para dVendedores[ID_Vendedor]
→ Arrastar fVendas[ID_Região] para dRegioes[ID_Região]
→ Verificar que todos são relações N:1 (Many-to-One)
```

**4. Criar as medidas DAX**
```
Clique direito em fVendas → Nova Medida
→ Copiar cada medida DAX da secção acima
→ Guardar em pasta "📊 Medidas"
```

**5. Construir as 4 páginas**

| Página | Visuais Principais |
|--------|-------------------|
| 📊 Visão Geral | KPI Cards (4x) · Gráfico de Linhas · Gráfico de Rosca |
| 📦 Produtos | Gráfico de Barras · Tabela ordenável |
| 👤 Vendedores | Gráfico de Barras · Tabela com ranking |
| 🗺️ Regiões | Gráfico de Barras · Mapa (opcional) |

**6. Adicionar Slicers (Filtros)**
```
Inserir → Segmentação de Dados
→ Ano (da coluna Data)
→ Trimestre
→ Região
→ Categoria de Produto
```

---

## 📊 KPIs Principais

| KPI | Valor | Fórmula DAX |
|-----|-------|------------|
| Total Vendas | 245M Kz | `SUM(fVendas[Valor_Total])` |
| Ticket Médio | 490K Kz | `DIVIDE([Total Vendas], COUNTROWS(fVendas))` |
| Crescimento YoY | +18.5% | Cálculo YoY com CALCULATE |
| Margem Média | 32.4% | `AVERAGEX(fVendas, fVendas[Margem_Pct])` |
| Transações | 500 | `COUNTROWS(fVendas)` |

---

## 💡 Insights Obtidos

- 🏆 **Maria João** lidera as vendas com 51M Kz (28 transações)
- 📍 **Luanda** representa 58% da receita total
- 📦 **Laptops** são o produto mais rentável (50M Kz)
- 📈 Crescimento de **+18.5% YoY** nas vendas totais
- 🌍 **Benguela** é a região com maior crescimento (+32%)

---

## 🏷️ Tecnologias

`Power BI Desktop` `DAX` `Power Query` `Star Schema` `KPIs` `Gráfico de Linhas` `Gráfico de Rosca` `Gráfico de Barras` `Slicers` `CALCULATE` `DIVIDE` `RANKX`

---

## 👤 Autor

**Josemar Manuel** · [LinkedIn](https://linkedin.com/in/josemarmanuel) · josemardiferencial@gmail.com · Luanda, Angola
