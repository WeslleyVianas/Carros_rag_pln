[readme_md.md]
# Sistema de Perguntas e Respostas sobre Marcas de Automóveis (PLN + RAG)

Projeto de Processamento de Linguagem Natural (PLN) voltado à recuperação de informação e geração de respostas fundamentadas sobre 18 marcas automotivas a partir de artigos da Wikipédia em português.

## Estrutura do Repositório

```text
├── notebook.ipynb              # Notebook executável completo (Etapas 1 a 6)
├── requirements.txt            # Dependências Python do projeto
├── README.md                   # Visão geral e instruções de execução
├── relatorio_tecnico.pdf       # Relatório técnico completo
├── data/
│   ├── README.md               # Documentação do corpus, licença e reprodutibilidade
│   ├── marcas.csv              # Metadados das 18 marcas e categorias
│   ├── gold_perguntas.csv      # Gold set de perguntas (dev e teste)
│   └── raw/                    # Cache dos artigos coletados via API do MediaWiki
└── resultados/
    └── resumo_resultados.md    # Síntese das métricas e tabelas geradas
```

## Instalação e Execução

1. **Instale as dependências:**
   ```bash
   pip install -r requirements.txt
   python -m spacy download pt_core_news_sm
   ```

2. **Execute o notebook:**
   Abra o arquivo `.ipynb` no Jupyter Notebook, VS Code ou Google Colab e execute todas as células sequencialmente (`Run All`).

## Metodologia Resumida

1. **Coleta e Corpus**: Extração via API do MediaWiki (CC BY-SA 4.0) cobrindo 18 marcas em 5 categorias.
2. **Pré-processamento**: Limpeza com regex (preservando números e entidades), segmentação em *chunks* ($\sim 120$ palavras), tokenização/lematização com `spaCy` (`pt_core_news_sm`), tratamento de *stopwords* e representações BoW, TF-IDF e bigramas ajustadas estritamente nos *chunks*.
3. **Recuperação Principal**: Busca textual por similaridade de cosseno comparando quatro configurações lexicais contra uma linha de base aleatória.
4. **Avaliação**: Métricas Hit@1, Hit@3, Hit@5, MRR, acurácia de marca no top-1, intervalos de confiança de 95% via *bootstrap*, matriz de confusão, análise de erros e calibração de limiar de abstenção ($\tau = 0{,}221$).
5. **Visualizações**: 7 gráficos cobrindo distribuição de classes, tamanho dos *chunks*, projeção PCA 2D, similaridade entre marcas, comparação de estratégias, matriz de confusão e distribuição de scores/limiar.
6. **Componente Contemporâneo (RAG)**: Geração condicionada ao contexto recuperado utilizando `google/flan-t5-small` apenas quando a similaridade supera o limiar calibrado, avaliando impacto do formato de *prompt*, uso de contexto e distinção entre falhas de recuperação e de geração.


[data_readme_md.md]
# Conjunto de Dados — Sistema de Perguntas e Respostas sobre Marcas Automotivas

Este diretório reúne os arquivos de entrada, cache e avaliação utilizados no projeto de Processamento de Linguagem Natural (PLN).

## 1. Estrutura dos Arquivos

* **`marcas.csv`**: Tabela de metadados contendo as 18 marcas de automóveis selecionadas para o corpus, organizadas em 5 categorias:
  * *Populares (10)*: Chevrolet, Fiat, Ford, Honda, Hyundai, Nissan, Peugeot, Renault, Toyota, Volkswagen.
  * *Luxo (3)*: Audi, BMW, Mercedes-Benz.
  * *Esportivas (3)*: Ferrari, Lamborghini, Porsche.
  * *Elétrica (1)*: Tesla.
  * *Histórica (1)*: Gurgel.
* **`raw/`**: Diretório de cache local contendo os artigos originais extraídos em português via API oficial do MediaWiki (Wikipédia). O uso de cache garante reprodutibilidade e evita requisições repetidas à API.
* **`gold_perguntas.csv`**: Conjunto de avaliação (*gold set*) autoral composto originalmente por 44 perguntas (das quais 41 permanecem válidas após verificação automática de alinhamento com os textos do corpus), dividido em conjuntos de desenvolvimento (*dev*) e *teste*, incluindo perguntas respondíveis (explícitas e implícitas) e perguntas sem resposta no corpus (para calibração de abstenção).

## 2. Fonte, Coleta e Licença

* **Fonte primária**: Artigos da Wikipédia em língua portuguesa (`pt.wikipedia.org`), coletados via API do MediaWiki (`w/api.php`).
* **Licença do conteúdo textual**: Creative Commons Atribuição-CompartilhaIgual 4.0 Internacional (**CC BY-SA 4.0**), conforme os termos de uso da Wikimedia Foundation.
* **Reprodução da coleta**: Caso a pasta `data/raw/` não esteja presente localmente, o notebook principal realiza automaticamente a consulta à API do MediaWiki usando os títulos definidos em `data/marcas.csv` e salva os arquivos em cache.

## 3. Limitações e Viés do Corpus

* **Fonte única**: Todo o conhecimento textual provém exclusivamente da Wikipédia em português.
* **Cobertura desigual e desbalanceamento**: Artigos de marcas mais populares ou históricas possuem extensões distintas, além do desbalanceamento intencional entre as categorias (10 populares vs. 1 elétrica/histórica).
* **Mutabilidade**: Por ser uma enciclopédia colaborativa, os artigos online podem sofrer alterações ao longo do tempo; por isso, recomenda-se utilizar o cache versionado em `data/raw/` para reproduzir exatamente os números do relatório técnico.
* **Citações cruzadas**: Alguns artigos mencionam marcas concorrentes ou parceiras (ex.: alianças industriais), o que pode gerar sobreposição lexical durante a recuperação.


[requirements_txt.txt]
numpy>=1.24.0
pandas>=2.0.0
scikit-learn>=1.3.0
spacy>=3.7.0
nltk>=3.8.1
requests>=2.31.0
matplotlib>=3.7.0
seaborn>=0.12.2
transformers>=4.36.0
torch>=2.1.0
sentencepiece>=0.1.99
accelerate>=0.25.0
