# coaching_test
Пройди короткий квіз, щоб зрозуміти, що саме зараз потребує твоєї уваги — енергія, фокус, напрям чи відновлення. Всього 5 запитань допоможуть визначити твій поточний стан і отримати персональну рекомендацію, як діяти далі — м’яко, усвідомлено і в своїй силі.
<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Коучинговий Квіз: Де ти застряг(ла)?</title>
    <!-- Підключення Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Використання шрифту Inter для професійного вигляду -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap" rel="stylesheet">
    <style>
        /* Налаштування шрифту та загального стилю */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fc; /* Світлий, чистий фон */
        }
        .quiz-card {
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 0 10px -5px rgba(0, 0, 0, 0.04);
        }
        /* Стиль для варіантів відповідей */
        .option-button {
            transition: all 0.2s ease;
            cursor: pointer;
            @apply block w-full text-left p-4 mb-3 border-2 border-gray-200 rounded-xl bg-white hover:bg-indigo-50 hover:border-indigo-400 text-gray-700 font-medium;
        }
        .option-button:hover {
            transform: translateY(-2px);
        }
        .option-button.selected {
            @apply bg-indigo-100 border-indigo-600 text-indigo-800 shadow-md;
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4 sm:p-6">

    <div id="quiz-container" class="w-full max-w-lg mx-auto quiz-card bg-white rounded-2xl p-6 sm:p-10">

        <!-- Заголовок -->
        <h1 class="text-3xl font-bold text-gray-900 mb-2 text-center">
            💭 Коучинговий Квіз
        </h1>
        <p class="text-lg text-gray-500 mb-8 text-center">
            Дізнайся, що тебе зараз найбільше тримає
        </p>

        <!-- Вікно Квізу -->
        <div id="quiz-window">
            <!-- Тут буде рендеринг питань або результатів -->
        </div>

    </div>

    <script>
        // --- ДАНІ КВІЗУ (УКРАЇНСЬКОЮ МОВОЮ) ---

        // 0: Туман (Ясність), 1: Виснаження (Енергія), 2: Розпорошення (Фокус), 3: Втрата сенсу (Сенс)
        const quizData = [
            {
                q: "1. Як би ти описав(ла) свій поточний стан?",
                options: [
                    { text: "Відчуваю, що стою на місці, не бачу, куди рухатись.", resultIndex: 0 },
                    { text: "Постійно втома, навіть прості справи виснажують.", resultIndex: 1 },
                    { text: "Є багато ідей, але я розпорошуюсь і нічого не доводжу до кінця.", resultIndex: 2 },
                    { text: "Все роблю, але не відчуваю внутрішньої радості чи сенсу.", resultIndex: 3 },
                ]
            },
            {
                q: "2. Коли думаєш про наступний великий крок...",
                options: [
                    { text: "Не розумію, з чого почати і який крок буде правильним.", resultIndex: 0 },
                    { text: "Знаю, що треба, але бракує сил навіть на невеликі дії.", resultIndex: 1 },
                    { text: "Я вже почав(ла) багато проєктів, але жоден не має пріоритету.", resultIndex: 2 },
                    { text: "Я виконую завдання, але внутрішній голос питає: 'Навіщо?'", resultIndex: 3 },
                ]
            },
            {
                q: "3. Яке твоє ставлення до планування часу?",
                options: [
                    { text: "Намагаюся планувати, але плани швидко плутаються і втрачають актуальність.", resultIndex: 0 },
                    { text: "Планування викликає стрес, бо не впевнений(а), що матиму сили це виконати.", resultIndex: 1 },
                    { text: "Я постійно змінюю пріоритети, реагуючи на терміновість, а не на важливість.", resultIndex: 2 },
                    { text: "Планую, але відчуваю, що це 'рутина заради рутини', а не шлях до мети.", resultIndex: 3 },
                ]
            },
            {
                q: "4. Як ти реагуєш на нову інформацію/можливості?",
                options: [
                    { text: "Складно її обробляти, бо голова вже переповнена невизначеністю.", resultIndex: 0 },
                    { text: "Відчуваю, що не можу взяти на себе більше зобов'язань, навіть перспективних.", resultIndex: 1 },
                    { text: "Одразу хапаюся за нову ідею, додаючи її до купи незавершених справ.", resultIndex: 2 },
                    { text: "Цікаво, але внутрішній критик каже, що це не має довгострокового сенсу.", resultIndex: 3 },
                ]
            },
            {
                q: "5. Що зараз найважче дається?",
                options: [
                    { text: "Прийняття рішення, що є головним.", resultIndex: 0 },
                    { text: "Фізична та емоційна присутність у моменті.", resultIndex: 1 },
                    { text: "Виконання одного завдання до його повного завершення.", resultIndex: 2 },
                    { text: "Згадка, чому я взагалі почав(ла) цей шлях.", resultIndex: 3 },
                ]
            },
        ];

        const resultsData = [
            // 0: Туман у голові
            {
                title: "Туман у голові 🌫️",
                symptom: "Бракує Ясності та Напрямку",
                recommendation: "Повернись до питання ‘що для мене зараз справді важливо?’ — іноді напрям відкривається не через дії, а через чесність із собою.",
                color: "bg-indigo-500",
                icon: "💡"
            },
            // 1: Виснаження
            {
                title: "Виснаження 🔋",
                symptom: "Ресурс на Мінімумі",
                recommendation: "Схоже, тіло просить паузи. Почни з малого: віднови контакт із тілом, сном, повітрям. Без ресурсу — ніякі плани не оживають.",
                color: "bg-red-500",
                icon: "⚡"
            },
            // 2: Розпорошення
            {
                title: "Розпорошення 🎯",
                symptom: "Є Потенціал, але бракує Фокусу",
                recommendation: "Оберіть 1 головний напрямок на тиждень. Все інше тимчасово постав ‘на паузу’. Принцип 'менше, але якісніше' — ваш ключ.",
                color: "bg-teal-500",
                icon: "🧭"
            },
            // 3: Втрата сенсу
            {
                title: "Втрата сенсу ❓",
                symptom: "Зовнішня Активність без Внутрішньої Мотивації",
                recommendation: "Повернись до питання: ‘що мені дає відчуття живості?’ — це ключ до твого сенсу. Час переглянути, чи твої дії відповідають твоїм цінностям.",
                color: "bg-yellow-500",
                icon: "💬"
            },
        ];


        // --- ЛОГІКА КВІЗУ ---

        let currentQuestionIndex = 0;
        // Масив для підрахунку балів: [Туман, Виснаження, Розпорошення, Втрата сенсу]
        let score = [0, 0, 0, 0];

        const quizWindow = document.getElementById('quiz-window');

        /**
         * Відображає поточне питання
         */
        function renderQuestion() {
            if (currentQuestionIndex >= quizData.length) {
                renderResult();
                return;
            }

            const questionData = quizData[currentQuestionIndex];

            const optionsHtml = questionData.options.map((option, index) => `
                <button
                    class="option-button"
                    data-result-index="${option.resultIndex}"
                    onclick="handleAnswer(this, ${option.resultIndex})"
                >
                    ${option.text}
                </button>
            `).join('');

            const progress = (currentQuestionIndex / quizData.length) * 100;

            quizWindow.innerHTML = `
                <div class="mb-6">
                    <div class="text-sm font-medium text-indigo-600 mb-2">Питання ${currentQuestionIndex + 1} з ${quizData.length}</div>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div class="bg-indigo-600 h-2.5 rounded-full" style="width: ${progress}%;"></div>
                    </div>
                </div>
                <h2 class="text-xl font-semibold text-gray-800 mb-6">
                    ${questionData.q}
                </h2>
                <div id="options-container">
                    ${optionsHtml}
                </div>
            `;
        }

        /**
         * Обробляє вибір відповіді
         * @param {HTMLElement} button - Натиснута кнопка
         * @param {number} resultIndex - Індекс стану, за який нараховується бал
         */
        function handleAnswer(button, resultIndex) {
            // Запобігаємо повторним клікам на цьому питанні
            document.querySelectorAll('.option-button').forEach(btn => {
                btn.disabled = true;
                btn.classList.remove('selected');
            });
            
            // Виділяємо обрану відповідь
            button.classList.add('selected');
            
            // Нараховуємо бал
            score[resultIndex]++;

            // Перехід до наступного питання через невелику затримку для візуалізації
            setTimeout(() => {
                currentQuestionIndex++;
                renderQuestion();
            }, 300);
        }

        /**
         * Обчислює результат і відображає фінальну сторінку
         */
        function renderResult() {
            // Знаходимо індекс найбільшого балу
            let maxScore = -1;
            let resultIndex = 0;

            for (let i = 0; i < score.length; i++) {
                if (score[i] > maxScore) {
                    maxScore = score[i];
                    resultIndex = i;
                }
            }

            const result = resultsData[resultIndex];

            quizWindow.innerHTML = `
                <!-- Результат -->
                <div class="text-center">
                    <div class="text-4xl mb-4">${result.icon}</div>
                    <h2 class="text-3xl font-bold text-gray-900 mb-6">
                        Ваш стан: <span class="text-indigo-600">${result.title}</span>
                    </h2>
                    
                    <div class="p-6 rounded-xl ${result.color} text-white mb-8">
                        <p class="text-lg font-semibold mb-2">${result.symptom}</p>
                        <p class="text-base leading-relaxed">
                            ${result.recommendation}
                        </p>
                    </div>

                    <!-- Фінал гри та CTA -->
                    <div class="mt-8 pt-6 border-t border-gray-100">
                        <p class="text-xl text-gray-700 font-medium mb-6">
                            Твоя система зараз підказує, що час звернути увагу на **${result.symptom.toLowerCase()}**.
                        </p>
                        <button 
                            onclick="alert('Дякуємо! Це імітація кнопки. У реальному блозі тут має бути ваше посилання на запис/консультацію.')"
                            class="w-full sm:w-auto px-8 py-3 bg-indigo-600 text-white font-bold rounded-xl text-lg hover:bg-indigo-700 transition duration-300 transform hover:scale-[1.02] shadow-lg shadow-indigo-200"
                        >
                            💬 Хочу розібратись глибше — Записатись на сесію
                        </button>
                    </div>

                    <button 
                        onclick="resetQuiz()"
                        class="mt-4 text-sm text-gray-500 hover:text-indigo-600 transition"
                    >
                        Почати квіз знову
                    </button>
                </div>
            `;
        }
        
        /**
         * Скидає стан квізу
         */
        function resetQuiz() {
            currentQuestionIndex = 0;
            score = [0, 0, 0, 0];
            renderQuestion();
        }

        // Ініціалізація квізу при завантаженні сторінки
        document.addEventListener('DOMContentLoaded', renderQuestion);

    </script>
</body>
</html>

