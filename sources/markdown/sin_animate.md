$$\begin{tikzpicture}
\def\t{0}
\def\r{3.1415}
\begin{axis}[width=12cm,height=7cm,
	ticks=none,
	xmin=-0.5, xmax=3.8,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true,mark size=1
	]
\addplot[smooth,blue,domain=\t:\r,samples=80] {1-cos(deg(x*3))};
\addplot[mark=*] coordinates {(\t,0)};
\addplot[mark=*] coordinates {(\r,2)};
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 0. КДПВ_</font>

Анимация в интерфейсе делает наглядным изменение его состояния. Например, при неудачной отправке длинная форма прокручивается к неправильно заполненному полю. Или увеличивающаяся по нажатию фотография раздвигает окружающие элементы.

Без анимации сложнее воспринимать резкие и внезапные изменения. Вместе с тем анимация должна быть короткой и ненавязчивой, чтобы не мешать пользователю.

Анимация кажется естественной, когда повторяет привычное движение предметов окружающего мира. Под катом я расскажу, как делал анимацию на основе физических законов. Смотрите [готовый результат на демо-странице](https://parpalak.github.io/demo/sin-animate.html) (там один блок следует за другим при движении мыши).
<habracut>
## Вспоминаем физику

Перемещение объектов описывается изменением координат _x_ с течением времени _t_. Если вы попытаетесь подобрать функцию _x_(_t_) "на глазок", вы потратите много времени, добиваясь плавного и естественного движения. Что выбрать? Гиперболу? Параболу? Куда ее переместить? Как повернуть?

За примерами движения лучше всего обратиться к предметам окружающего мира. Математический закон их движения диктуется физикой. Толкнем брусок, лежащий на столе. Он проходит определенное расстояние, замедляясь под действием силы трения. В хорошем приближении сила сухого трения скольжения постоянна, и зависимость _x_(_t_) оказывается параболой. Такое замедление можно использовать, если в начальный момент объект анимации уже двигался.

$$\begin{tikzpicture}
\def\t{0}
\def\r{3.4}
\begin{axis}[width=10cm,height=7cm,
	ticks=none,
	xmin=-0, xmax=3.8,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true,mark size=1
	]
\addplot[smooth,blue,domain=\t:\r,samples=80]{-(x-\r)^2} node[pos=0.75,black,anchor=south east,inner sep=2pt]{$x=A+Bt+Ct^2$};
\addplot[dashed,domain=\r-0.7:\r,samples=2]{0};
\addplot[mark=*] coordinates {(\t,-\r*\r)};
\addplot[mark=*,green!50!black] coordinates {(\r,0)} node[pin=-90:{\scriptsize{\text{плавная остановка :)}}}]{};
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 1. Торможение сухим трением по параболе_</font>

Сила вязкого трения пропорциональна скорости движения тела. В таком случае тело будет двигаться к точке остановки по экспоненте за бесконечно большое время. Если экспоненту исказить, чтобы ограничить время движения, такая анимация будет казаться неестественной. Из-за трудностей с остановкой в разумное время не следует использовать модель вязкого трения, только если симуляция самого вязкого трения не является целью.

$$\begin{tikzpicture}
\def\t{0}
\def\r{3.8}
\begin{axis}[width=10cm,height=7cm,
	ticks=none,
	xmin=-0, xmax=3.8,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true,mark size=1
	]
\addplot[smooth,blue,domain=\t:\r,samples=80] {1-exp(-x*1.0)} node[pos=0.45,black,anchor=south east,inner sep=2pt]{$x=A-Be^{-\alpha t}$};
\addplot[dashed,domain=\t:\r,samples=2]{1};
\addplot[mark=*] coordinates {(\t,0)};
\addplot[red!80!black] coordinates {(3.4,1)} node[pin=-90:{\scriptsize{\text{не останавливается :(}}}]{} ;
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 2. Торможение по экспоненте в вязкой среде_</font>

Отклоненный от положения равновесия маятник (или грузик на пружине) плавно набирает скорость, проходит положение равновесия и плавно тормозит. Затем движение повторяется в обратную сторону, и так до бесконечности (если трения нет). График такого движения --- синусоида. Периодический повтор нам не особо интересен, а вот движение маятника между крайними точками получается плавным и естественным.

$$\begin{tikzpicture}
\def\t{0}
\def\r{3.1415}
\begin{axis}[width=10cm,height=7cm,
	ticks=none,mark size=1,
	xmin=-0.5, xmax=3.6,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true
	]
\addplot[smooth,blue,domain=\t:\r,samples=80] {1-cos(deg(x))} node[pos=0.52,black,anchor=south east,inner sep=2pt]{$x=A-B\cos\omega t$};
\addplot[dashed,domain=\t:\t+0.6,samples=2] {1-cos(deg(\t))};
\addplot[dashed,domain=\r-0.6:\r,samples=2] {1-cos(deg(\r))};
\addplot[mark=*,green!50!black] coordinates {(\t,0)} node[pin=90:{\scriptsize{\text{плавный запуск :)}}}]{};
\addplot[mark=*,green!50!black] coordinates {(\r,2)} node[pin=-90:{\scriptsize{\text{\quad плавная остановка :)}}}]{};
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 3. Движение маятника по синусоиде между крайними точками_</font>

В JS-библиотеках и CSS есть заготовки [easing-функций](http://easings.net/ru) для создания специальных эффектов. Почти все заготовки следует использовать в специальных случаях, с осторожностью. Только синусоида более-менее универсальна.

Во-первых, синусоидальная траектория переводит тело из одного покоящегося положения в другое. Во-вторых, продолжительность такого движения равна половине периода. Движение ограничено во времени. Продолжительность не зависит от внешних обстоятельств и начальных условий. Она зависит только от свойств самой системы и определяется соотношением жесткости и инерционности.

Обычно я выбираю длительность анимации по синусоиде в 200 миллисекунд. Такая длительность в несколько раз больше времени реакции человека. Анимация хорошо заметна, но не успевает раздражать.

Давайте научимся проводить синусоидальную траекторию по начальным условиям, времени движения и точке остановки.

## Как провести синусоиду через две точки

Пусть тело покоится в начальный и конечный момент времени. Тогда касательные к графику в точках _t_~1~ и _t_~2~ горизонтальны, а сам график --- это полупериод синусоиды.

$$\begin{tikzpicture}
\def\t{0}
\def\r{3.1415}
\begin{axis}[
	ticks=none,
	xmin=-1, xmax=4.5,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true
	]
	\addplot[smooth,blue,domain=\t:\r,samples=80] 
		{1-cos(deg(x))};
	\addplot[dashed,domain=\t:\t+1,samples=2] 
		{1-cos(deg(\t))};
	\addplot[dashed,domain=\r-1:\r,samples=2] 
		{1-cos(deg(\r))};
\addplot[mark=*,mark size=1] coordinates {(\t,0)} node[pin=95:{$(t_1,x_1)$}]{} ;
\addplot[mark=*,mark size=1] coordinates {(\r,2)} node[pin=-85:{$(t_2,x_2)$}]{} ;
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 4. График движения между двумя положениями покоя_</font>

Уравнение, описывающее полупериод синусоиды, легко подобрать:

$$x(t)=x_1+{x_2-x_1\over 2}\left[1 - \cos\left(\pi{t-t_1\over t_2-t_1}\right)\right].$$

После окончания одной анимации мы можем начинать другую опять по этой формуле. Но что делать, если новая анимация должна начаться, пока еще не закончилась старая? Чтобы обеспечить плавность движения, мы останавливаем текущую анимацию (синяя линия) и начинаем новую анимацию (красная линия) с ненулевой начальной скоростью:

$$\begin{tikzpicture}
\def\tgnt{0.7}
\def\t{0}
\def\r{3.1415}
\def\tb{1}
\def\rb{\r+\tb}
\def\dx{1.27}
\begin{axis}[
	ticks=none,
	xmin=-1, xmax=4.9,
	axis y line=left,axis x line=bottom,
	xlabel=$t$,ylabel=$x$, 
every axis x label/.style={at={(current axis.south east)},anchor=south},
every axis y label/.style={at={(current axis.north west)},anchor=west},
enlargelimits=true,mark size=1
	]
	\addplot[smooth,blue,domain=\t:\tb,samples=80] 
		{-cos(deg(x))+1};
	\addplot[smooth,dotted,blue,domain=\tb:\r,samples=10] 
		{-cos(deg(x))+1};
	\addplot[blue,dashed,
		 domain=\t:\t+\tgnt,samples=2] 
		{-cos(deg(\t))+1};
	\addplot[smooth,thick,red,domain=\tb:\rb,samples=80] 
		{-1.5*cos(deg(1+0.69*(x-\tb)))+\dx};
	\addplot[dashed,red,domain=\tb-\tgnt:\tb+\tgnt,samples=2] 
		{-1.5*cos(deg(\tb))+\dx+sin(deg(\tb))*(x-\tb)};
	\addplot[dashed,red,domain=\rb-\tgnt:\rb,samples=2] 
		{1.5+\dx};
\addplot[mark=*] coordinates {(\t,0)};
\addplot[mark=*] coordinates {(\tb,1-cos(deg(\tb)))} node[pin=-85:{$(t_1,x_1)$}]{} ;
\addplot[mark=*] coordinates {(\r,2)};
\addplot[mark=*] coordinates {(\rb,1.5+\dx)} node[pin=-85:{$(t_2,x_2)$}]{} ;
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 5. График движения с ненулевой начальной скоростью_</font>

Без математических вычислений не получится написать формулу, соответствующую красной линии. Давайте проделаем эти вычисления.

Семейство всех возможных синусоид описывается уравнением

$$f(t)=A\cos\omega (t-t_2)+B\sin\omega (t-t_2)+C$$(1)

с четырьмя неизвестными параметрами _A_, _B_, _C_ и $$\omega>0$$. Я сдвинул начало отчета времени в точку _t_~2~, чтобы сразу избавиться от второго слагаемого. Действительно, производная $$f'(t_2)=B\omega$$ должна быть нулевой, потому что касательная в точке _t_~2~ горизонтальна. Это возможно, когда _B_=0.

Так как $$f(t_2)=x_2$$, то подставляя $$t=t_2$$ в&nbsp;(1), получаем $$f(t_2)=A+C$$. Отсюда исключаем _C_:

$$f(t)=x_2 + A\left[\cos\omega (t-t_2)-1\right].$$

Продифференцируем, чтобы найти скорость

$$f'(t)=-A\,\omega\sin\omega (t-t_2).$$

Нам известно положение _x_~1~ и скорость _v_ в начальный момент времени:

$$\begin{cases}
x_1\!\!\!\!\!&=x_2+A\left[\cos\omega(t_1-t_2)-1\right],\\
v\!\!\!\!\!&=-A\,\omega\sin\omega(t_1-t_2).
\end{cases}$$

Из этой системы уравнений нужно найти _A_ и $$\omega$$. Пора вводить новую переменную $$k=\omega(t_2-t_1)$$ вместо $$\omega$$. Ее смысл --- разность фаз синусоиды в начальной и конечной точке. Например, для графика на рис. 4 $$k=\pi$$, потому что на промежутке $$(t_1,t_2)$$ укладывается полупериод синусоиды. На рис. 5 $$k<\pi$$, потому что $$t_2-t_1$$ меньше половины периода.

После подстановки и небольших преобразований приходим к системе

$$\begin{cases}
x_2-x_1&=A\left(1-\cos k\right),\\
v(t_2-t_1)\!\!\!\!&=A\,k\sin k.
\end{cases}$$

Разделим почленно первое уравнение на второе:

$${x_2-x_1\over v(t_2-t_1)}={1-\cos k\over k\sin k}\quad
\Rightarrow\quad{1-\cos k\over\sin k}=\alpha k,\quad\text{где}
\ \alpha={x_2-x_1\over v(t_2-t_1)}.$$

Параметр $$\alpha$$ в правой части известен заранее. Он определяет требуемый характер движения. Если $$\alpha\gg1$$, то начальная скорость мала, тело сначала должно ускориться. Если $$\alpha\ll1$$, начальная скорость велика, тело должно замедляться.

Тригонометрические функции в левой части сводятся к тангенсу половинного угла. В итоге у нас нелинейное уравнение относительно _k_:

$$\text{tg}\,{k\over2}=\alpha k.$$(2)

Проанализировать его решения можно на графике. Нарисуем график левой и правой части при некоторых значениях параметра $$\alpha$$:

$$\begin{tikzpicture}\small
\def\aa{1.5}
\def\ab{0.3}
\def\ac{-0.5}
\begin{axis}[legend pos=south east,mark size=1,samples=2,
	restrict y to domain=-8:8,
	width=12cm, height=250pt,
	xmin=-10.5, xmax=10.5,
	ytick={-6,-3,...,6},
	xtick={-9.4247,-3.1416,...,10},
	xticklabels={$-{3\pi}$,$-{\pi}$,${\pi}$,${3\pi}$},
	axis x line=center,
	axis y line=center,
	xlabel=$k$]
\addplot[blue!70!black,domain=-9.4247:9.4247,semithick,samples=802]{tan(deg(x/2))};
\addplot[red]{\aa*x};
\addplot[green!70!black,domain=-9.4247:9.4247]{\ab*x};
\addplot[olive,domain=-9.4247:9.4247]{\ac*x};
\addplot[mark=*] coordinates {(2.65,3.97)} node[anchor=west]{$A$};
\addplot[mark=*] coordinates {(8.69,2.61)} node[anchor=west]{$B$};
\addplot[mark=*] coordinates {(4.06,-2.03)} node[anchor=west]{$C$};
\legend{$\text{tg}\,k/2$,$\aa\,k$,$\ab\,k$,$\ac\,k$}
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 6. Графическое решение уравнения (2)_</font>

Обсудим получившиеся решения.

1. Рассмотрим точку _A_. Это решение существует при $$\alpha>1/2$$ и соответствует изображенному на рисунке 5: $$\begin{tikzpicture}
\def\t{1}
\def\r{3.1415}
\begin{axis}[width=1.9cm,height=2cm,hide axis,ticks=none,
	xmin=\t,xmax=\r,mark size=0.3]
\addplot[smooth,blue,domain=\t:\r,samples=80] {-cos(deg(x))};
\addplot[mark=*] coordinates {(\t,-cos(deg(x)))};
\addplot[mark=*] coordinates {(\r,-cos(deg(x)))};
\end{axis}
\end{tikzpicture}$$. Как ожидалось, $$k<\pi$$. В пределе нулевой скорости $$\alpha\to\infty$$, красная прямая совпадет с осью ординат, точка A уйдет по тангенсоиде в бесконечность. В этом пределе $$k\to\pi$$. Пока всё идет правильно.

2. Точка _C_ отвечает значению $$\alpha<0$$. Такое случается, когда тело в первый момент времени движется вперед, а надо двигаться назад. Теперь $$\pi<k<2\pi$$. Движение описывается фрагментом синусоиды, большим чем полупериод, но меньшим, чем период:
$$\begin{tikzpicture}
\def\t{-1.7}
\def\r{3.1415}
\begin{axis}[width=2.2cm,height=2cm,hide axis,ticks=none,
	xmin=\t,xmax=\r,mark size=0.3]
\addplot[smooth,blue,domain=\t:\r,samples=80] {cos(deg(x))};
\addplot[mark=*] coordinates {(\t,cos(deg(x)))};
\addplot[mark=*] coordinates {(\r,cos(deg(x)))};
\end{axis}
\end{tikzpicture}$$. Тело тормозит, останавливается, движется назад и останавливается в требуемом месте.

3. Из графика видно, что при $$0<\alpha<1/2$$ точка _B_ попадает в диапазон $$2\pi<k<3\pi$$. Тело пройдет по синусоиде больше, чем полный период колебаний: $$\begin{tikzpicture}
\def\t{-1.6}
\def\r{2*3.1415}
\begin{axis}[width=2.5cm,height=2cm,hide axis,ticks=none,
	xmin=\t,xmax=\r,mark size=0.3]
\addplot[smooth,blue,domain=\t:\r,samples=80] {cos(deg(x))};
\addplot[mark=*] coordinates {(\t,cos(deg(x)))};
\addplot[mark=*] coordinates {(\r,cos(deg(x)))};
\end{axis}
\end{tikzpicture}$$. Причина такого странного решения в том, что точка остановки находится слишком близко по сравнению с характерным расстоянием <nobr>_v_ (_t_~2~ &minus; _t_~1~)</nobr>. Поэтому провести синусоиду без дополнительной остановки и возврата не получится.

<spoiler title="Про квантовую механику">Похожие на&nbsp;(2) уравнения возникают при решении задач квантовой механики про уровни энергии частиц в прямоугольных потенциальных ямах. Там приходится сшивать, например, синусоиду и экспоненту. Условие отсутствия изломов дает подобные уравнения с бесконечным количеством корней.</spoiler>

## Приближенное решение

Мы решили математическую задачу проведения синусоиды, но жизнь от этого проще не стала. Во-первых, чтобы определить параметры синусоиды, надо решить нелинейное уравнение&nbsp;(2). Привет, итерационные методы! Во-вторых, уравнение имеет бесконечное количество решений, а требуемое решение не всегда существует.

Эти трудности возникли от того, что мы зафиксировали продолжительность анимации ровно в 200 миллисекунд. Однако ничего страшного не случится, если анимация продлится, скажем, 180 миллисекунд. Или даже 250 миллисекунд. Нам важнее остановка в заданном месте, а точной продолжительностью анимации мы пожертвуем для упрощения расчетов.

Ослабив требования на продолжительность анимации, мы проделаем такой трюк. Предположим, что у нас есть приближенное решение $$k'$$ нелинейного уравнения&nbsp;(2). Оно является точным решением уравнения с другим параметром

$$\alpha'={1\over k'}\,\text{tg}{k'\over 2},$$

Ему соответствует другое время окончания анимации:

$$t_2'=t_1+{x_2-x_1\over v\alpha'}.$$

Теперь неизвестные параметры траектории _A_ и $$\omega$$ элементарно выражаются через $$k'$$ и $$\alpha'$$.

Я подобрал подходящее для наших целей приближение к уравнению&nbsp;(2):

$${1\over 2\alpha}\approx1-\left({k\over\pi}\right)^2.$$

Синяя сплошная линия соответствует точному уравнению&nbsp;(2), а красная пунктирная --- его приближению:

$$\begin{tikzpicture}\small
\begin{axis}[legend pos=south east,
	restrict y to domain=-8:8,
	width=12cm,
	xmin=-7.3, xmax=7.3,
	ytick={-6,-3,...,6},
	xtick={-6.2832,-3.1416,...,10},
	xticklabels={$-{2\pi}$,$-{\pi}$,$0$,${\pi}$,${2\pi}$},
	axis x line=center,axis y line=center,
	xlabel=$k$,ylabel=$\alpha$]
\addplot[smooth,samples=580,blue!70!black,domain=-7:7]{tan(deg(x/2))/x};
\addplot[smooth,samples=580,red,dashed,domain=-7:7]{0.5/(1 - (x/pi)^2)};
\legend{$(1/k)\,\text{tg}\,k/2$,$0.5/\!\left[1 - ({k/\pi})^2\right]$}
\end{axis}
\end{tikzpicture}$$

<font color="#666">_Рис. 7. Сравнение точного соотношения&nbsp;(2) и его приближения_</font>

А еще в случае $$0<\alpha<1/2$$ предлагаю взять $$\alpha'$$ чуть больше, чем 1/2, и сократить время анимации, чтобы избежать отскока и возврата.

## Применение

Код и сферический пример использования есть [на демо-странице](https://parpalak.github.io/demo/sin-animate.html). Поводите мышью и посмотрите, как черный блок следует за оранжевым.

Описанная схема применяется и в готовом продукте. Я разработал ее для синхронной прокрутки исходника и предпросмотра в [markdown- и latex- редакторе математических текстов](https://tex.s2cms.ru/page/).

Идею и первоначальную реализацию нашел [на демо-странице js-парсера markdown-it](https://markdown-it.github.io/). В их варианте анимация получилась рваной и подтормаживающей. Тому есть несколько причин:

1. Для анимации применяется линейная функция: `$(...).stop(true).animate({scrollTop: ...}, 100, 'linear')`. Вместо гладкого графика получается ломаная.
2. Анимация через `jQuery().stop().animate()` тормозит по сравнению с `requestAnimationFrame()`.
3. Чтобы избежать падения производительности, "проглатываются" события `onscroll`, следующие чаще чем 50 миллисекунд. В моем варианте такой проблемы нет. Последовательные события `onscroll` корректируют положение точки остановки и не замедляют анимацию.

Чтобы добиться важной для продукта качественной анимации, я проработал метод вычисления на основе физических уравнений, и реализовал его через специальный браузерный метод `requestAnimationFrame()`. Метод хорошо работает при любой прокрутке: клавишами PageUp/PageDown, через перемещение полос прокрутки, колесико мыши, тачпад, тачскрин.

<spoiler title="Исходник поста и картинок">Этот пост я набирал в упомянутом редакторе. Выкладываю [исходник](https://raw.githubusercontent.com/parpalak/parpalak.github.io/master/sources/markdown/sin_animate.md), может кому-нибудь пригодится tex-код графиков.</spoiler>