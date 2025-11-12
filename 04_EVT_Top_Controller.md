# EVT как верхнеуровневый управляющий модуль

## 1. Переменные и входные данные
- Остатки MR-модели $\varepsilon_t$ и условное СКО $\sigma_t$ (из GARCH).
- Стандартизованные остатки $z_t=\varepsilon_t/\sigma_t$ — вход в EVT (приближ. iid).

## 2. POT (Peaks-Over-Threshold) и GPD
- Порог $u$ (высокий квантиль $p_u$); превышения $Y_t = X_t - u \mid X_t>u$.
- GPD($\xi,\beta$): 
$$
P(Y\le y)=1-\left(1+\xi\frac{y}{\beta}\right)^{-1/\xi},\quad y>0.
$$
- Оценка $(\xi,\beta)$: MLE, вероятностные графики (Hill/Pickands для $\xi$), профили правдоподобия.
- Диагностика: QQ/P-P, Mean Residual Life (MRL), стабильность по окнам/порогам.
- **Исправление опечатки:** методы — *MLE / Probability Plot / Hill / Pickands* (без иероглифов).

## 3. Квантили и tail-ES
$$
q_\alpha = u + \frac{\beta}{\xi}\Big[\Big(\frac{1-\alpha}{\hat{p}_u}\Big)^{-\xi}-1\Big],\quad 
\mathrm{ES}_\alpha^{\text{tail}} = \frac{q_\alpha}{1-\xi} + \frac{\beta-\xi u}{1-\xi},\ \xi<1.
$$
Где $\hat{p}_u = \frac{\#\{X_t>u\}}{N}$ — эмпирическая частота превышений.

## 4. Режимы риска
- Normal: $\xi_t \le \xi_L$ и $q_{\alpha,t}\le Q_L$.
- High: $\xi_L<\xi_t\le \xi_H$ или $q_{\alpha,t}\in(Q_L,Q_H]$.
- Extreme: $\xi_t>\xi_H$ или $q_{\alpha,t}>Q_H$.

## 5. Подробный алгоритм верхнеуровневого контроллера (EVT→MR)
**Вход:** цены $P_t$, сделки/квоты; параметры окон $W_{\text{MR}}, W_{\text{GARCH}}, W_{\text{EVT}}$; порог $u$ или целевой $p_u$; уровни $Q_L,Q_H,\xi_L,\xi_H$.  
**Выход:** обновлённые параметры $\Theta_t=\{\gamma_t,\lambda_t,\kappa_t,\mu_t\}$; флаг *Pause/Resume* по инструментам.

1. **Подготовка остатков.** Калибруем AR/OU на окне $W_{\text{MR}}$, оцениваем GARCH на $\varepsilon_t$ (окно $W_{\text{GARCH}}$), строим $z_t=\varepsilon_t/\sigma_t$; проводим declustering (peaks separated by $d$).  
2. **Выбор порога.** Подбираем $u$ по MRL-графику и стабильности $(\xi,\beta)$; целимся в $p_u\in[0.90,0.99]$.  
3. **Оценка хвоста.** MLE GPD на $Y_t=X_t-u$; получаем $(\hat{\xi},\hat{\beta})$, считаем $q_{\alpha,t}$ и $\mathrm{ES}_{\alpha,t}^{\text{tail}}$.  
4. **Диагностика качества.** QQ/P-P, информационные критерии, проверка стабильности по сдвигам окна и $u$. При провале — увеличиваем окно или изменяем $u$.  
5. **Классификация режима.** По $(\hat{\xi}, q_{\alpha,t})$ определяем режим: Normal/High/Extreme.  
6. **Планировщик параметров MR.**  
   - Extreme: $\gamma_t\!\leftarrow\!\rho_\gamma\gamma_t$; $\lambda_t\!\leftarrow\!\lambda_t+\Delta_\lambda$; $\kappa_t\!\leftarrow\!\rho_\kappa\kappa_t$; *Pause* для низколиквидных тикеров.  
   - High: умеренное снижение $\gamma_t$, повышение $\lambda_t$; запрет на усреднение против тренда.  
   - Normal: релаксация к базовым параметрам; разрешение на агрессивные входы при валидации execution-риск блоком.  
7. **Согласование с VaR/ES.** Сверяем $q_{\alpha,t}$ с портфельной $\mathrm{VaR}/\mathrm{ES}$. При конфликте (underestimation) — ужесточаем execution-лимиты (понижаем $\text{Limit}_\alpha$).  
8. **Применение и логирование.** Обновляем $\Theta_t$ в стратегия-агентах; логируем $(\hat{\xi},q_{\alpha,t})$, режим, изменения параметров, причины (для аудита).  
9. **Мониторинг и обратная связь.** Отслеживаем частоту нарушений VaR, частоту EVT-пауз, влияние на PnL/Sharpe/MaxDD. Корректируем $Q_L,Q_H,\xi_L,\xi_H$, $u$, окна.  
10. **Гармонизация по корзинам.** Вводим общесекторные триггеры (например, при Extreme на BTC — ужесточение параметров на альтах).

## 6. Теоретические требования
- Приблизительная iid на стандартизированных остатках; корректный declustering.
- Стабильность оценок при смене окна; порог $u$ «достаточно высокий» (bias–variance trade-off).
- Pre-whitening (GARCH) перед EVT уменьшает ложные срабатывания.

## 7. Источники
Embrechts–Klüppelberg–Mikosch (1997); McNeil–Frey–Embrechts (2015); Coles (2001); de Haan & Ferreira (2006); Resnick (2007).  
Pickands (1975); Hill (1975). + Практические обзоры: Jansen; Velu; Hardy; Nehren; Bell; Dobleman.
