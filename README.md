<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>青少年防詐騙互動問答 (純問答版)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
        }
        .quiz-container {
            background-color: #1e293b; /* slate-800 */
        }
        .option-btn {
            transition: all 0.2s ease-in-out;
        }
        .option-btn:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }
        .correct {
            background-color: #22c55e !important; /* green-500 */
            border-color: #16a34a !important; /* green-600 */
        }
        .incorrect {
            background-color: #ef4444 !important; /* red-500 */
            border-color: #dc2626 !important; /* red-600 */
        }
    </style>
</head>
<body class="bg-slate-900 flex items-center justify-center min-h-screen p-4">

    <div class="quiz-container w-full max-w-3xl mx-auto text-white p-6 sm:p-8 rounded-2xl shadow-2xl border border-slate-700">

        <!-- Start Screen -->
        <div id="start-screen" class="text-center">
            <h1 class="text-3xl sm:text-4xl font-black text-cyan-300 mb-4">防詐大作戰！</h1>
            <p class="text-slate-300 mb-8">點擊下方按鈕，開始與現場聽眾進行一場即時的防詐騙知識問答！</p>
            <button id="start-btn" class="w-full bg-cyan-500 hover:bg-cyan-600 text-white font-bold py-3 px-6 rounded-lg text-lg shadow-lg transition-transform transform hover:scale-105">
                開始挑戰
            </button>
        </div>

        <!-- Quiz Screen -->
        <div id="quiz-screen" class="hidden">
            <div class="mb-6">
                <p id="question-counter" class="text-sm text-slate-400">問題 1 / 10</p>
                <h2 id="question-text" class="text-xl sm:text-2xl font-bold mt-2">問題文字...</h2>
            </div>
            <div id="options-container" class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                <!-- Options will be generated here -->
            </div>
            <div id="feedback-container" class="hidden bg-slate-900/50 p-4 rounded-lg">
                <h3 class="font-bold text-lg mb-2" id="feedback-title"></h3>
                <p class="text-sm text-slate-300" id="feedback-text"></p>
            </div>
            <div id="controls-container" class="mt-6 flex flex-col sm:flex-row gap-4">
                 <button id="next-question-btn" class="hidden w-full bg-cyan-500 hover:bg-cyan-600 font-bold py-3 px-6 rounded-lg">下一題</button>
            </div>
        </div>

        <!-- Result Screen -->
        <div id="result-screen" class="hidden text-center">
            <h2 class="text-3xl font-black text-yellow-300 mb-4">測驗結束！</h2>
            <p class="text-lg text-slate-300 mb-8">感謝大家的參與！希望今天的活動能幫助各位建立更強的防詐免疫力。</p>
            <button id="restart-btn" class="w-full bg-yellow-500 hover:bg-yellow-600 text-slate-900 font-bold py-3 px-6 rounded-lg text-lg">
                重新開始
            </button>
        </div>

    </div>

    <script>
        const quizData = [
            // 30% 心理學概念
            {
                type: 'multiple-choice',
                question: '在網路上買演唱會門票，賣家說「很多人在問，要就快！」，這是利用了你的哪種心理？',
                options: ['權威', '喜好', '稀缺與急迫', '同情'],
                answer: '稀缺與急迫',
                explanation: '這是典型的「稀缺性」與「急迫性」話術，目的是壓縮你的思考時間，讓你因為害怕錯過（FOMO）而倉促付款。'
            },
            {
                type: 'true-false',
                question: '詐騙集團冒充警察或檢察官，是為了讓我們喜歡他，進而聽從指示。',
                options: ['O (是)', 'X (非)'],
                answer: 'X (非)',
                explanation: '這是利用「權威」原則，讓我們因為害怕而不敢質疑，並非「喜好」。'
            },
            {
                type: 'multiple-choice',
                question: '網路交友時，對方花好幾個月跟你培養感情，再用「家人生病」的理由借錢，這主要利用了哪種心理鉤子？',
                options: ['貪婪與希望', '喜好與同情', '社會認同', '稀缺性'],
                answer: '喜好與同情',
                explanation: '透過長期經營建立「喜好」，再用悲慘故事激發「同情」，是交友詐騙的核心手法。'
            },
            // 40% 應對行動與方法
            {
                type: 'true-false',
                question: '應徵打工時，公司為了確認你的信用，要求你先寄送個人存摺和提款卡，這是正常的流程。',
                options: ['O (是)', 'X (非)'],
                answer: 'X (非)',
                explanation: '絕對不是！任何要求交付實體存摺或提款卡的行為，都是「人頭帳戶」詐騙。'
            },
            {
                type: 'multiple-choice',
                question: '防詐黃金法則「暫停、查證、質疑」，不包含以下哪一項？',
                options: ['暫停思考', '查證來源', '質疑合理性', '與對方辯論'],
                answer: '與對方辯論',
                explanation: '遇到疑似詐騙時，我們要做的是保護自己並向外求證，而不是與詐騙方直接衝突或辯論。'
            },
            {
                type: 'true-false',
                question: '「SAFE」行動計畫中的「S」代表「Share」(分享)，意思是看到可疑訊息要趕快分享給朋友。',
                options: ['O (是)', 'X (非)'],
                answer: 'X (非)',
                explanation: '「S」代表的是「Stop & Slow Down」(停止與放慢)，在做任何決定前先為自己創造緩衝時間。'
            },
            {
                type: 'multiple-choice',
                question: '如果不幸被騙了，最重要且正確的第一步是什麼？',
                options: ['自認倒楣，當作教訓', '自己上網找駭客幫忙追回', '保持冷靜，告訴師長並打165', '責怪自己太笨'],
                answer: '保持冷靜，告訴師長並打165',
                explanation: '被騙不是你的錯！勇敢求助，保留證據並報警，才是阻止傷害擴大的唯一方法。'
            },
             // 30% 隨機問題
            {
                type: 'true-false',
                question: '根據簡報統計，目前臺灣少年最主要的涉案類型是「暴力犯罪」。',
                options: ['O (是)', 'X (非)'],
                answer: 'X (非)',
                explanation: '不是喔！根據警政署統計，少年涉案類型第一名是「詐欺」。'
            },
            {
                type: 'multiple-choice',
                question: '下列哪一個是簡報中提到的官方求助資源？',
                options: ['臉書爆料公社', 'Dcard 感情版', '165 反詐騙專線', '私家偵探社'],
                answer: '165 反詐騙專線',
                explanation: '165是警政署設立的官方反詐騙諮詢專線，提供最即時、最正確的協助。'
            },
            {
                type: 'true-false',
                question: '只幫忙領錢的「取款車手」，因為沒有直接騙人，所以法律責任很輕。',
                options: ['O (是)', 'X (非)'],
                answer: 'X (非)',
                explanation: '大錯特錯！車手是詐欺的共犯，會面臨詐欺、洗錢、組織犯罪等多重刑責，後果非常嚴重。'
            }
        ];

        const startScreen = document.getElementById('start-screen');
        const quizScreen = document.getElementById('quiz-screen');
        const resultScreen = document.getElementById('result-screen');

        const startBtn = document.getElementById('start-btn');
        const nextQuestionBtn = document.getElementById('next-question-btn');
        const restartBtn = document.getElementById('restart-btn');

        const questionCounter = document.getElementById('question-counter');
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const feedbackContainer = document.getElementById('feedback-container');
        const feedbackTitle = document.getElementById('feedback-title');
        const feedbackText = document.getElementById('feedback-text');

        let currentQuestionIndex = 0;

        startBtn.addEventListener('click', startQuiz);
        nextQuestionBtn.addEventListener('click', showNextQuestion);
        restartBtn.addEventListener('click', startQuiz);

        function startQuiz() {
            startScreen.classList.add('hidden');
            resultScreen.classList.add('hidden');
            quizScreen.classList.remove('hidden');
            currentQuestionIndex = 0;
            showQuestion();
        }

        function showQuestion() {
            resetState();
            const currentQuestion = quizData[currentQuestionIndex];
            questionCounter.textContent = `問題 ${currentQuestionIndex + 1} / ${quizData.length}`;
            questionText.textContent = currentQuestion.question;

            currentQuestion.options.forEach(option => {
                const button = document.createElement('button');
                button.innerText = option;
                button.classList.add('option-btn', 'w-full', 'p-4', 'rounded-lg', 'border-2', 'border-slate-600', 'bg-slate-700', 'hover:bg-slate-600', 'text-left');
                button.addEventListener('click', selectAnswer);
                optionsContainer.appendChild(button);
            });
        }

        function resetState() {
            nextQuestionBtn.classList.add('hidden');
            feedbackContainer.classList.add('hidden');
            while (optionsContainer.firstChild) {
                optionsContainer.removeChild(optionsContainer.firstChild);
            }
        }

        function selectAnswer(e) {
            const selectedBtn = e.target;
            const currentQuestion = quizData[currentQuestionIndex];
            const isCorrect = selectedBtn.innerText === currentQuestion.answer;

            const optionButtons = optionsContainer.querySelectorAll('.option-btn');
            optionButtons.forEach(button => {
                button.disabled = true; // Disable all buttons
                if (button.innerText === currentQuestion.answer) {
                    button.classList.add('correct');
                } else if (button === selectedBtn) {
                    button.classList.add('incorrect');
                }
            });

            if (isCorrect) {
                feedbackTitle.textContent = '答對了！';
                feedbackTitle.style.color = '#22c55e'; // green-500
            } else {
                feedbackTitle.textContent = '答錯了...';
                feedbackTitle.style.color = '#ef4444'; // red-500
            }
            
            feedbackText.textContent = currentQuestion.explanation;
            feedbackContainer.classList.remove('hidden');

            nextQuestionBtn.classList.remove('hidden');
            if (currentQuestionIndex === quizData.length - 1) {
                nextQuestionBtn.textContent = "結束測驗";
            } else {
                nextQuestionBtn.textContent = "下一題";
            }
        }

        function showNextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < quizData.length) {
                showQuestion();
            } else {
                showResults();
            }
        }

        function showResults() {
            quizScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
        }
    </script>
</body>
</html>
