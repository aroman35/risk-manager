# Концепция: адаптивное управление риском MR-стратегий с использованием EVT и VaR/CVaR

## Краткое описание
Иерархическая система риск-менеджмента для mean-reversion (MR) стратегий на крипто-деривативах. 
**EVT** (Extreme Value Theory) — верхнеуровневый надзор tail-risk и переключатель режимов рынка; 
**VaR/CVaR** — локальные лимиты в execution-контуре (контроль позиции и плеча).

## Обозначения и переменные
- $P_t$ — цена инструмента в момент $t$; $r_t = \ln(P_t/P_{t-1})$ — лог-доходность.
- $x_t$ — стационарный спред (отклонение) для MR (например, ошибка коинтеграции).
- $\mu$ — долгосрочный уровень $x_t$; $\kappa$ — скорость возврата к среднему (OU); $\phi$ — AR(1)-коэффициент.
- $\varepsilon_t$ — инновации (ошибки модели); $\sigma_t$ — условное СКО (GARCH).
- $\lambda_t$ — порог входа (|z-score|); $\gamma_t$ — коэффициент размера позиции/плеча.
- $L_t$ — убытки портфеля за горизонт $h$; $\mathrm{VaR}_\alpha$, $\mathrm{ES}_\alpha$ — риск-метрики уровня доверия $\alpha$.
- EVT (POT): $u$ — порог; $(\xi,\beta)$ — shape/scale GPD; $q_{\alpha}$ — хвостовой квантиль; $\mathrm{ES}_\alpha^{\text{tail}}$ — tail-ES.

### Откуда берутся переменные
- $P_t$ и биржевые поля — из исторических данных (Tardis/биржи). 
- $r_t$ — из $P_t$; $x_t$ — из коинтеграционной регрессии/спред-конструкции.
- $\mu,\phi$ — из AR(1); $\kappa$ — из связи $\phi \approx e^{-\kappa \Delta t}$; $\sigma_t$ — из GARCH/EGARCH.
- $(\xi,\beta), q_\alpha$ — из POT-GPD на стандартизированных остатках $z_t=\varepsilon_t/\sigma_t$.
- $\lambda_t,\gamma_t$ — настраиваются контуром управления (EVT/Execution).

## Исследовательские гипотезы
- **H1:** доходности криптоактивов — heavy-tailed и асимметричны → «гауссовые» предпосылки занижают tail-risk.
- **H2:** эффективность MR зависит от режима волатильности; при экстремальных режимах нужен динамический контроль.
- **H3:** EVT-квантили и tail-ES дают более реалистичный контроль экстремальных рисков по сравнению с нормальным VaR.
- **H4:** Иерархия «EVT (режим) → MR-параметры → Execution VaR/ES» повышает устойчивость портфеля стратегий.

## Ключевые источники
- Engle (1982), Bollerslev (1986) — ARCH/GARCH.  
- Tsay, R. S. *Analysis of Financial Time Series* (Wiley).  
- Cont, R. (2001) *Empirical Properties of Asset Returns*.  
- McNeil, Frey, Embrechts (2015) *Quantitative Risk Management* (Princeton).  
- Coles (2001) *EVT*. Embrechts–Klüppelberg–Mikosch (1997). Resnick (2007).  
- **Дополнительно:** Stefan Jansen, Williams Dobleman, Raja Velu, Maxence Hardy, Daniel Nehren, Steve Bell.
