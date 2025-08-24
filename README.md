# I2A2 – Curso de Agentes Autônomos

Repositório destinado ao curso da **I2A2** sobre agentes autônomos.  
Cada pasta contém atividades diferentes propostas ao longo do curso.

---

## 📁 Organização

- As atividades estão separadas por pastas, conforme sua ordem no curso.
- Cada atividade pode conter fluxos, scripts ou documentação auxiliar.

---

## ✅ Atividade Obrigatória – 18 de Junho

**Framework utilizado:** [n8n](https://n8n.io/)

### 📥 Como importar o projeto

1. No n8n, crie um novo workflow.
2. Importe o arquivo JSON fornecido.
3. Configure as conexões e credenciais necessárias:
   - Chave de API do provedor de IA (para o agente de planejamento).
   - (Se aplicável) conexões externas usadas no fluxo.

---

## ✅ Atividade Obrigatória – Desafio 4  
# Consolidação de VR com n8n + IA (resumo executivo)

### 🎯 Objetivo
Unificar planilhas de pessoas (ativos, admissões, desligados, exterior, férias, afastamentos) em **uma base única** e calcular o **VR da competência** definida, gerando um **XLSX final**.

### 🧠 Papel do Agente de IA (Planejamento)
A IA é usada **apenas no início**. Ela recebe **amostras** das planilhas e devolve um **plano estruturado (`plan.json`)** com:
- **Mapeamentos** do que aproveitar de cada planilha.
- **Regras de negócio** (dias e valores por UF, corte de demissão “OK” até dia 15, exclusões e como deduzir a UF a partir do sindicato).

> Se regras mudarem, ajusta-se o **prompt/plan**; o executor continua o mesmo (evita reescrita de código).

### ⚙️ Arquitetura do fluxo (alto nível)
[Leituras das planilhas]
├─ Amostras ─> [Agente IA – Planejamento] ─> plan.json
└─ Dados completos ─────────────────────────┐
├─> [Empacotar plano + dados] ─> [Executor determinístico] ─> [XLSX] 
└────────────────────────────────────────────────────────────┘


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
