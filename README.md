# 🚗 DriveWell Motors — Análise de Performance Comercial

Dashboard de vendas para uma rede de concessionárias multi-regional. Descobri que o crescimento anunciado de 23,6% era enganoso, e que o problema da rede não era regional: era de uma loja específica.

**🔗 [Ver dashboard ao vivo](https://app.powerbi.com/view?r=eyJrIjoiNjE4ZWU3MTUtMzdlOS00ZmMxLWFlYTMtMjcxNGExNWE4ZGYyIiwidCI6ImU4MmU1OWEwLWY0YTAtNDNmMC1iM2E5LTIwMDZjNjdmMGQ2NiJ9)** · publicado no Power BI Service

---

## 🎥 Preview

| Visão Executiva | Concessionárias |
|:---:|:---:|
| ![Visão Executiva](assets/pagina-1-visao-executiva.png) | ![Concessionárias](assets/pagina-2-concessionarias.png) |

---

## O problema

A DriveWell Motors é uma rede fictícia de concessionárias que opera em sete regiões dos EUA, construída sobre o dataset real *Car Sales Report* (Kaggle). O Gerente Comercial encomendou um painel para responder duas perguntas:

- Onde está o crescimento da receita? (região, marca, tempo)
- Onde a rede sub-performa?

O contrato exigia mais que gráficos. Exigia uma recomendação de negócio, defensável numa apresentação de cinco minutos.

---

## Os achados

**1. O crescimento de 23,6% veio quase todo de quatro meses.**
De janeiro a agosto, 2023 rodou praticamente no mesmo patamar de 2022. Todo o ganho se concentrou entre setembro e dezembro. A rede não cresceu, teve um fim de ano melhor, e ficou *mais* dependente dele.
O plano comercial precisa atacar o vale de jan–ago, não celebrar o número anual.

**2. Quem mais vende não é quem mais fatura por carro.**
Chevrolet, Ford e Dodge lideram a receita por volume, com ticket de ~$26 mil. Cadillac, Saab e Lexus vendem acima de $34 mil, 40% acima da média da rede, e ninguém as gerencia como linha separada. Existe um segmento premium dentro da rede, tratado igual ao resto.

**3. A sub-performance não é regional. É de loja.**
As sete regiões cresceram todas entre 20,2% e 25,5%, uma amplitude de apenas 5,3 pontos. Já entre as 28 concessionárias, a amplitude é de **25 pontos**: de +32,4% a +7,5%.
Cinco lojas ficaram abaixo da meta. Quatro por pouco (17% a 19%). Uma está quebrada: **Buddy Storbeck's cresceu 7,5%, dezesseis pontos abaixo da rede.** A ação não é convocar cinco gerentes. É investigar um.

---

## O que caiu (e por que isso importa)

Testei quatro hipóteses de segmentação. Todas falharam, e documentar isso é parte do trabalho, não uma nota de rodapé.

| Hipótese | Método | Resultado |
|---|---|---|
| Renda prevê o preço do carro | Faixas, Pearson, decis | Correlação 0,0121. Nenhuma. |
| Gênero segmenta o ticket | Dispersão entre categorias | 0,8%, ruído |
| Transmissão segmenta o ticket | Dispersão entre categorias | 1,2%, ruído |
| Cor segmenta o ticket | Dispersão entre categorias | 4,6%, fraco demais |

A hipótese da renda era a espinha de uma **página inteira** do dashboard. Testei com três métodos independentes (faixas de quartil, correlação de Pearson e decis) e o sinal não existe: o ticket médio varia 1,4% entre a faixa mais pobre e a mais rica.

Fui olhar o dado e achei o motivo: **22% dos registros de renda têm o valor idêntico de 13.500**, assinatura clássica de dado sintético gerado sem lógica de negócio.

**Cortei a página.** O dashboard tem duas páginas em vez de três. Uma página bonita provando que não há nada ali não é entrega, é enchimento.

Detalhes em [`docs/06-decisoes-e-aprendizados.md`](docs/06-decisoes-e-aprendizados.md).

---

## Stack e processo

**Excel** → limpeza no Power Query, EDA com tabelas dinâmicas, validação com fórmulas
**Power BI** → modelo estrela, DAX, dashboard de duas páginas
**Design** → sistema visual próprio, planos de fundo em SVG

O Excel não está neste repositório como arquivo. Ele foi a fase de descoberta, e o que importa dela são as decisões, não o `.xlsx`. Estão em [`docs/02-dados-e-profiling.md`](docs/02-dados-e-profiling.md) e [`docs/03-etl-e-modelagem.md`](docs/03-etl-e-modelagem.md).

Todos os números do Power BI foram **validados contra o Excel**. Receita total: `$671.525.465`. Carros: `23.906`. Crescimento 2023: `+23,6%`. Se não batesse, o erro estaria no modelo.

---

## Estrutura do repositório

```
├── README.md
├── docs/
│   ├── 01-contexto.md                  o briefing e a tese
│   ├── 02-dados-e-profiling.md         o dataset e o que estava quebrado nele
│   ├── 03-etl-e-modelagem.md           Power Query, star schema, o bug da dimensão
│   ├── 04-medidas-dax.md               as 8 medidas, uma a uma, com a decisão por trás
│   ├── 05-analise.md                   os achados e como cheguei neles
│   ├── 06-decisoes-e-aprendizados.md   o que caiu, os tropeços, o que eu faria diferente
│   └── 07-dashboard.md                 cada visual e por que ele está lá
├── powerbi/
│   └── Análise de Vendas — DriveWell Motors.pbix
└── assets/
    ├── pagina-1-visao-executiva.png
    ├── pagina-2-concessionarias.png
    └── backgrounds/                    os planos de fundo do dashboard
```

---

## Próximos passos

Não são desculpas. São os limites que eu enxergo do trabalho, e o que faria diferença se ele continuasse.

**Investigar Buddy Storbeck's em campo.** O dashboard aponta o dedo (7,5% contra 23,6% da rede), mas a causa não está nos dados. Faltam headcount, estoque, tempo de operação, mix por loja. Nenhum painel resolve isso, precisa de entrevista.

**Trazer custo e margem.** Toda a análise é de receita. "Cadillac tem ticket de $42 mil" não significa "Cadillac é mais lucrativa". Sem margem, o achado do segmento premium é uma hipótese comercial, não uma conclusão financeira.

**Modelar meta por loja, não por rede.** A meta de +20% é uma régua única para 28 concessionárias de portes diferentes. Uma loja pequena crescer 20% e uma grande crescer 20% não custam o mesmo esforço. Uma meta ponderada por base instalada seria mais justa.

**Quebrar o vale de jan–ago.** Sei que existe. Não sei por quê. É sazonalidade de mercado ou falha comercial da rede? Comparar com dados de mercado responderia, e a resposta muda completamente a ação.

---

## Limitações

**O dataset é sintético em partes.** A coluna de renda tem 22% de valores idênticos e correlação zero com o preço. Não dá para afirmar que a ausência de sinal reflete comportamento real de mercado, provavelmente reflete apenas como o dado foi gerado. Tratei como limitação declarada, não como achado de negócio.

**A meta de +20% é uma premissa minha.** Não existe meta na base. Escolhi 20% porque é exigente (a rede cresceu 23,6%), redondo, e separa bem: cinco lojas ficam abaixo. Uma meta que ninguém erra não é meta. Mas é uma escolha, não um dado.

**"Concessionária" e "região" são dimensões independentes.** Todas as 28 lojas vendem em todas as 7 regiões, não existe hierarquia geográfica real, o que é implausível no mundo físico. É uma característica do dataset, e limita qualquer análise territorial mais profunda.

**Sem custo ou margem.** Toda análise é de receita. "Ticket médio alto" não significa "mais lucrativo".

---

## Autor

**Lucas Souza Fontes Caminha**

[![GitHub](https://img.shields.io/badge/GitHub-Lucasfontez-181717?style=flat-square&logo=github)](https://github.com/Lucasfontez)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-preencher-0A66C2?style=flat-square&logo=linkedin)](#)

---

## Autor

**Autor:** Lucas Fontes · 🔗 [GitHub](https://github.com/Lucasfontez) · [LinkedIn](https://www.linkedin.com/in/lucassfontesc/)
