# Projeto 2 — Deep Learning para Detecção de Phishing por URL

**Instituição:** Universidade de Brasília (UnB) — Engenharia de Produção  
**Autora:** Ana Beatriz Maciel de Almeida  
**Dataset:** PhiUSIIL Phishing URL Dataset, UCI Machine Learning Repository (ID 967)  
**Entregáveis:** notebook Python, short paper de 6 páginas e apresentação de 20 slides (15–20 minutos).

## 1. Objetivo

Construir e avaliar um MVP de detecção de *phishing* por meio de uma rede neural profunda (DNN), classificando URLs como **legítimas (0)** ou **phishing (1)**. O foco são **18 atributos lexicais e estruturais de URL**, que não dependem de renderizar a página para a predição. Este trabalho é distinto do MVP anterior com UNSW-NB15, que tratava de detecção de intrusões no tráfego de redes.

## 2. Metodologia

- **Fonte:** PhiUSIIL (235.795 registros); importação pelo pacote `ucimlrepo` (`fetch_ucirepo(id=967)`).
- **Seleção de variáveis:** 18 atributos de composição, comprimento, domínio, obfuscação e caracteres especiais da URL.
- **Variável-alvo:** o rótulo da base foi invertido no notebook para que **1 = phishing** e **0 = legítima**.
- **Divisão de dados:** 70% para treinamento, 15% para validação e 15% para teste, de forma estratificada (`random_state=42`).
- **Pré-processamento:** imputação pela mediana e padronização numérica, ajustadas nos dados de treinamento.
- **Arquitetura:** `18 entradas → Dense(128) → Dense(64) → Dense(32) → Sigmoid`, com ReLU, Batch Normalization, Dropout e pesos de classe.
- **Treinamento:** Adam, binary cross-entropy, Early Stopping e ReduceLROnPlateau.
- **Decisão:** limiar selecionado usando o F1-score **somente na validação**; teste preservado até a avaliação final.

## 3. Resultados finais observados

Resultados de uma execução no conjunto de teste de **35.370 URLs**:

| Métrica | Resultado |
|---|---:|
| Acurácia | 99,83% |
| Precisão (phishing) | 99,98% |
| Recall (phishing) | 99,63% |
| F1-score | 99,80% |
| ROC-AUC | 99,90% |
| Average Precision (PR) | 99,92% |
| Limiar selecionado | 0,24 |

| Matriz de confusão | Predita legítima | Predita phishing |
|---|---:|---:|
| **Real legítima** | 20.225 (TN) | 3 (FP) |
| **Real phishing** | 56 (FN) | 15.086 (TP) |

**Interpretação:** o modelo identificou 15.086 dos 15.142 casos de phishing no teste, deixando passar 56, e gerou apenas 3 falsos alertas entre 20.228 URLs legítimas. Esse desempenho é **do benchmark com divisão aleatória da própria base**, e não uma comprovação de eficácia em URLs atuais ou campanhas futuras. A validação externa, temporal e por domínio é uma etapa necessária antes de qualquer aplicação real.

## 4. Arquivos finais

- `TRAB_DL_Phishing_PhiUSIIL_FINAL_Colab_VSCode.ipynb`: notebook para **Google Colab** ou **VS Code/Jupyter**.
- `ShortPaper_Phishing_PhiUSIIL_FINAL_Overleaf.zip`: projeto independente do **short paper**, contendo `main.tex`, `resultados.tex`, `images/` e `dados/`.
- `Apresentacao_Phishing_PhiUSIIL_FINAL_Overleaf.zip`: projeto independente dos **slides Beamer**, com a mesma estrutura.
- `ShortPaper_Phishing_PhiUSIIL_FINAL.pdf`: short paper compilado, com **6 páginas**.
- `Apresentacao_Phishing_PhiUSIIL_FINAL.pdf`: apresentação compilada, com **20 slides**.
- `artefatos_phiusiil.zip`: gráficos, métricas e demais saídas da execução do notebook.

## 5. Como executar o notebook

### Google Colab

1. Abra o Google Colab e faça upload do arquivo `.ipynb`.
2. Execute as células **em ordem** (menu **Ambiente de execução → Executar tudo**).
3. O notebook instala automaticamente pacotes ausentes e baixa a base da UCI (é necessário acesso à internet).
4. No final, obtenha o arquivo `artefatos_phiusiil.zip`, gerado no diretório de trabalho da sessão.

### VS Code / Jupyter local

1. Instale Python compatível com TensorFlow, por exemplo **Python 3.12**.
2. Crie e ative um ambiente virtual na pasta do projeto. No PowerShell do Windows:

   ```powershell
   py -3.12 -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install --upgrade pip
   ```

3. Abra o notebook no VS Code, selecione como *kernel* o Python do ambiente `.venv` e execute as células em ordem. Os pacotes ausentes são instalados pela primeira célula; pode ser preciso reiniciar o kernel após a instalação.
4. As saídas serão gravadas em `artefatos_phiusiil/`, na pasta corrente do notebook.

**Observação:** uma nova execução pode produzir valores ligeiramente diferentes por causa do ambiente, das versões de pacotes e das operações de treinamento de redes neurais. Mantenha separados os resultados já documentados da execução de referência e os valores de eventuais novas execuções.

## 6. Como abrir os documentos no Overleaf

1. No Overleaf, clique em **New Project → Upload Project**.
2. Envie **cada ZIP separadamente**, criando dois projetos: um para o paper e outro para a apresentação.
3. Use `main.tex` como arquivo principal e compile com **pdfLaTeX**.
4. Os ZIPs finais **já contêm os valores reais e as imagens**. Não é preciso executar o notebook apenas para visualizar os documentos.
5. Se executar um **novo** experimento, substitua os dados correspondentes em `resultados.tex` e as figuras de `images/` pelos arquivos dessa nova execução, antes de recompilar.

## 7. Limitações e próximos passos

A avaliação atual usa partição aleatória de uma única base e não simula necessariamente ameaças novas. Além disso, a aplicação não foi testada em um navegador, gateway ou ambiente de produção. Próximos passos recomendados: validação temporal, divisão por domínio/campanha para reduzir correlações entre treino e teste, comparação com modelos clássicos, análise de robustez adversarial e medição de latência.

**Fonte dos dados:** [PhiUSIIL Phishing URL Dataset — UCI](https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset)
