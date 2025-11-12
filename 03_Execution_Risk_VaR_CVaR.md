# Execution-риск: VaR/CVaR как «сторож» стратегии

## 1. Переменные и определения
- $L_{t:h}$ — PnL-потери на горизонте $h$; распределение оценивается исторически/параметрически.
- $\mathrm{VaR}_\alpha$ — квантиль потерь; $\mathrm{ES}_\alpha$ — ожидаемая потеря условно на превышение VaR.
$$
\mathrm{VaR}_\alpha=\inf\{x: P(L\le x)\ge \alpha\},\quad \mathrm{ES}_\alpha=\mathbb{E}[L\mid L\ge \mathrm{VaR}_\alpha].
$$

## 2. Где брать величины
- Историческая симуляция по $L_{t:h}$; параметрическая оценка (Normal/$t_\nu$/Cornish–Fisher).
- EVT-POT для хвостов: согласование квантилей с $q_\alpha^{\text{EVT}}$.

## 3. Правила в execution
- **Pre-trade check:** $\mathrm{VaR}_\alpha^{\text{new}}(w_t+\Delta w)\le \text{Limit}_\alpha$ → разрешить; иначе отклонить.
- **On-trade monitor:** если $\widehat{\mathrm{ES}}_\alpha^{\text{port}}>\text{Limit}_\alpha$ → $\gamma_t \leftarrow \rho\gamma_t$, $0<\rho<1$.
- **Stop escalation:** повторное нарушение → уменьшение $\lambda_t$-толеранса, частичное/полное закрытие.

## 4. Бэктестинг risk-модели
- VaR: Kupiec POF, Christoffersen (independence/conditional coverage).
- ES: Acerbi–Székely.
- Отчет: частота исключений, кластеризация нарушений, поправка лимитов.

## 5. Практические аспекты
- Горизонты: intraday (1–15m) и EOD; учет комиссий, спреда, проскальзывания, latency.
- Интеграция с EVT: при $q_\alpha^{\text{EVT}}$ выше периметра — ужесточение лимитов/пауза.

## 6. Источники
McNeil–Frey–Embrechts; Jorion; Kupiec (1995); Christoffersen (1998); Acerbi–Székely.
