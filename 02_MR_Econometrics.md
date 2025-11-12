# Эконометрическое обоснование MR-стратегии (регрессионный анализ)

## 1. Переменные и источники
- $P_t$ — цена; $r_t=\ln(P_t/P_{t-1})$ — лог-доходность (из трейдов/квот).
- $x_t$ — стационарный спред (ошибка коинтеграции Engle–Granger/Johansen) или нормированный ценовой индекс пары.
- $Z_t$ — вектор регрессоров (микроструктура/ликвидность/волатильность/кросс-сигналы).

## 2. Модели MR
### 2.1 AR(1)
$$
x_t = \mu + \phi(x_{t-1}-\mu) + \varepsilon_t,\quad |\phi|<1.
$$
- Оценка: OLS/GLS на rolling-окнах; проверка стационарности ADF/KPSS.
- **Half-life:** $HL=\frac{\ln 2}{-\ln\phi}$; $\kappa \approx -\ln \phi/\Delta t$.

### 2.2 OU (непрерывный аналог)
$$
dx_t = \kappa(\mu-x_t)\,dt + \sigma_t\,dW_t,\quad \kappa>0.
$$
- Оценка: MLE/Квазимаксимум через дискретизацию (Euler).

## 3. Условная волатильность ошибок
$$
\varepsilon_t=\sigma_t z_t,\ z_t \sim iid(0,1),\quad \sigma_t^2=\omega+\alpha\varepsilon_{t-1}^2+\beta\sigma_{t-1}^2.
$$
- EGARCH/GJR — асимметрия; Student-$t_\nu$ — heavy tails.
- Диагностика: ARCH-LM, Ljung–Box по $z_t$ и $z_t^2$.

## 4. Расширенная спецификация с факторами
$$
x_t-\mu_t = \phi_t(x_{t-1}-\mu_{t-1}) + \Gamma^\top Z_{t-1} + \varepsilon_t,
$$
где $Z_{t}$ включает: bid-ask spread, order imbalance, realized vol, funding, OI, lead-lag индикаторы, BTC.D/DXY/VIX proxy.

## 5. Сигнал и управление параметрами
- z-score: $s_t=\frac{x_t-\mu_t}{\hat{\sigma}_{eq,t}}$ (равновесное СКО из долгого окна).
- Порог входа: $|s_t|>\lambda_t$; знак позиции: $\operatorname{sign}(-s_t)$.
- Вес позиции: $w_t=\gamma_t \cdot \mathrm{clip}\!\big(\frac{|s_t|-\lambda_t}{\Lambda_{\max}},0,1\big)\cdot \operatorname{sign}(-s_t)$.

## 6. Оценка и робастность
- OLS/GLS, QMLE; HAC/Newey–West; bootstrap доверительные интервалы.
- Rolling / expanding windows, регуляризация ($\ell_1/\ell_2$) для $\Gamma$ при большом $Z_t$.

## 7. Отчетные метрики
- $\kappa, HL$, стабильность $\phi$; доля успешных возвратов; IR/Sharpe/Sortino; hit ratio; оборот; нагрузка на капитал.
- Качество остатков: автокорр., гетероскедастичность, хвосты ($t_\nu$).

## 8. Источники
Engle (1982); Bollerslev (1986); Tsay; Cont; Velu; Jansen; Bell.
