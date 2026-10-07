# Oficina de IA: primeiros passos com o dataset

**Disciplina:** Inteligência Artificial e Machine Learning  
**Professor:** Lucas Hermenegildo do Nascimento  
**Aula:** 06/10/2026 — início do desenvolvimento da P2

## Objetivo

Carregar o dataset do grupo no Google Colab, entender sua estrutura, identificar problemas de qualidade e definir uma pergunta para o projeto. Neste primeiro momento, o código faz análise exploratória: ele ainda não treina um modelo de IA.

Ao final, o grupo deverá conseguir explicar de onde vieram os dados, o que cada registro representa e quais colunas podem ajudar a responder à pergunta escolhida.

## 1. Preparar o ambiente

Abra o Google Colab e crie um notebook. Renomeie-o para `P2_IA_NomeDoGrupo`. Execute os blocos abaixo em células separadas, na ordem apresentada, usando o botão de execução ou `Shift + Enter`.

Uma célula de **código** executa Python. Uma célula de **texto** registra explicações, decisões e conclusões do grupo. Salve o notebook e confira se todos os integrantes conseguem acessá-lo.

## 2. Código inicial completo para arquivos CSV

```python
# Importa a biblioteca de manipulação de tabelas.
import pandas as pd

# Importa o recurso de upload do Google Colab.
from google.colab import files

# Abre a seleção de arquivos no computador.
arquivos = files.upload()

# Nesta atividade, envie exatamente um dataset por vez.
if len(arquivos) != 1:
    raise ValueError("Envie exatamente um arquivo para esta etapa.")

# Recupera o nome do arquivo enviado.
nome = next(iter(arquivos))

# Lê um CSV separado por vírgulas, com codificação UTF-8.
# Ajuste os parâmetros conforme o formato real do seu arquivo.
df = pd.read_csv(nome, sep=",", encoding="utf-8")

# Mostra as cinco primeiras linhas.
display(df.head())

# Informa a quantidade de linhas e colunas.
print("Linhas e colunas:", df.shape)

# Mostra tipos das colunas e contagem de valores não ausentes.
df.info()

# Mostra um resumo estatístico das colunas.
display(df.describe(include="all"))

# Conta valores ausentes por coluna, do maior para o menor.
display(df.isna().sum().sort_values(ascending=False))

# Conta linhas inteiramente iguais a uma linha anterior.
print("Linhas duplicadas:", df.duplicated().sum())
```

## 3. Entendendo cada comando

### `import pandas as pd`

Carrega a biblioteca **pandas**, que permite ler, organizar, filtrar e analisar tabelas. `pd` é um apelido: por isso escrevemos `pd.read_csv()`.

### `from google.colab import files`

Importa o recurso de arquivos do Colab. Este comando depende do ambiente do Google Colab; em um Python local, a leitura pode usar diretamente o caminho do arquivo.

### `arquivos = files.upload()`

Abre o seletor para enviar o dataset. A variável `arquivos` guarda um dicionário cujas chaves são os nomes dos arquivos e cujos valores são os bytes enviados.

O upload disponibiliza o arquivo na sessão de execução. Se a sessão for reiniciada, pode ser necessário enviá-lo novamente. O notebook salvo não garante a preservação do dataset enviado.

### `nome = next(iter(arquivos))`

`iter()` cria um iterador sobre os nomes dos arquivos; `next()` recupera o primeiro nome. A verificação anterior exige um único arquivo para evitar selecionar uma base por engano.

### `df = pd.read_csv(nome, ...)`

Lê o CSV e cria um **DataFrame**, uma tabela com linhas e colunas. `df` é apenas o nome escolhido para essa variável.

- `nome`: arquivo a ser lido.
- `sep=","`: indica que as colunas são separadas por vírgulas.
- `encoding="utf-8"`: indica como os caracteres do texto são interpretados.

Confira o resultado antes de continuar. Se toda a tabela aparecer em uma única coluna, o separador provavelmente está incorreto.

### `display(df.head())`

Exibe as cinco primeiras linhas em formato de tabela. Para ver dez, use `df.head(10)`. Essa amostra ajuda a reconhecer as colunas, mas não representa necessariamente toda a base.

### `df.shape`

Retorna uma tupla `(quantidade_de_linhas, quantidade_de_colunas)`. Por exemplo, `(500, 8)` significa 500 registros e oito colunas. O cabeçalho não conta como registro.

### `df.info()`

Mostra nomes das colunas, quantidade de valores não ausentes e tipos identificados, como `int64` para inteiros, `float64` para números decimais e `object` frequentemente para texto. Uma coluna de números lida como texto pode precisar de conversão; não a converta sem verificar seus valores.

### `df.describe(include="all")`

Produz um resumo. Para números, costuma apresentar quantidade, média, desvio padrão, mínimo, quartis e máximo. Para texto, costuma mostrar quantidade, número de valores distintos, valor mais frequente e frequência desse valor.

Algumas células aparecem como `NaN` porque determinada estatística não se aplica àquele tipo de coluna. Isso não significa, por si só, que a coluna original contém valores ausentes.

### `df.isna().sum().sort_values(ascending=False)`

O comando reúne três operações:

1. `isna()` identifica valores ausentes reconhecidos pelo pandas.
2. `sum()` conta esses valores por coluna.
3. `sort_values(ascending=False)` organiza a contagem do maior para o menor.

Textos como `"não informado"`, espaços ou códigos como `-999` podem representar ausência sem serem reconhecidos automaticamente. O grupo deve investigar as convenções da base.

### `df.duplicated().sum()`

Conta registros inteiramente repetidos, desconsiderando a primeira ocorrência de cada conjunto de linhas iguais. Três linhas idênticas resultam em duas duplicatas.

Registros iguais podem representar eventos legítimos. Antes de removê-los, investigue o significado de cada linha e a existência de um identificador.

## 4. Adaptar a leitura ao formato do dataset

Use **apenas uma** das alternativas abaixo no lugar da linha `pd.read_csv()` do código inicial.

### CSV com ponto e vírgula e decimal com vírgula

```python
df = pd.read_csv(nome, sep=";", decimal=",", encoding="utf-8")
```

### CSV com outra codificação

Se ocorrer `UnicodeDecodeError`, confirme a codificação do arquivo com sua origem. Caso ela seja Latin-1:

```python
df = pd.read_csv(nome, sep=";", decimal=",", encoding="latin-1")
```

Não troque a codificação sem conferir se os acentos foram interpretados corretamente.

### Excel (.xlsx)

```python
# Lê a primeira aba. Para outra, informe o nome em sheet_name.
df = pd.read_excel(nome, sheet_name=0)
```

Se houver erro indicando ausência de `openpyxl`, execute em outra célula `%pip install openpyxl` e repita a leitura.

### JSON com uma lista de registros

```python
df = pd.read_json(nome)
```

Para JSON com um registro por linha, use `pd.read_json(nome, lines=True)`. Estruturas JSON aninhadas podem exigir uma preparação específica; as duas opções não atendem a todos os formatos.

## 5. Investigações adicionais

Execute os blocos após carregar `df`.

### Listar os nomes exatos das colunas

```python
print(df.columns.tolist())
```

Copie esses nomes ao escrever comandos. Letras maiúsculas, acentos e espaços fazem diferença.

### Calcular o percentual de ausência

```python
percentual_ausente = df.isna().mean().mul(100).round(2)
display(percentual_ausente.sort_values(ascending=False))
```

O percentual ajuda a avaliar a gravidade do problema em bases de tamanhos diferentes. Uma coluna com muita ausência não deve ser descartada automaticamente: considere sua importância.

### Examinar os valores de uma categoria

```python
coluna = "SUBSTITUA_PELO_NOME_DA_COLUNA"
display(df[coluna].value_counts(dropna=False))
```

Substitua o texto por um nome real. `dropna=False` inclui valores ausentes na contagem. Procure categorias escritas de formas diferentes, como `Sim`, `SIM` e `sim`.

## 6. Criar dois gráficos e interpretá-los

Escolha colunas relacionadas à pergunta do grupo. Substitua os nomes antes de executar.

```python
import matplotlib.pyplot as plt

coluna_numerica = "SUBSTITUA_POR_UMA_COLUNA_NUMERICA"

df[coluna_numerica].dropna().plot.hist(bins=20)
plt.title(f"Distribuição de {coluna_numerica}")
plt.xlabel(coluna_numerica)
plt.ylabel("Quantidade de registros")
plt.show()
```

O histograma mostra onde os valores se concentram. A coluna deve estar em formato numérico. Valores extremos merecem investigação: podem ser erros ou casos reais.

```python
coluna_categoria = "SUBSTITUA_POR_UMA_COLUNA_CATEGORICA"

df[coluna_categoria].value_counts().head(10).plot.bar()
plt.title(f"Categorias mais frequentes: {coluna_categoria}")
plt.xlabel(coluna_categoria)
plt.ylabel("Quantidade de registros")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
plt.show()
```

O gráfico apresenta até dez categorias mais frequentes e exclui ausências. Em classificação, uma diferença grande entre as quantidades das classes pode indicar desbalanceamento.

**Após cada gráfico, escreva:** o que ele mostra, qual padrão chamou atenção e como isso se relaciona com o problema. Uma associação observada não comprova causa e efeito.

## 7. Registrar as decisões de limpeza

Mantenha uma cópia de trabalho e preserve a base original:

```python
df_trabalho = df.copy()
```

Documente cada mudança. Não execute todas as possibilidades de limpeza automaticamente.

Exemplo de remoção de duplicatas, somente se o grupo confirmar que são registros indevidos:

```python
antes = len(df_trabalho)
df_trabalho = df_trabalho.drop_duplicates()
print("Registros removidos:", antes - len(df_trabalho))
```

Não preencha todos os campos vazios com zero: zero pode ter um significado real e distorcer a análise. Imputação por média, mediana ou categoria mais frequente, quando necessária para o modelo, deve ser ajustada apenas com os dados de treino e aplicada ao teste com os mesmos parâmetros.

## 8. Definir o problema de Machine Learning

Preencha em uma célula de texto:

| Item | Resposta do grupo |
| --- | --- |
| Origem e autorização de uso da base | |
| O que uma linha representa? | |
| Pergunta do projeto | |
| Tipo de tarefa: regressão, classificação ou exploração | |
| Target: resposta que será prevista | |
| Features: informações disponíveis para fazer a previsão | |
| Colunas excluídas e justificativa | |
| Problemas de qualidade encontrados | |
| Limitações da base | |

**Exemplo:** prever o preço de um imóvel usando área, quantidade de quartos e localização. O preço é o target; as outras variáveis são features. Prever um valor numérico é regressão. Prever uma categoria, como aprovado/reprovado, é classificação.

Se não houver target conhecido, registre essa limitação e converse com o professor sobre análise exploratória ou uma tarefa de agrupamento.

## 9. Próxima etapa: preparar o treinamento

Este arquivo cobre a abertura e exploração da base. Para treinar, o grupo precisará escolher um modelo e uma métrica adequados ao problema.

Antes disso:

- Exclua identificadores sem utilidade preditiva e informações que revelem diretamente a resposta.
- Use como features apenas informações disponíveis no momento em que a previsão seria feita.
- Separe treino e teste antes de aprender imputações, escalas ou outras transformações.
- Em dados temporais, respeite a ordem do tempo; em registros repetidos da mesma pessoa ou entidade, evite colocá-la simultaneamente em treino e teste.
- Compare o modelo com uma referência simples, como prever a média no caso de regressão ou a classe mais frequente no caso de classificação.

A separação aleatória comum só será adequada quando a estrutura da base permitir. Um resultado alto pode esconder vazamento de informação.

## 10. Erros comuns

| Situação | O que verificar |
| --- | --- |
| `NameError: df is not defined` | Execute primeiro a célula que carrega o dataset. |
| `FileNotFoundError` | Reenvie o arquivo e confira o nome; a sessão pode ter reiniciado. |
| `UnicodeDecodeError` | Confirme a codificação do CSV. |
| Toda a tabela em uma coluna | Confira o separador do CSV. |
| `KeyError` | Confira o nome exato da coluna com `df.columns.tolist()`. |
| Erro de leitura ou linhas malformadas | Confira delimitadores e aspas no arquivo; não descarte linhas silenciosamente. |
| Gráfico numérico falha | Confira o tipo da coluna e os valores antes de convertê-los. |
| `ModuleNotFoundError: google.colab` | O bloco de upload foi feito para o Colab; em outro ambiente use caminhos locais. |

## 11. Entrega da aula

Entregue o notebook `.ipynb` com os códigos executados e explicações do grupo. Inclua:

1. Identificação dos integrantes, origem da base e pergunta do projeto.
2. Resultados de `head`, `shape`, `info` e `describe` interpretados.
3. Diagnóstico de ausências e duplicatas.
4. Dois gráficos acompanhados de interpretação.
5. Registro das alterações feitas e suas justificativas.
6. Definição de features e target, quando aplicável.
7. Próximo passo e dúvidas para o professor.

**Pergunta final:** “Nossos dados permitem responder à pergunta escolhida? Que evidências sustentam essa conclusão?”

Não envie dados pessoais identificáveis ao notebook compartilhado. Use uma base autorizada e adequada à atividade. A qualidade desta etapa será avaliada pela compreensão e pelas decisões justificadas, não apenas pela execução dos comandos.
