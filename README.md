# Exportacoes-Brasil
# 🏭 Data Warehouse & Cockpits – Exportações do Brasil (2000–2023)

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=power%20bi&logoColor=black)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=postgresql&logoColor=white)
![ETL](https://img.shields.io/badge/ETL-Process-FF6F00?style=for-the-badge&logo=apacheairflow&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)

Este repositório contém a modelagem, o processo ETL e os dashboards desenvolvidos para analisar as exportações brasileiras no período de 2000 a 2023. O projeto visa fornecer uma visão consolidada e interativa dos dados de comércio exterior, permitindo análises por produto, estado de origem, país de destino, portos e modais de transporte.

---

## 📊 Visão Geral do Projeto

**Objetivo:**  
Construir um Data Warehouse (DW) em esquema estrela para armazenar os dados históricos de exportações do Brasil e disponibilizar dashboards no Power BI que facilitem a exploração de métricas como valor FOB, quantidades exportadas, rankings e distribuições geográficas.

**Fonte de Dados:**  
Arquivo CSV contendo registros de exportações de 2000 a 2023 (`/data/exportacoes_2000_2023.csv`).

---

## 🧱 Modelagem do Data Warehouse

O DW foi modelado no padrão **estrela**, com uma tabela fato central e seis dimensões.

### 📌 Tabela Fato: `f_exportacao`
| Campo              | Descrição                              |
|--------------------|----------------------------------------|
| `id_exportacao`    | Chave primária (surrogate)             |
| `data_exportacao`  | Data da operação (chave estrangeira para `dim_tempo`) |
| `valor_fob`        | Valor FOB em dólares                    |
| `quantidade`       | Quantidade exportada (em kg/unidade)    |
| `id_produto`       | Chave estrangeira para `dim_produto`    |
| `id_estado_origem` | Chave estrangeira para `dim_estado`     |
| `id_pais_destino`  | Chave estrangeira para `dim_pais`       |
| `id_porto_origem`  | Chave estrangeira para `dim_porto`      |
| `id_transporte`    | Chave estrangeira para `dim_transporte` |

### 📁 Dimensões

| Dimensão           | Atributos                                            |
|--------------------|------------------------------------------------------|
| `dim_tempo`        | `dia`, `mês`, `trimestre`, `ano`                     |
| `dim_produto`      | `código NCM`, `descrição`, `categoria`               |
| `dim_estado`       | `UF`, `nome`, `região`                               |
| `dim_pais`         | `código`, `nome`                                     |
| `dim_porto`        | `código`, `nome`, `estado` (UF)                      |
| `dim_transporte`   | `modal` (rodoviário, ferroviário, marítimo, aéreo, dutoviário) |

---

## 🔄 Fluxo ETL & Transformações

O processo ETL segue as etapas abaixo:

1. **Extração**  
   Leitura do arquivo `exportacoes_2000_2023.csv` para uma área de staging (tabelas temporárias).

2. **Limpeza**  
   - Remoção de registros com valores nulos ou inconsistentes.  
   - Padronização de formatos (datas, códigos, textos).  
   - Correção de codificação de caracteres.

3. **Enriquecimento**  
   - Lookup de descrições de produtos a partir de tabelas de apoio.  
   - Categorização adicional (ex.: setor econômico) baseada em regras de negócio.

4. **Carga (Incremental)**  
   Scripts SQL (`stage_to_dw.sql`) realizam a inserção dos dados nas tabelas dimensionais e fato, garantindo que novos registros sejam adicionados sem duplicação.

5. **Validação**  
   - Contagem de registros e comparação com totais oficiais divulgados pelo governo.  
   - Checksum de valores agregados (soma de FOB por ano) para assegurar consistência.

---

## 📈 Cockpits & Dashboards (Power BI)

Os dashboards foram desenvolvidos para oferecer uma experiência analítica completa. As principais visualizações incluem:

| Painel / Visual              | Descrição                                                                 |
|------------------------------|---------------------------------------------------------------------------|
| **Visão Geral**              | Evolução anual das exportações (valor FOB total) em gráfico de linhas.    |
| **Top 10 Produtos**          | Ranking dos produtos mais exportados (por valor e quantidade).            |
| **Estados Exportadores**     | Gráfico de barras com o total exportado por UF de origem.                 |
| **Portos Principais**        | Mapa interativo e ranking dos 5 portos com maior movimento.               |
| **Destinos das Exportações** | Gráfico de bolhas (ou mapa) mostrando o valor exportado por país.         |
| **Modalidades de Transporte**| Participação percentual de cada modal no total exportado.                 |
| **Mapa Coroplético**         | Distribuição das exportações por região do Brasil (intensidade de cor).   |

Todos os relatórios são interativos, permitindo filtros por ano, produto, estado, país e modal.

---

## 🛠 Tecnologias Utilizadas

- **Banco de Dados**: PostgreSQL (ou outro SGBD relacional) – para o Data Warehouse.
- **ETL**: Scripts SQL e, opcionalmente, Python (pandas) para transformações complexas.
- **Visualização**: Power BI Desktop – criação dos dashboards.
- **Versionamento**: Git e GitHub – controle de código e documentação.

---

## 🚀 Como Reproduzir o Projeto

### Pré-requisitos
- PostgreSQL (ou outro banco compatível com SQL) instalado.
- Power BI Desktop (para abrir o arquivo `.pbix`).
- (Opcional) Python 3.8+ e bibliotecas como `pandas`, se desejar recriar os scripts de ETL.

### Passo a passo

1. **Clone o repositório**  
   ```bash
   git clone https://github.com/seu-usuario/exportacoes-brasil-dw.git
   cd exportacoes-brasil-dw
