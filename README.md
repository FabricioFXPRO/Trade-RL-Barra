# Testes de Aprendizado por Reforço para Trading Algorítimo

# Relatório do Projeto
https://raspy-cheek-f77.notion.site/1257dc6c82b58007a423ee423dadb8bd?v=1257dc6c82b581d08cc7000cf3c71662

## 1. Introdução

### 1.1 Início do Projeto e Objetivo

O projeto teve início em **20 de outubro de 2024**, com o objetivo de **estudar e desenvolver uma estratégia de trading com aprendizado por reforço em barras de 1 minuto no Nasdaq NQ**. O foco é criar um agente que aprenda a operar com base em **gatilhos de trade na janela operacional dos primeiros 30 minutos da abertura**. Devido ao tempo de treinamento, os testes foram expandidos para **M5, M15 e M30**, e a janela operacional ajustada para **9:40 às 17:00h (verão) e 10:40 às 18:00 (inverno) EUA**.

### 1.2 Estratégia de Desenvolvimento

- Implementação de um ambiente de aprendizado por reforço.
- Definição de regras para execução de trades com base em gatilhos.
- Ajustes progressivos de hiperparâmetros.
- Testes em diferentes timeframes e análise de desempenho.

Pasta do projeto: **D:\Aprendizado_por_Reforco\TradeM1**

## 2. Detalhes do Ambiente

- **Biblioteca Python:** 3.9.13
- Keras
    
- **Dados Utilizados:** M1 com colunas **Open, High, Low, Close, médias móveis, indicadores técnicos (RSI, MACD, Estocástico, Volume), e variáveis personalizadas (PavioSuperior, PavioInferior, Corpo, Delta, etc.)**.
- **Janela Operacional:** O "Gatilho" determina se o agente pode operar. Nos primeiros 30 minutos, o valor é **1** (operações ativas). Fora desse período, o valor é **0**, encerrando qualquer posição aberta.

Colunas detalhadas:

```python
DateTime,Open,High,Low,Close,Volume,PavioSuperior,PavioInferior,Corpo,Range,D_Open,D_High,D_Low,D_Close,D_PavSup,D_PavInf,D_Corpo,
SMA4,SMA8,SMA12,SMA20,SMA50,SMA100,SMA200,StochasticoK,StochasticoD,RSI,MACD,MACDSignal,MACDHistogram

```

## 3. Agente de Aprendizado por Reforço

- **Ações:**
    - Com "Gatilho" = **1**, o agente decide entre **comprar, vender ou manter**.
    - Se já estiver em operação, decide entre **continuar ou sair**.
    - Com "Gatilho" = **0**, qualquer posição aberta é encerrada automaticamente.

### 3.1 Sistema de Recompensas

- **Recompensa Base:** Calculada pelo ganho real da operação.
- **Recompensa Extra por Alta Assertividade:** Bônus progressivo para sequências de operações lucrativas.
- **Penalidade Progressiva por Sequência de Erros:** Penaliza múltiplas perdas consecutivas.
- **Fator de Balanceamento:** Ajusta a relação entre recompensas e penalizações para manter o aprendizado eficiente.
- **Limitação Dinâmica:** O agente pode abrir uma nova operação se a anterior foi lucrativa.

### 3.2 Alternativas de Recompensa Testadas

### 1. **Recompensa do Agente (Usada no Treinamento)**

- Baseada em assertividade (número de acertos recentes).
- **Recompensas Extras para Alta Assertividade:** Bônus por 3 acertos consecutivos.
- **Penalização Progressiva:** Penalidades escalonadas para sequências de perdas.
- **Fator de Balanceamento:** Ajustado dinamicamente para evitar aprendizado tendencioso.

### 2. **Recompensa Real (Monitoramento do Desempenho)**

- Calculada com base no **ganho real** em pontos.
- **Monitoramento e Avaliação:** Serve como métrica de referência sem influenciar diretamente o aprendizado.

## 4. Regras para Operações

- **Com "Gatilho" = 1:** O agente pode operar.
- **Com "Gatilho" = 0:** O agente deve encerrar todas as posições.

## 5. Análise de Resultados

- Melhor desempenho do agente após ajustes na penalização progressiva.
- Ajustes em hiperparâmetros **batch_size (32-512), gamma (0.99 a 0.48), learning_rate** melhoraram a estabilidade.
- Timeframes **M15 e M30** mostraram melhor consistência nos padrões.
- Implementação de **Rainbow DQN** ajudou a reduzir a variabilidade das decisões.

## 6. Conclusão e Próximos Passos

📌 **Conclusão**: O modelo evoluiu significativamente com ajustes progressivos e penalização de erros. A transição para **PyTorch + Stable Baselines3** proporcionou maior controle e eficiência.

🔹 **Próximos Passos:**

1. Refinamento baseado nos melhores hiperparâmetros.
2. Testes com **stop loss dinâmico**.
3. Avaliação e validação do modelo em cenários simulados e reais.

🚀 O projeto segue para otimização e testes, visando uma abordagem mais robusta para decisões automatizadas no trading.
