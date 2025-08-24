## ✅ Atividade Obrigatória – Desafio 4  
# Consolidação de VR com n8n + IA (resumo executivo)

### 🎯 Objetivo
Unificar planilhas de pessoas (ativos, admissões, desligados, exterior, férias, afastamentos) em **uma base única** e calcular o **VR da competência** definida, gerando um **XLSX final**.

### 🧠 Papel do Agente de IA (Planejamento)
A IA é usada **apenas no início**. Ela recebe **amostras** das planilhas e devolve um **plano estruturado (`plan.json`)** com:
- **Mapeamentos** do que aproveitar de cada planilha.
- **Regras de negócio** (dias e valores por UF, corte de demissão “OK” até dia 15, exclusões e como deduzir a UF a partir do sindicato).

> Se regras mudarem, ajusta-se o **prompt/plan**; o executor continua o mesmo (evita reescrita de código).

### 🛠️ Arquitetura do fluxo (alto nível)

    +-----------------------+               +-----------------------------+
    | Leituras das planilhas| -- amostras-> | Agente IA (Planejamento)    |
    +-----------------------+               |  gera: plan.json            |
                    \                       +-------------+---------------+
                     \                                    |
                      \-- dados completos --> +-----------v-----------+
                                              | Agregador de dados    |
                                              +-----------+-----------+
                                                          |
                                    +---------------------v----------------------+
                                    | Empacotar (plan.json + dados completos)   |
                                    +---------------------+----------------------+
                                                          |
                                    +---------------------v----------------------+
                                    | Executor determinístico                    |
                                    | (join, regras, cálculos)                   |
                                    +---------------------+----------------------+
                                                          |
                                    +---------------------v----------------------+
                                    | XLSX final (VR_Mensal_Final.xlsx)         |
                                    +--------------------------------------------+



### 🧮 O que o executor faz (determinístico)
- Consolida por matrícula.
- Deduz **UF** a partir do texto do sindicato.
- Calcula **Dias** usando **base fixa por UF** (SP/PR=22; RJ/RS=21) **proporcional ao mês**, descontando **férias** informadas.
- Aplica **exclusões** (ex.: diretor/estagiário/aprendiz, exterior, licença-maternidade).
- Regra: **demissão com “OK” até o dia 15 ⇒ Dias = 0**.
- Define **Valor diário** por UF, calcula **VR Total** e **80/20**.
- Marca `__Pendencia__` quando a **UF não foi mapeada** (logo não há valor diário).

### 📦 Saída
- **VR_Mensal_Final.xlsx**: base única por matrícula, com competência, dias, valor diário, VR total, custo empresa, desconto profissional e observações.
- A coluna **`__Pendencia__`** indica linhas que exigem **revisão humana** (principalmente UF não reconhecida).

### ▶️ Como rodar
1. Garanta que as planilhas de entrada estejam ligadas.
2. Execute o workflow: o agente IA gera o **`plan.json`**, o executor aplica as regras e o fluxo grava o **XLSX** final.

---

## 🔗 Link do repositório original
https://github.com/vcdanjos/i2a2
