# Análise de Variância (ANOVA) e Testes Pós-Hoc

Projeto de **análise estatística aplicada a dados de visualização de trailers de filmes**, utilizando Python para investigar se existem diferenças estatisticamente significativas no tempo médio de visualização entre os tipos de trailer **Drama, Comédia e Futurista**.

O projeto aborda a **ANOVA One-Way** e, após identificar diferenças significativas, utiliza os testes pós-hoc **Tukey HSD** e **Bonferroni** para descobrir quais grupos apresentam diferenças entre si.

## 📌 Sobre o projeto

O objetivo principal é analisar o **tempo de visualização por usuário** para diferentes tipos de trailers e verificar se o gênero do trailer está associado a diferenças significativas no engajamento.

A análise segue esta sequência:

```text
Dataset
   ↓
Análise exploratória
   ↓
Estatísticas descritivas
   ↓
ANOVA One-Way
   ↓
Teste F
   ↓
Tukey HSD
   ↓
Bonferroni
   ↓
Comparação e interpretação dos resultados
```

## 🧰 Tecnologias e bibliotecas

O projeto foi desenvolvido em **Python** utilizando:

- **Pandas** — carregamento e manipulação dos dados;
- **SciPy** — testes estatísticos, incluindo ANOVA e testes t;
- **Statsmodels** — ANOVA, Tukey HSD e correção de múltiplas comparações;
- **Matplotlib** — criação de gráficos;
- **Seaborn** — visualização das distribuições dos dados.

## 📂 Estrutura do projeto

Uma estrutura recomendada para o repositório é:

```text
Testes-estatisticos-comparacao-de-medias/
│
├── Testes_estatísticos_comparação_de_médias_completo.ipynb
├── trailers_dados.csv
└── README.md
```

> O notebook utiliza o arquivo `trailers_dados.csv` como fonte dos dados. Se o arquivo for grande demais para o GitHub, recomenda-se utilizar Git LFS ou disponibilizá-lo em um serviço de armazenamento apropriado e documentar o acesso no projeto.

## 📊 Dataset

O dataset contém informações sobre o **tempo de visualização por usuário** para diferentes tipos de trailers.

As principais colunas utilizadas na análise são:

- `trailer_tipo` — tipo do trailer;
- `tempo_visualizacao` — tempo de visualização do trailer pelo usuário.

Os grupos analisados são:

- **Drama**
- **Comedia**
- **Futurista**

O tempo de visualização é convertido para inteiro durante o carregamento:

```python
df['tempo_visualizacao'] = df['tempo_visualizacao'].astype(int)
```

## 🔎 1. Análise exploratória

Inicialmente, os dados são carregados utilizando Pandas:

```python
df = pd.read_csv('/content/trailers_dados.csv')
df['tempo_visualizacao'] = df['tempo_visualizacao'].astype(int)

display(df.head())
```

Em seguida, são criados histogramas com KDE para visualizar a distribuição do tempo de visualização em cada grupo.

Essa etapa permite observar visualmente como o comportamento do tempo de visualização varia entre os diferentes tipos de trailer.

## 📈 2. Análise descritiva

São calculadas estatísticas descritivas para cada grupo, incluindo:

- Média;
- Desvio padrão;
- Tamanho da amostra.

Essas medidas permitem conhecer o comportamento inicial de cada grupo antes da realização dos testes estatísticos.

Os resultados são organizados em um DataFrame contendo:

```text
trailer_tipo
media
desvio_padrao
```

## 🧪 3. ANOVA One-Way

A **ANOVA One-Way** é utilizada para verificar se existe diferença estatisticamente significativa entre as médias de tempo de visualização dos três grupos.

### Hipóteses

**H₀ (hipótese nula):**

As médias de tempo de visualização dos grupos são iguais.

**H₁ (hipótese alternativa):**

Pelo menos uma das médias é diferente.

A ANOVA é executada com:

```python
f_statistic, p_value = stats.f_oneway(
    group_drama,
    group_comedia,
    group_futurista
)
```

Também é utilizado `statsmodels` para construir o modelo:

```python
model = smf.ols(
    'tempo_visualizacao ~ C(trailer_tipo)',
    data=df
).fit()

anova_table = anova.anova_lm(model, typ=2)
```

## 📐 4. Estatística F

A estatística F utilizada na ANOVA é baseada na razão entre a variabilidade entre os grupos e a variabilidade dentro dos grupos:

$$
F = \frac{MS_{between}}{MS_{within}}
$$

Onde:

- `MS_between` representa os Quadrados Médios Entre Grupos;
- `MS_within` representa os Quadrados Médios Dentro dos Grupos.

O notebook também calcula esses valores manualmente a partir da tabela ANOVA.

Na análise realizada:

```text
Soma de Quadrados Entre Grupos: 41921.613333
Soma de Quadrados Dentro dos Grupos: 52603.880000
```

O notebook também apresenta a distribuição F e marca graficamente a estatística F calculada.

## ✅ 5. Resultado da ANOVA

O resultado obtido no notebook apresenta:

```text
p-valor = 1.958658e-19
```

Como o p-valor é muito menor que o nível de significância de `0.05`, a hipótese nula é rejeitada.

### Conclusão

Existe uma **diferença estatisticamente significativa entre as médias de tempo de visualização dos grupos de trailers**.

Entretanto, a ANOVA por si só não informa quais grupos são diferentes. Por isso, são realizados testes pós-hoc.

## 🔬 6. Teste Pós-Hoc de Tukey HSD

Após a ANOVA significativa, o projeto utiliza o **Tukey HSD (Honestly Significant Difference)** para realizar comparações entre todos os pares de grupos.

Os pares analisados são:

```text
Drama vs Comedia
Drama vs Futurista
Comedia vs Futurista
```

O notebook primeiro calcula manualmente a estatística `q`.

A fórmula utilizada é:

$$
q = \frac{\bar{Y}_i - \bar{Y}_j}
{\sqrt{\frac{MS_{within}}{n}}}
$$

Onde:

- `Ŷᵢ` e `Ŷⱼ` representam as médias dos grupos comparados;
- `MS_within` representa a variância dentro dos grupos;
- `n` representa o tamanho da amostra;
- `q` representa a estatística utilizada na comparação.

### Valores calculados manualmente

O notebook apresenta:

| Comparação | Estatística q |
|---|---:|
| Drama vs Comedia | 2.99 |
| Drama vs Futurista | 14.50 |
| Comedia vs Futurista | 11.51 |

O valor crítico utilizado é aproximadamente:

```text
q crítico = 3.358
```

para:

```text
k = 3 grupos
df = 147
alpha = 0.05
```

### Interpretação

Comparando `q` com o valor crítico:

- **Drama vs Comedia:** não apresenta diferença estatisticamente significativa;
- **Drama vs Futurista:** apresenta diferença estatisticamente significativa;
- **Comedia vs Futurista:** apresenta diferença estatisticamente significativa.

## 🐍 7. Tukey HSD com Statsmodels

Além do cálculo manual, o projeto utiliza a função `pairwise_tukeyhsd`:

```python
tukey_result = pairwise_tukeyhsd(
    endog=df['tempo_visualizacao'],
    groups=df['trailer_tipo'],
    alpha=0.05
)

print(tukey_result.summary())
```

Os resultados do cálculo manual são comparados com os resultados obtidos pelo `statsmodels`.

O notebook conclui que os resultados são virtualmente idênticos e que as conclusões de significância são concordantes.

## 📊 8. Comparação dos resultados

A comparação mostra que as estatísticas `q` calculadas manualmente são equivalentes às obtidas a partir da diferença de médias e do erro padrão utilizado pelo teste.

Isso demonstra, dentro do contexto do notebook, como o cálculo estatístico manual se relaciona com a implementação disponível na biblioteca `statsmodels`.

## 🔢 9. Teste Pós-Hoc de Bonferroni

O projeto também utiliza o **Teste de Bonferroni** como alternativa ao Tukey HSD.

O método corrige o nível de significância para múltiplas comparações:

$$
\alpha_{Bonferroni} =
\frac{\alpha_{original}}{m}
$$

Onde:

- `α_original` é o nível de significância original;
- `m` é o número de comparações realizadas.

Para três grupos existem três comparações:

```text
Drama vs Comedia
Drama vs Futurista
Comedia vs Futurista
```

O notebook realiza testes t independentes para cada par e aplica a correção de Bonferroni utilizando:

```python
multipletests(
    uncorrected_p_values,
    method='bonferroni'
)
```

## 📌 10. Resultado do Bonferroni

Os resultados apresentados no notebook são:

| Comparação | p-valor corrigido | Resultado |
|---|---:|---|
| Drama vs Comedia | 0.0896 | Não significativo |
| Drama vs Futurista | 0.0000 | Significativo |
| Comedia vs Futurista | 0.0000 | Significativo |

Assim como no Tukey HSD, o Bonferroni indica diferenças estatisticamente significativas entre:

- Drama e Futurista;
- Comedia e Futurista.

Não foi identificada diferença estatisticamente significativa entre:

- Drama e Comedia.

## 🎯 Principais conclusões

A análise indica que o **tipo de trailer está associado a diferenças significativas no tempo de visualização**.

O principal resultado observado é que os trailers do tipo **Futurista** apresentam diferenças estatisticamente significativas em relação aos trailers:

- Drama;
- Comedia.

Por outro lado, a diferença entre **Drama e Comedia** não foi considerada estatisticamente significativa pelos testes realizados.

Os resultados foram consistentes entre:

- ANOVA One-Way;
- Tukey HSD;
- Bonferroni.

## 🧠 Conceitos estatísticos praticados

Este projeto permite praticar:

- Análise exploratória de dados;
- Média;
- Desvio padrão;
- ANOVA One-Way;
- Hipótese nula e alternativa;
- Estatística F;
- Distribuição F;
- Soma de quadrados;
- Graus de liberdade;
- Quadrados médios;
- Testes pós-hoc;
- Tukey HSD;
- Estatística `q`;
- Distribuição Studentized Range;
- Bonferroni;
- Teste t;
- P-valor;
- Nível de significância;
- Erro Tipo I;
- Comparações múltiplas;
- Interpretação estatística.

## 🚀 Como executar

### 1. Clone o repositório

```bash
git clone https://github.com/SEU-USUARIO/SEU-REPOSITORIO.git
cd SEU-REPOSITORIO
```

### 2. Instale as dependências

```bash
pip install pandas scipy statsmodels matplotlib seaborn
```

### 3. Disponibilize o dataset

Coloque o arquivo:

```text
trailers_dados.csv
```

no local esperado pelo notebook.

O notebook originalmente utiliza:

```python
/content/trailers_dados.csv
```

Esse caminho é compatível com ambientes como Google Colab. Em outro ambiente, ajuste o caminho para o local onde o CSV estiver armazenado.

### 4. Execute o notebook

Abra:

```text
Testes_estatísticos_comparação_de_médias_completo.ipynb
```

O projeto pode ser executado em:

- Google Colab;
- Jupyter Notebook;
- JupyterLab;
- Visual Studio Code com suporte a notebooks.

## 📁 Arquivos do projeto

```text
.
├── Testes_estatísticos_comparação_de_médias_completo.ipynb
├── trailers_dados.csv
└── README.md
```

## 📌 Observação sobre o dataset

Caso `trailers_dados.csv` seja muito grande para ser armazenado diretamente no GitHub, recomenda-se utilizar **Git LFS** ou hospedar o dataset externamente e manter no repositório apenas as instruções para obtê-lo.

## 👨‍💻 Autor

Projeto desenvolvido como estudo prático de **Estatística, Ciência de Dados e análise de diferenças entre médias utilizando Python**.
