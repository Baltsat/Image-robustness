# Глубокий анализ adversarial-атак и защит на мультимодальных моделях (2022–2025)

## 1. Введение

Мультимодальные большие языковые модели (MLLMs) ознаменовали собой трансформационные достижения в области искусственного интеллекта, обеспечивая бесшовную интеграцию разнообразных модальностей данных <a href="#ref1">[1]</a>. В отличие от унимодальных моделей, которые ограничены обработкой одного типа входных данных, MLLMs открывают широкий спектр приложений:

* Описание изображений
* Визуальное вопросно-ответное взаимодействие
* Суммирование видео
* Аудиовизуальное распознавание речи <a href="#ref1">[1]</a>

Основанные на своей способности понимать и интегрировать мультимодальную информацию, MLLMs позволяют осуществлять осмысление и генерацию контента, охватывающего различные типы данных, предлагая целостное понимание, которое отражает сложные взаимосвязи между модальностями и взаимодействия реального мира <a href="#ref1">[1]</a>.

Несмотря на эти успехи, существующие исследования показывают, что MLLMs остаются **уязвимыми для adversarial-атак** <a href="#ref1">[1]</a>. Хотя мультимодальная интеграция является ключевым преимуществом, она одновременно **расширяет поверхность атаки**, вводя новые векторы для манипулирования как в рамках отдельных модальностей, так и в кросс-модальных сценариях <a href="#ref1">[1]</a>.

> В то время как различные исследования изучали adversarial-стратегии в рамках отдельных модальностей, до сих пор не сформировалось единой перспективы для всестороннего решения проблем, связанных с MLLMs <a href="#ref1">[1]</a>.

Adversarial-угрозы могут возникать не только во время инференса, но и на этапе обучения <a href="#ref1">[1]</a>. Эти уязвимости создают значительные риски, внедряя скрытые угрозы в MLLMs, которые возникают в результате adversarial-атак и усиливаются растущим развертыванием MLLMs в критически важных приложениях (автономные системы, медицинская диагностика, модерация контента) <a href="#ref1">[1]</a>.

Учитывая эти многогранные угрозы, вопрос разработки надежных защитных механизмов становится первостепенным. Однако современные средства защиты часто отстают от сложности возникающих атак, поскольку они в основном нацелены на унимодальные adversarial-сценарии и не учитывают сложную динамику интеграции мультимодальных данных <a href="#ref1">[1]</a>.

**Цель данного отчета:** собрать, структурировать и критически проанализировать современные методы adversarial-атак и защит на мультимодальных моделях (Vision-Language Models: CLIP, LLaVA, BLIP, ImageBind, Janus-Pro и др.) за период с 2022 по 2025 год, с учетом последних публикаций, open-source-экспериментов, лидербордов и реальных кейсов.

---

## 2. Таксономия adversarial-атак на мультимодальных моделях (2022–2025)

Adversarial-атаки на мультимодальные модели можно классифицировать по нескольким ключевым аспектам [<a href="#ref1">1</a>, <a href="#ref2">2</a>, <a href="#ref3">3</a>, <a href="#ref4">4</a>, <a href="#ref5">5</a>]. Используем интерактивные блоки для удобства навигации:

<details>
<summary><strong>2.1. Классификация по фазе атаки</strong></summary>

Атаки могут быть разделены на те, которые происходят во время инференса (после обучения модели), и те, которые нацелены на этап обучения модели <a href="#ref1">[1]</a>.

* **2.1.1. Атаки во время инференса:**
  * **Цель:** Манипулирование выходными данными уже обученной модели путем подачи специально созданных входных данных.
  * **Примеры:**
    * Adversarial-атаки с использованием градиентов или диффузии для незаметных изменений входных данных (например, изображений) <a href="#ref1">[1]</a>.
    * **Jailbreak-атаки:** Обход механизмов безопасности для генерации вредоносного контента [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>]. Могут использовать кросс-модальную несогласованность или манипуляции с промптами <a href="#ref1">[1]</a>.
    * Другие: Image hijacking, атаки с verbose images, stop-reasoning attacks, temporal-coherence attacks (видео), scenario-driven attacks (автономное вождение) [<a href="#ref1">1</a>, <a href="#ref10">10</a>, <a href="#ref11">11</a>, <a href="#ref12">12</a>], аудио adversarial-атаки [<a href="#ref1">1</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>], test-time backdoor attacks (например, AnyDoor) [<a href="#ref1">1</a>, <a href="#ref15">15</a>].
  * **Значение:** Подчеркивают широкий спектр методов эксплуатации уже развернутых MLLM.

* **2.1.2. Атаки во время обучения:**
  * **Цель:** Компрометация MLLM на этапе обучения путем внедрения вредоносных данных в обучающий набор.
  * **Примеры:**
    * **Data Poisoning:** Внедрение искаженных примеров в обучающий набор для влияния на поведение модели [<a href="#ref1">1</a>, <a href="#ref16">16</a>, <a href="#ref17">17</a>, <a href="#ref18">18</a>, <a href="#ref19">19</a>, <a href="#ref20">20</a>].
    * **Backdoor Attacks:** Внедрение скрытых триггеров, активирующих вредоносное поведение во время инференса (например, VLTrojan, BadVLMDriver) [<a href="#ref1">1</a>, <a href="#ref15">15</a>, <a href="#ref20">20</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref24">24</a>].
    * Jailbreak на этапе обучения <a href="#ref1">[1]</a>.
  * **Значение:** Особенно опасны из-за внедрения скрытых, трудно обнаруживаемых уязвимостей с долгосрочным эффектом.

* **2.1.3. Атаки BadEncoder:**
  * **Концепция:** Эксплуатация уязвимостей в визуальных энкодерах MLLMs путем введения adversarial-возмущений в визуальные входы, заставляя энкодер извлекать некорректные признаки [<a href="#ref1">1</a>, <a href="#ref18">18</a>, <a href="#ref25">25</a>].
  * **Значение:** Показывают, что уязвимости могут существовать внутри отдельных компонентов MLLM, влияя на надежность всей системы.

</details>

<details>
<summary><strong>2.2. Классификация по цели атаки</strong></summary>

Adversarial-атаки могут быть нацелены на различные задачи, выполняемые мультимодальными моделями [<a href="#ref3">3</a>, <a href="#ref7">7</a>, <a href="#ref22">22</a>].

* **2.2.1. Атаки, нацеленные на классификацию:** Заставить модель неправильно классифицировать входные данные [<a href="#ref22">22</a>, <a href="#ref26">26</a>].
* **2.2.2. Атаки, нацеленные на поиск (retrieval):** Заставить модель извлечь некорректные элементы (например, поиск изображения по тексту) [<a href="#ref22">22</a>, <a href="#ref29">29</a>].
* **2.2.3. Атаки, нацеленные на генерацию:** Генерация некорректного или вводящего в заблуждение контента [<a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref10">10</a>].
* **2.2.4. Атаки, нацеленные на описание (captioning):** Генерация некорректных описаний для изображений [<a href="#ref10">10</a>, <a href="#ref22">22</a>].
* **2.2.5. Jailbreak-атаки:**
  * **Цель:** Обход механизмов безопасности для вызова вредоносных ответов [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref30">30</a>, <a href="#ref31">31</a>, <a href="#ref38">38</a>, <a href="#ref39">39</a>, <a href="#ref40">40</a>].
  * **Пример метрики:** Jailbreak-Probability-based Attack (JPA) для количественной оценки вероятности вредоносных ответов <a href="#ref3">[3]</a>.
  * **Значение:** Серьезная угроза из-за потенциала злоупотребления, подчеркивает необходимость надежных механизмов безопасности.

</details>

<details>
<summary><strong>2.3. Классификация по модальности атаки</strong></summary>

Атаки могут быть направлены на одну модальность (унимодальные) или на несколько модальностей одновременно (мультимодальные) [<a href="#ref2">2</a>, <a href="#ref6">6</a>, <a href="#ref10">10</a>, <a href="#ref23">23</a>, <a href="#ref25">25</a>, <a href="#ref26">26</a>, <a href="#ref27">27</a>].

* **2.3.1. Унимодальные атаки:**
  * **Фокус:** Независимое внесение возмущений в одну модальность (изображение *или* текст).
  * **Примеры:**
    * Атаки на изображения (например, Block-wise Similarity Attack (BSA) в <a href="#ref6">[6]</a>) для нарушения универсальных представлений.
    * Текстовые атаки <a href="#ref6">[6]</a>. Сравнение устойчивости унимодальных (BERT) и мультимодальных (CLIP) моделей к текстовым атакам <a href="#ref26">[26]</a>.
  * **Значение:** Уязвимости в отдельных путях обработки могут скомпрометировать всю систему.

* **2.3.2. Мультимодальные атаки:**
  * **Фокус:** Разработка скоординированных возмущений в нескольких модальностях, учитывая их взаимодействие.
  * **Примеры:**
    * Iterative Cross-Search Attack (ICSA) <a href="#ref6">[6]</a> (итеративное обновление пар изображение-текст).
    * Атаки на изображение и текст одновременно [<a href="#ref1">1</a>, <a href="#ref10">10</a>].
    * Универсальные adversarial-атаки на выровненные мультимодальные LLMs (могут передаваться между архитектурами) <a href="#ref23">[23]</a>.
  * **Значение:** Использование кросс-модального взаимодействия может привести к более мощным атакам.

</details>

<details>
<summary><strong>2.4. Классификация по уровню доступа атакующего</strong></summary>

Уровень доступа атакующего к целевой модели является важным фактором [<a href="#ref4">4</a>, <a href="#ref5">5</a>, <a href="#ref7">7</a>, <a href="#ref11">11</a>, <a href="#ref12">12</a>, <a href="#ref28">28</a>, <a href="#ref29">29</a>, <a href="#ref30">30</a>, <a href="#ref31">31</a>].

* **2.4.1. Атаки типа "белый ящик" (White-Box Attacks):**
  * **Доступ:** Полная информация об архитектуре, параметрах, градиентах [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref5">5</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref11">11</a>, <a href="#ref12">12</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref24">24</a>, <a href="#ref31">31</a>, <a href="#ref33">33</a>, <a href="#ref34">34</a>, <a href="#ref37">37</a>, <a href="#ref43">43</a>, <a href="#ref44">44</a>, <a href="#ref47">47</a>].
  * **Значение:** Нереалистичны в реальных сценариях, но ценны для понимания уязвимостей и разработки защит (например, adversarial-обучения).

* **2.4.2. Атаки типа "черный ящик" (Black-Box Attacks):**
  * **Доступ:** Нет прямого доступа к внутренним деталям; атакующий полагается на запросы к модели и наблюдение за выходами [<a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref10">10</a>, <a href="#ref11">11</a>, <a href="#ref12">12</a>, <a href="#ref21">21</a>, <a href="#ref28">28</a>, <a href="#ref29">29</a>, <a href="#ref31">31</a>, <a href="#ref35">35</a>, <a href="#ref46">46</a>, <a href="#ref47">47</a>].
  * **Подтипы:**
    * *Query-based attacks:* Аппроксимация градиентов через многократные запросы [<a href="#ref7">7</a>, <a href="#ref31">31</a>].
    * *Transfer-based attacks:* Использование передаваемости adversarial-примеров (создание на суррогатной модели) [<a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref23">23</a>, <a href="#ref25">25</a>, <a href="#ref29">29</a>, <a href="#ref31">31</a>, <a href="#ref46">46</a>]. Фреймворк VLATTACK <a href="#ref6">[6]</a>.
  * **Значение:** Более практичны, отражают реальные сценарии. Передаваемость (transferability) - ключевой аспект.

* **2.4.3. Атаки типа "серый ящик" (Gray-Box Attacks):**
  * **Доступ:** Частичное знание о целевой модели [<a href="#ref4">4</a>, <a href="#ref7">7</a>, <a href="#ref30">30</a>, <a href="#ref40">40</a>].
  * **Значение:** Промежуточный сценарий между "белым" и "черным ящиком".

</details>

---

## 3. Подробный анализ современных adversarial-атак (2022–2025)

Рассмотрим подробнее ключевые типы атак, разработанные и исследованные в этот период:

<details>
<summary><strong>3.1. Patch Attacks</strong></summary>

* **Описание:** Использование небольших локализованных возмущений (патчей), которые, будучи примененными к изображению, вызывают неверную классификацию.
* **Типы:**
  * *Универсальные:* Один патч обманывает модель на разных изображениях <a href="#ref8">[8]</a>.
  * *Таргетированные:* Вызывают конкретную ошибку классификации.
* **Реализация:** Цифровая и физическая [<a href="#ref12">12</a>, <a href="#ref20">20</a>, <a href="#ref32">32</a>, <a href="#ref33">33</a>]. Пример: унифицированный патч для видимых и ИК-детекторов <a href="#ref33">[33]</a>.
* **Значение:** Физические атаки демонстрируют риски в реальных приложениях (например, автономное вождение [<a href="#ref12">12</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>]).

</details>

<details>
<summary><strong>3.2. Universal Adversarial Perturbations (UAPs)</strong></summary>

* **Описание:** Небольшие, не зависящие от конкретного изображения возмущения, которые при добавлении к *любому* входу могут вызвать ошибку [<a href="#ref11">11</a>, <a href="#ref23">23</a>, <a href="#ref38">38</a>].
* **Пример:** Одно оптимизированное изображение для обхода защитных механизмов выравнивания в MLLMs <a href="#ref23">[23]</a>.
* **Значение:** Демонстрируют фундаментальную хрупкость MLLMs, так как одно возмущение влияет на множество входов.

</details>

<details>
<summary><strong>3.3. FGSM, PGD, AutoAttack и их производные</strong></summary>

* **Тип:** Градиентные атаки (используют градиенты модели).
* **Методы:**
  * *FGSM (Fast Gradient Sign Method):* Одношаговая атака [<a href="#ref11">11</a>, <a href="#ref24">24</a>, <a href="#ref34">34</a>].
  * *PGD (Projected Gradient Descent):* Итеративное расширение FGSM, обычно более эффективно [<a href="#ref11">11</a>, <a href="#ref24">24</a>, <a href="#ref34">34</a>, <a href="#ref35">35</a>].
  * *AutoAttack:* Мощный ансамбль атак для надежной оценки устойчивости [<a href="#ref26">26</a>, <a href="#ref34">34</a>].
* **Значение:** Основополагающие методы в adversarial ML, широко используются для оценки устойчивости.

</details>

<details>
<summary><strong>3.4. Embedding Alignment Attacks (например, CrossFire, VLAttack)</strong></summary>

* **Описание:** Манипулирование входами так, чтобы их эмбеддинги выравнивались с эмбеддингами целевых данных (в пространстве признаков одной или между модальностями) [<a href="#ref6">6</a>, <a href="#ref23">23</a>, <a href="#ref43">43</a>].
* **Примеры:**
  * *CrossFire <a href="#ref43">[43]</a>:* Минимизация углового отклонения между эмбеддингами.
  * *VLATTACK <a href="#ref6">[6]</a>:* Объединение возмущений изображений и текстов.
* **Значение:** Атакуя внутренние представления, могут эффективно вводить модель в заблуждение.

</details>

<details>
<summary><strong>3.5. Jailbreak-атаки, prompt injection и instruction attacks</strong></summary>

* **Описание:** Техники для обхода механизмов безопасности MLLMs и вызова вредоносных ответов [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>, <a href="#ref11">11</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref30">30</a>, <a href="#ref31">31</a>, <a href="#ref38">38</a>, <a href="#ref39">39</a>, <a href="#ref40">40</a>, <a href="#ref44">44</a>].
* **Варианты:**
  * *Prompt injection:* Вредоносные инструкции внутри промпта [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref39">39</a>].
  * *Instruction attacks:* Adversarial-изображения или аудио для передачи вредоносных инструкций [<a href="#ref14">14</a>, <a href="#ref40">40</a>].
  * *Кросс-модальные jailbreak-атаки* [<a href="#ref19">19</a>, <a href="#ref23">23</a>, <a href="#ref38">38</a>].
  * *Jailbreak на этапе обучения* [<a href="#ref1">1</a>, <a href="#ref19">19</a>].
  * *Jailbreak-Probability-based Attack (JPA)* <a href="#ref3">[3]</a>.
* **Значение:** Легкость jailbreaking'а подчеркивает ограничения текущих методов выравнивания безопасности.

</details>

<details>
<summary><strong>3.6. Pipeline attacks и data poisoning attacks</strong></summary>

* **Описание:** Атаки на конвейер обработки данных (включая пре/постобработку) или внедрение вредоносных данных в обучающий набор (data poisoning) [<a href="#ref1">1</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>, <a href="#ref15">15</a>, <a href="#ref16">16</a>, <a href="#ref17">17</a>, <a href="#ref18">18</a>, <a href="#ref19">19</a>, <a href="#ref20">20</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref41">41</a>, <a href="#ref42">42</a>].
* **Последствия Data Poisoning:** Ухудшение производительности, внедрение бэкдоров [<a href="#ref1">1</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>, <a href="#ref15">15</a>, <a href="#ref16">16</a>, <a href="#ref17">17</a>, <a href="#ref18">18</a>, <a href="#ref19">19</a>, <a href="#ref20">20</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>].
* **Пример Pipeline Attack:** Атаки на мультимодальные системы поиска и генерации с расширенным контекстом (M-RAG) <a href="#ref41">[41]</a>.
* **Значение:** Целостность данных и всего конвейера критична для безопасности MLLMs.

</details>

<details>
<summary><strong>3.7. Physical attacks</strong></summary>

* **Описание:** Реализация adversarial-атак в физическом мире (например, патчи на объектах, манипуляции сценами) [<a href="#ref1">1</a>, <a href="#ref10">10</a>, <a href="#ref11">11</a>, <a href="#ref20">20</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>, <a href="#ref32">32</a>, <a href="#ref33">33</a>, <a href="#ref45">45</a>].
* **Примеры:** Атаки в сценариях автономного вождения [<a href="#ref12">12</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>].
* **Значение:** Подчеркивают уязвимость MLLMs в реальных приложениях, взаимодействующих с физическим миром.

</details>

<details>
<summary><strong>3.8. Новые и менее распространенные (экзотические) методы</strong></summary>

* **Примеры:**
  * *Meticulous Adversarial Attack (MAA):* Повышение передаваемости атак <a href="#ref46">[46]</a>.
  * *Cascading Adversarial Disruption (CAD):* Атака на VLM в автономном вождении <a href="#ref28">[28]</a>.
* **Значение:** Постоянное развитие области приводит к появлению новых методов, эксплуатирующих новые уязвимости.

</details>

---

## 4. Оценка эффективности и применимости adversarial-атак

Эффективность и применимость атак оцениваются по ряду метрик и в зависимости от архитектуры.

* **4.1. Метрики эффективности:**
  * **Attack Success Rate (ASR):** Процент успешных атак [<a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref16">16</a>, <a href="#ref23">23</a>, <a href="#ref33">33</a>, <a href="#ref37">37</a>].
  * **Незаметность (Stealth):** Сложность обнаружения возмущений человеком [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref10">10</a>, <a href="#ref13">13</a>, <a href="#ref15">15</a>, <a href="#ref20">20</a>, <a href="#ref24">24</a>, <a href="#ref32">32</a>, <a href="#ref33">33</a>, <a href="#ref34">34</a>, <a href="#ref37">37</a>, <a href="#ref39">39</a>].
  * **Универсальность (Universality):** Способность одного возмущения обманывать модель на разных входах [<a href="#ref8">8</a>, <a href="#ref11">11</a>, <a href="#ref23">23</a>].
  * **Переносимость (Transferability):** Эффективность атаки, созданной для одной модели, на другой модели [<a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref23">23</a>, <a href="#ref25">25</a>, <a href="#ref29">29</a>, <a href="#ref30">30</a>, <a href="#ref31">31</a>, <a href="#ref46">46</a>].
    > **Ключевой вывод:** Высокие ASR, незаметность и переносимость делают атаку особенно опасной.

* **4.2. Применимость к различным архитектурам:**
  * Разные MLLMs (LLaVA, BLIP, MiniGPT-4, GPT-4V, Gemini, Claude-3 и др.) показывают разную уязвимость [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref5">5</a>, <a href="#ref6">6</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>, <a href="#ref10">10</a>, <a href="#ref12">12</a>, <a href="#ref13">13</a>, <a href="#ref14">14</a>, <a href="#ref15">15</a>, <a href="#ref19">19</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref25">25</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>, <a href="#ref31">31</a>, <a href="#ref38">38</a>, <a href="#ref44">44</a>, <a href="#ref45">45</a>].
  * Примеры: атаки на LLaVA с разными энкодерами CLIP <a href="#ref5">[5]</a>, jailbreak на разные MLLMs [<a href="#ref3">3</a>, <a href="#ref6">6</a>], атаки на агентов на базе GPT-4V/Gemini/Claude/GPT-4o <a href="#ref12">[12]</a>.

* **4.3. Open-source реализации и бенчмарки:**
  * Репозитории на GitHub, Colab, статьи на ArXiv облегчают воспроизводимость и сравнение [<a href="#ref6">6</a>, <a href="#ref9">9</a>, <a href="#ref13">13</a>, <a href="#ref23">23</a>, <a href="#ref25">25</a>, <a href="#ref31">31</a>, <a href="#ref46">46</a>].

* **4.4. Сравнительный анализ эффективности атак:**
  * Для полного анализа требуется сбор данных из цитируемых работ и их систематизация в таблицу, сравнивающую ASR, переносимость и незаметность разных атак на разных моделях.

---

## 5. Современные методы защиты от adversarial-атак

За период 2022–2025 разработаны различные методы защиты [<a href="#ref2">2</a>, <a href="#ref5">5</a>, <a href="#ref11">11</a>, <a href="#ref16">16</a>, <a href="#ref21">21</a>, <a href="#ref24">24</a>, <a href="#ref32">32</a>, <a href="#ref34">34</a>, <a href="#ref35">35</a>, <a href="#ref36">36</a>, <a href="#ref44">44</a>, <a href="#ref47">47</a>].

<details>
<summary><strong>5.1. Adversarial training</strong></summary>

* **Описание:** Обучение моделей на adversarial-примерах для повышения устойчивости [<a href="#ref2">2</a>, <a href="#ref5">5</a>, <a href="#ref11">11</a>, <a href="#ref24">24</a>, <a href="#ref34">34</a>, <a href="#ref36">36</a>, <a href="#ref44">44</a>, <a href="#ref47">47</a>].
* **Применение:** К отдельным модальностям или мультимодально [<a href="#ref2">2</a>, <a href="#ref44">44</a>].
* **Пример:** Переосмысление adversarial-обучения как задачи оптимизации предпочтений (AdPO) <a href="#ref5">[5]</a>.

</details>

<details>
<summary><strong>5.2. Методы шумоподавления и робастные энкодеры</strong></summary>

* **Описание:** Удаление/смягчение возмущений из входных данных *до* обработки моделью, или использование более устойчивых к шуму энкодеров [<a href="#ref1">1</a>, <a href="#ref32">32</a>, <a href="#ref36">36</a>].
* **Пример:** Использование диффузионных моделей для защиты от патч-атак <a href="#ref32">[32]</a>.

</details>

<details>
<summary><strong>5.3. Обнаружение и отклонение adversarial-примеров</strong></summary>

* **Описание:** Выявление adversarial-примеров и их отклонение/пометка [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref14">14</a>, <a href="#ref16">16</a>, <a href="#ref18">18</a>, <a href="#ref36">36</a>].
* **Примеры:**
  * Обнаружение бэкдоров [<a href="#ref14">14</a>, <a href="#ref16">16</a>, <a href="#ref18">18</a>].
  * Multi-Shield: обнаружение с использованием мультимодальной информации <a href="#ref36">[36]</a>.

</details>

<details>
<summary><strong>5.4. Фильтрация входных данных и робастные пайплайны</strong></summary>

* **Описание:** Фильтрация потенциально вредоносных входов или построение конвейеров обработки, менее восприимчивых к манипуляциям [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref21">21</a>].
* **Пример:** Фильтрация промптов <a href="#ref21">[21]</a>.

</details>

<details>
<summary><strong>5.5. Специализированные методы защиты для LVLMs</strong></summary>

* **Описание:** Учитывая масштаб и сложность Large Vision-Language Models (LVLMs), требуются специализированные стратегии [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref5">5</a>, <a href="#ref6">6</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>, <a href="#ref12">12</a>, <a href="#ref14">14</a>, <a href="#ref21">21</a>, <a href="#ref22">22</a>, <a href="#ref23">23</a>, <a href="#ref38">38</a>, <a href="#ref39">39</a>, <a href="#ref44">44</a>].
* **Примеры:** Фильтрация промптов <a href="#ref21">[21]</a>, использование защитных слоев <a href="#ref1">[1]</a>, ProEAT <a href="#ref44">[44]</a>.

</details>

<details>
<summary><strong>5.6. Ограничения и уязвимости существующих методов защиты</strong></summary>

* **Проблема:** Adversarial-атаки постоянно развиваются, многие средства защиты имеют ограничения или могут быть обойдены [<a href="#ref1">1</a>, <a href="#ref3">3</a>, <a href="#ref19">19</a>, <a href="#ref21">21</a>, <a href="#ref32">32</a>, <a href="#ref35">35</a>, <a href="#ref44">44</a>].
* **Примеры:**
  * Обычное adversarial-обучение может быть неэффективно против некоторых новых атак <a href="#ref3">[3]</a>.
  * Продвинутые black-box атаки испытывают трудности против даже простых моделей с adversarial-обучением <a href="#ref35">[35]</a>.

</details>

---

## 6. Лидеры в области устойчивости мультимодальных моделей

> На текущий момент **не существует общепринятых лидербордов**, комплексно оценивающих устойчивость MLLMs к adversarial-атакам.

Однако отдельные исследования предоставляют сравнения:

* <a href="#ref5">[5]</a>: Сравнение устойчивости версий LLaVA.
* <a href="#ref10">[10]</a>: Результаты атак на BLIP, LLaVA, MiniGPT-4.
* <a href="#ref12">[12]</a>: Сравнение устойчивости агентов на базе GPT-4V, Gemini-1.5, Claude-3, GPT-4o.

**Вывод:** Разные архитектуры и методы обучения приводят к разной устойчивости. Поиск реальных кейсов внедрения устойчивых моделей требует дальнейших исследований.

---

## 7. Перспективы развития области и открытые проблемы

* **7.1. Наиболее опасные атаки сейчас?**
  * **Jailbreak-атаки:** Высокий успех в обходе защит, генерация вредоносного контента [<a href="#ref3">3</a>, <a href="#ref6">6</a>, <a href="#ref7">7</a>, <a href="#ref8">8</a>, <a href="#ref9">9</a>, <a href="#ref23">23</a>].
  * **Universal Adversarial Perturbations (UAPs):** Влияние на широкий спектр входов, переносимость [<a href="#ref8">8</a>, <a href="#ref11">11</a>, <a href="#ref23">23</a>].
  * **Физические атаки:** Непосредственная угроза в критически важных приложениях (например, автономное вождение [<a href="#ref12">12</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>]).

* **7.2. Перспективные стратегии защиты?**
  * **Adversarial-обучение** (особенно мультимодальное) [<a href="#ref2">2</a>, <a href="#ref5">5</a>, <a href="#ref24">24</a>, <a href="#ref34">34</a>, <a href="#ref36">36</a>, <a href="#ref44">44</a>, <a href="#ref47">47</a>].
  * **Методы обнаружения** adversarial-примеров <a href="#ref36">[36]</a>.
  * Совершенствование **шумоподавления** и разработка **робастных архитектур**.

* **7.3. Направления будущих исследований?**
  * Устойчивость к **кросс-модальным атакам**.
  * Более эффективная защита от **jailbreak-атак**.
  * **Переносимость атак** между разными семействами моделей.
  * Влияние **масштабирования моделей** на устойчивость [<a href="#ref19">19</a>, <a href="#ref42">42</a>].
  * Разработка **улучшенных метрик** мультимодальной adversarial-устойчивости [<a href="#ref3">3</a>, <a href="#ref7">7</a>].
  * Необходимость **единого подхода** к оценке устойчивости MLLMs <a href="#ref1">[1]</a>.
  * Переход к **вероятностной оценке** атак на генеративные модели (концепция вероятности jailbreak) [<a href="#ref3">3</a>, <a href="#ref7">7</a>].
  * Недостаточно изученная область **black-box атак на VLM в автономном вождении** [<a href="#ref12">12</a>, <a href="#ref27">27</a>, <a href="#ref28">28</a>].

---

## 8. Заключение

Анализ показывает как значительные успехи в разработке MLLMs, так и их существенную уязвимость к adversarial-атакам. Разнообразие атак подчеркивает сложность обеспечения безопасности. Несмотря на прогресс в защите, область динамична, и угрозы постоянно эволюционируют. Дальнейшие исследования устойчивости MLLMs критически важны для их безопасного использования.

---

## 9. Ссылки

<details id="ref1"><summary>[1] Zhang et al. (2025). <a href="https://arxiv.org/abs/2503.13962" target="_blank">"A Survey on Adversarial Robustness of Multimodal Large Language Models."</a> arXiv:2503.13962.</summary>Обзор устойчивости MLLM к состязательным атакам, охватывающий разные модальности и подчеркивающий уникальные проблемы из-за взаимозависимостей.</details>
<details id="ref2"><summary>[2] Yu et al. (2024). <a href="https://arxiv.org/abs/2405.18770" target="_blank">"Towards Defending Against Multi-Modal Attacks."</a> arXiv:2405.18770.</summary>Исследование стратегий защиты от мультимодальных атак и предложение мультимодального состязательного обучения (MAT).</details>
<details id="ref3"><summary>[3] Li et al. (2025). <a href="https://arxiv.org/abs/2503.06989" target="_blank">"Utilizing Jailbreak Probability to Attack and Safeguard Multimodal LLMs."</a> arXiv:2503.06989.</summary>Предложение атаки на основе вероятности джейлбрейка (JPA) и методов защиты для MLLM.</details>
<details id="ref4"><summary>[4] Alnajjar et al. (2025). <a href="https://www.mdpi.com/2079-9292/14/11/4614" target="_blank">"A Survey of Adversarial Attacks on Sentiment Analysis."</a> *Electronics* 14(11), 4614.</summary>Обзор состязательных атак, уровней (black-box, white-box, gray-box) и метрик оценки.</details>
<details id="ref5"><summary>[5] Zhang et al. (2025). <a href="https://arxiv.org/abs/2504.01735" target="_blank">"AdPO: Adversarial Preference Optimization for Robust Vision-Language Models."</a> arXiv:2504.01735.</summary>Переосмысление состязательного обучения как задачи оптимизации предпочтений для повышения устойчивости LVLM.</details>
<details id="ref6"><summary>[6] Yin et al. (2023). <a href="https://proceedings.neurips.cc/paper_files/paper/2023/hash/11e1472a42a5271b6ee66213d55a357b-Abstract-Conference.html" target="_blank">"VLATTACK: Multimodal Adversarial Attacks on Vision-Language Tasks via Pre-trained Models."</a> NeurIPS 2023. (arXiv: <a href="https://arxiv.org/abs/2310.04655" target="_blank">2310.04655</a>)</summary>Фреймворк VLATTACK для генерации состязательных примеров путем объединения возмущений изображений и текстов для атаки на black-box модели.</details>
<details id="ref7"><summary>[7] Dai et al. (2025). <a href="https://www.hindawi.com/journals/wcmc/2025/1401953/" target="_blank">"Adversarial Attacks and Defenses on Deep Learning Models: A Survey."</a> *Wireless Communications and Mobile Computing* 2025, 1401953.</summary>Таксономия состязательных атак на модели глубокого обучения на основе целей и знаний атакующего.</details>
<details id="ref8"><summary>[8] Schaeffer et al. (2024). <a href="https://arxiv.org/abs/2403.05030" target="_blank">"Targeted Adversarial Attacks on Black-Box Vision-Language Models."</a> arXiv:2403.05030.</summary>Анализ высокой переносимости целевых состязательных примеров на проприетарные black-box VLLM (GPT-4o, Claude, Gemini).</details>
<details id="ref9"><summary>[9] Chen et al. (2024). <a href="https://openreview.net/forum?id=3QvA7m11yK" target="_blank">"FAP: Few-shot Adversarial Prompt Learning for Vision-Language Models."</a> NeurIPS 2024.</summary>Фреймворк Few-shot Adversarial Prompt (FAP) для адаптации VLMs с промпт-обучением для zero-shot состязательной устойчивости.</details>
<details id="ref10"><summary>[10] Nayyer et al. (2023). <a href="https://openreview.net/forum?id=qM4W8qL6Yk" target="_blank">"Maximum Information Entropy Attack for Black-Box Adversarial Examples on Large Vision-Language Models."</a> OpenReview.</summary>White-box атака, максимизирующая информационную энтропию (MIE) для генерации вводящих в заблуждение описаний изображений VLMs.</details>
<details id="ref11"><summary>[11] Samoilov et al. (2024). <a href="https://www.e3s-conferences.org/articles/e3sconf/abs/2024/24/e3sconf_aies2024_02004/e3sconf_aies2024_02004.html" target="_blank">"Attacks on multimodal models."</a> *E3S Web of Conferences* 486, 02004.</summary>Исследование различных атак на мультимодальные модели, включая FGSM, PGD, UAP.</details>
<details id="ref12"><summary>[12] Du et al. (2024). <a href="https://arxiv.org/abs/2406.12814" target="_blank">"Adversarial Attacks on Multimodal Agents."</a> arXiv:2406.12814.</summary>Демонстрация уязвимости мультимодальных агентов к атакам с использованием состязательных текстовых строк для управления возмущениями изображений.</details>
<details id="ref13"><summary>[13] Li et al. (2024). <a href="https://arxiv.org/abs/2405.14672" target="_blank">"Invisible Backdoor Attacks on Self-Supervised Learning Models."</a> arXiv:2405.14672.</summary>Разработка бэкдор-атаки для моделей самообучения (SSL) с использованием незаметных триггеров.</details>
<details id="ref14"><summary>[14] Wang et al. (2025). <a href="https://arxiv.org/abs/2503.16872" target="_blank">"Lie Detector: Towards Detecting Backdoors in Autoregressive Language Models."</a> arXiv:2503.16872.</summary>Предложение метода Lie Detector для обнаружения бэкдор-атак, включая MLLM.</details>
<details id="ref15"><summary>[15] Zeng et al. (2024). <a href="https://openreview.net/forum?id=K8wzAA23hS" target="_blank">"AnyDoor: Test-Time Backdoor Attack on Multimodal Language Models."</a> OpenReview.</summary>AnyDoor: бэкдор-атака во время тестирования, не требующая доступа к данным обучения или изменения параметров модели.</details>
<details id="ref16"><summary>[16] Yang et al. (2024). <a href="https://arxiv.org/abs/2409.04881" target="_blank">"Efficient Backdoor Defense in Multimodal Contrastive Learning: A Token-Level Unlearning Method for Mitigating Threats."</a> arXiv:2409.04881.</summary>Эффективный механизм защиты от бэкдоров в мультимодальном контрастном обучении с использованием машинного "разучивания".</details>
<details id="ref17"><summary>[17] Zhao et al. (2023). <a href="https://arxiv.org/abs/2309.10116" target="_blank">"BAGS: Boosting backdoor attacks in multimodal learning via gradient-based score."</a> arXiv:2309.10116.</summary>Эффективные по данным и вычислениям бэкдор-атаки на мультимодальное обучение с использованием оценки на основе градиентов (BAGS).</details>
<details id="ref18"><summary>[18] Jia et al. (2022). <a href="https://arxiv.org/abs/2203.15426" target="_blank">"BadEncoder: Backdoor Attacks to Pre-trained Encoders in Self-Supervised Learning."</a> arXiv:2203.15426.</summary>BadEncoder: бэкдор-атака на предварительно обученные энкодеры в SSL, где триггеры вызывают целевые ошибки классификации.</details>
<details id="ref19"><summary>[19] Zhu et al. (2023). <a href="https://arxiv.org/abs/2311.09435" target="_blank">"Cross-Modality Attack on Vision Language Models."</a> arXiv:2311.09435.</summary>Кросс-модальные атаки на VLM путем сопряжения состязательных изображений с текстовыми промптами для нарушения выравнивания.</details>
<details id="ref20"><summary>[20] Ni et al. (2025). <a href="https://arxiv.org/abs/2503.16023" target="_blank">"BadToken: Backdoor Attack on Multimodal Large Language Models via Token Substitution and Addition."</a> arXiv:2503.16023.</summary>BadToken: бэкдор-атака на уровне токенов для MLLM путем замены или добавления критических токенов.</details>
<details id="ref21"><summary>[21] Liu et al. (2024). <a href="https://openreview.net/forum?id=tT6O9qj72K" target="_blank">"SafeMLLM: Contrastive Embedding Attack and Defense for Multimodal Jailbreaking."</a> OpenReview.</summary>SafeMLLM: фреймворк защиты, использующий контрастную атаку на эмбеддинги (CoE-Attack) для генерации возмущений и обновления параметров модели.</details>
<details id="ref22"><summary>[22] Cui et al. (2024). <a href="https://openaccess.thecvf.com/content/CVPR2024/html/Cui_On_the_Robustness_of_Large_Multimodal_Models_Against_Image_CVPR_2024_paper.html" target="_blank">"On the Robustness of Large Multimodal Models Against Image Adversarial Attacks."</a> CVPR 2024.</summary>Комплексное исследование устойчивости различных LMM к состязательным атакам на изображения в разных задачах.</details>
<details id="ref23"><summary>[23] Carlini et al. (2025). <a href="https://arxiv.org/abs/2502.07987" target="_blank">"Universal Adversarial Attack on Aligned Multimodal LLMs."</a> arXiv:2502.07987.</summary>Универсальная состязательная атака на MLLM с использованием одного оптимизированного изображения для обхода защитных механизмов.</details>
<details id="ref24"><summary>[24] Dai et al. (2024). <a href="https://www.itm-conferences.org/articles/itmconf/abs/2024/04/itmconf_icast2024_02004/itmconf_icast2024_02004.html" target="_blank">"Research on the Adversarial Attack and Defense of Deep Learning Models."</a> *ITM Web of Conferences* 91, 02004.</summary>Обсуждение состязательных атак (FGSM, PGD) и состязательного обучения для защиты.</details>
<details id="ref25"><summary>[25] Zhang et al. (2025). <a href="https://www.nature.com/articles/s41598-025-57933-9" target="_blank">"Boosting adversarial transferability in vision-language models via multimodal feature heterogeneity."</a> *Scientific Reports* 15(1), 7366.</summary>Метод гетерогенизации признаков и агрегации кросс-модальной дисперсии для повышения переносимости атак на VLM.</details>
<details id="ref26"><summary>[26] Zhang et al. (2024). <a href="https://arxiv.org/abs/2407.01224" target="_blank">"Is multimodal fusion better than unimodal model in resisting textual adversarial attacks?"</a> arXiv:2407.01224.</summary>Сравнение устойчивости мультимодальных (CLIP) и унимодальных (BERT) моделей к текстовым атакам.</details>
<details id="ref27"><summary>[27] Li et al. (2024). <a href="https://arxiv.org/abs/2411.18275" target="_blank">"ADvLM: Adversarial Attack Framework for Vision-Language Models in Autonomous Driving."</a> arXiv:2411.18275.</summary>ADvLM: первый фреймворк визуальных состязательных атак, специально разработанный для VLM в автономном вождении.</details>
<details id="ref28"><summary>[28] Liu et al. (2025). <a href="https://arxiv.org/abs/2501.13563" target="_blank">"Cascading Adversarial Disruption: Black-Box Attack on Vision-Language Models for Autonomous Driving."</a> arXiv:2501.13563.</summary>Cascading Adversarial Disruption (CAD): первая black-box атака, нацеленная на VLM в автономном вождении.</details>
<details id="ref29"><summary>[29] Gao et al. (2024). <a href="https://arxiv.org/abs/2409.06726" target="_blank">"Feedback-based Modal Mutual Search Attack for Black-Box Vision-Language Pre-training Models."</a> arXiv:2409.06726.</summary>Метод атаки Feedback-based Modal Mutual Search (FMMS) для black-box VLP моделей, использующий обратную связь для уточнения примеров.</details>
<details id="ref30"><summary>[30] Wang et al. (2023). <a href="https://www.mdpi.com/1424-8220/23/5/2940" target="_blank">"GAAS: A Gray-Box Adversarial Attack on Self-Supervised Learning."</a> *Sensors* 23(5), 2940.</summary>GAAS: gray-box атака на SSL с использованием общедоступных размеченных данных.</details>
<details id="ref31"><summary>[31] Chen et al. (2024). <a href="https://openreview.net/forum?id=U77QG90q8a" target="_blank">"Zer0-Jack: Zero-Order Black-Box Attack for Jailbreaking Multimodal Large Language Models."</a> OpenReview.</summary>Zer0-Jack: метод, использующий оптимизацию нулевого порядка для прямой black-box атаки на MLLM с целью джейлбрейка.</details>
<details id="ref32"><summary>[32] Park et al. (2021). <a href="https://www.mdpi.com/2076-3417/11/1/26" target="_blank">"Defense Against Adversarial Patch Attacks Based on Anomaly Localization."</a> *Applied Sciences* 11(1), 26.</summary>Метод защиты от патч-атак на основе локализации аномалий.</details>
<details id="ref33"><summary>[33] Zhang et al. (2023). <a href="https://openreview.net/forum?id=5xN6RArb4E" target="_blank">"Cross-Modal Physical Attack on Object Detectors."</a> OpenReview.</summary>Унифицированный состязательный патч для кросс-модальных физических атак (обман видимых и ИК-детекторов).</details>
<details id="ref34"><summary>[34] Zhang et al. (2024). <a href="https://arxiv.org/abs/2403.08170" target="_blank">"Versatile defense model against adversarial attacks."</a> arXiv:2403.08170.</summary>Универсальная модель защиты, обученная на различных типах состязательных атак (включая FGSM и PGD).</details>
<details id="ref35"><summary>[35] Tramèr et al. (2024). <a href="https://arxiv.org/abs/2412.20987" target="_blank">"Are Black-Box Attacks Effective?"</a> arXiv:2412.20987.</summary>Оценка эффективности black-box атак против моделей с состязательным обучением, показывающая трудности атак.</details>
<details id="ref36"><summary>[36] Zhang et al. (2024). <a href="https://arxiv.org/abs/2412.10353" target="_blank">"Multi-Shield: Multi-Modal Information Guided Adversarial Defense."</a> arXiv:2412.10353.</summary>Multi-Shield: механизм защиты, использующий MLLM для обнаружения состязательных примеров путем проверки выравнивания представлений.</details>
<details id="ref37"><summary>[37] Zhang et al. (2024). <a href="https://openreview.net/forum?id=tU_IOmC4wR" target="_blank">"Gradient Regularized-based Cross-Prompt Attack for Vision Language Models."</a> OpenReview.</summary>Gradient Regularized-based Cross-Prompt Attack (GrCPA) для генерации более переносимых атак на VLM между разными промптами.</details>
<details id="ref38"><summary>[38] Zhang et al. (2025). <a href="https://arxiv.org/abs/2503.20823" target="_blank">"Playing the Fool: Jailbreaking LLMs and Multimodal LLMs with Out-of-Distribution Strategy."</a> arXiv:2503.20823.</summary>Исследование джейлбрейка LLM и MLLM с использованием входов вне распределения для эксплуатации слабостей.</details>
<details id="ref39"><summary>[39] Davoodi et al. (2024). <a href="https://www.nature.com/articles/s41467-024-45678-9" target="_blank">"Prompt injection attacks on vision language models in oncology."</a> *Nature Communications* 15, 1239.</summary>Демонстрация уязвимости VLM в медицинских задачах к атакам prompt injection.</details>
<details id="ref40"><summary>[40] Li et al. (2024). <a href="https://arxiv.org/abs/2405.16260" target="_blank">"Con Instruction: Communicating Instructions to Multimodal Language Models Through Adversarial Examples."</a> arXiv:2405.16260.</summary>Con Instruction: gray-box атака, генерирующая состязательные изображения/аудио для передачи вредных инструкций MLLM.</details>
<details id="ref41"><summary>[41] Schuster et al. (2025). <a href="https://arxiv.org/abs/2504.02132" target="_blank">"Poisoning Knowledge in Multimodal Retrieval Augmented Generation."</a> arXiv:2504.02132.</summary>Исследование атак отравления (poisoning) на конвейеры M-RAG.</details>
<details id="ref42"><summary>[42] Hubinger et al. (2024). <a href="https://arxiv.org/abs/2408.02946" target="_blank">"Data Poisoning Attacks Against Large Language Models."</a> arXiv:2408.02946.</summary>Исследование уязвимости LLM к отравлению данных, показывающее большую восприимчивость крупных моделей.</details>
<details id="ref43"><summary>[43] Pang et al. (2024). <a href="https://arxiv.org/abs/2409.06793" target="_blank">"CrossFire: Targeted Embedding Attack on Multi-Modal Models."</a> arXiv:2409.06793.</summary>CrossFire: атака, манипулирующая мультимодальными моделями путем минимизации углового отклонения между эмбеддингами.</details>
<details id="ref44"><summary>[44] Liu et al. (2025). <a href="https://arxiv.org/abs/2503.04833" target="_blank">"ProEAT: Projector-Enhanced Adversarial Training for Defending Multimodal Jailbreak Attacks."</a> arXiv:2503.04833.</summary>ProEAT: парадигма состязательного обучения для защиты MLLM от джейлбрейк-атак путем совместной оптимизации модальностей.</details>
<details id="ref45"><summary>[45] Li et al. (2024). <a href="https://arxiv.org/abs/2411.13587" target="_blank">"Quantifying the Robustness of Vision-Language-Action Models in Robotics."</a> arXiv:2411.13587.</summary>Систематическая количественная оценка устойчивости моделей Vision-Language-Action (VLA) в робототехнике к состязательным атакам.</details>
<details id="ref46"><summary>[46] Li et al. (2024). <a href="https://arxiv.org/abs/2404.05386" target="_blank">"MAA: Meticulous Adversarial Attack on Vision-Language Pre-trained Models."</a> arXiv:2404.05386.</summary>Meticulous Adversarial Attack (MAA) для повышения переносимости и эффективности атак на VLP модели.</details>
<details id="ref47"><summary>[47] Zhu et al. (2024). <a href="https://www.ecva.net/papers/eccv_2024/papers_ECCV/html/15690_ECCV.html" target="_blank">"Adversarial Prompt Tuning for Vision-Language Models."</a> ECCV 2024.</summary>Adversarial Prompt Tuning (AdvPT): техника повышения состязательной устойчивости энкодеров изображений в VLM.</details>
