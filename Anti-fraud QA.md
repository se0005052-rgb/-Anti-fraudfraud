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
        .option-btn:not(:disabled):hover {
            transform: scale(1.03);
            background-color: #0369a1; /* sky-700 */
        }
        .correct {
            background-color: #16a34a !important; /* green-600 */
            border-color: #22c55e !important; /* green-500 */
            transform: scale(1.05);
        }
        .incorrect {
            background-color: #dc2626 !important; /* red-600 */
            border-color: #ef4444 !important; /* red-500 */
            opacity: 0.7;
        }
        .selected-incorrect {
             background-color: #dc2626 !important; /* red-600 */
            border-color: #ef4444 !important; /* red-500 */
            opacity: 1;
            transform: scale(1.05);
        }
    </style>
</head>
<body class="bg-slate-900 flex items-center justify-center min-h-screen p-4">

    <div class="quiz-container w-full max-w-2xl mx-auto text-white p-6 sm:p-8 rounded-2xl shadow-2xl border border-slate-700">

        <!-- Start Screen -->
        <div id="start-screen" class="text-center">
            <h1 class="text-3xl sm:text-4xl font-black text-cyan-300 mb-4">防詐知識挑戰賽</h1>
            <p class="text-slate-300 mb-8">點擊下方按鈕，開始一場即時的防詐騙知識問答！</p>
            <button id="start-btn" class="w-full bg-cyan-500 hover:bg-cyan-600 text-white font-bold py-3 px-6 rounded-lg text-lg shadow-lg transition-transform transform hover:scale-105">
                開始測驗
            </button>
        </div>

        <!-- Quiz Screen -->
        <div id="quiz-screen" class="hidden">
            <div class="mb-6">
                <p id="question-counter" class="text-sm text-slate-400">問題 1 / 10</p>
                <h2 id="question-text" class="text-xl sm:text-2xl font-bold mt-2">問題文字...</h2>
            </div>
            <div id="options-container" class="space-y-4 mb-6">
                <!-- Option buttons will be generated here -->
            </div>
            <div id="explanation-container" class="hidden mt-6 p-4 bg-slate-700/50 rounded-lg border border-slate-600">
                <h3 class="font-bold text-amber-300 mb-2">觀念解析</h3>
                <p id="explanation-text" class="text-slate-300"></p>
            </div>
            <div id="controls-container" class="mt-6 flex flex-col sm:flex-row gap-4">
                 <button id="next-question-btn" class="hidden w-full bg-cyan-500 hover:bg-cyan-600 font-bold py-3 px-6 rounded-lg">下一題</button>
            </div>
        </div>
        
        <!-- Result Screen -->
        <div id="result-screen" class="hidden text-center">
            <h2 class="text-3xl font-black text-yellow-300 mb-4">測驗完成！</h2>
            <p class="text-slate-300 mb-8">做得很好！你已經掌握了重要的防詐知識，記得在生活中應用它們！</p>
             <div class="text-6xl mb-8">🏆</div>
            <button id="restart-btn" class="w-full mt-6 bg-yellow-500 hover:bg-yellow-600 text-slate-900 font-bold py-3 px-6 rounded-lg text-lg">
                重新挑戰一次
            </button>
        </div>

    </div>

    <script>
        const quizData = [
            { type: 'true-false', question: '根據統計，「詐欺」已經超越其他犯罪，成為臺灣少年最主要的涉案類型。', options: ['O (是)', 'X (非)'], answer: 'O (是)', explanation: '沒錯！簡報數據顯示，無論男女，詐欺都是少年最主要的涉案類型，這凸顯了防詐教育的重要性。' },
            { type: 'multiple-choice', question: '簡報中提到，青少年雖然是「數位原住民」，但大腦的哪個部分尚未發育完全，導致容易衝動決策？', options: ['杏仁核', '海馬迴', '前額葉皮質', '小腦'], answer: '前額葉皮質', explanation: '「前額葉皮質」負責理性思考與風險評估，在青少年時期仍在發展中，因此更容易受到情緒影響而做出高風險決定。' },
            { type: 'multiple-choice', question: '在網路上買演唱會門票，賣家說「很多人在問，要就快！」，這是利用了你的哪種心理？', options: ['權威', '喜好', '稀缺與急迫', '同情'], answer: '稀缺與急迫', explanation: '這是典型的「稀缺性」與「急迫性」話術，目的是壓縮你的思考時間，讓你因為害怕錯過（FOMO）而倉促付款。' },
            { type: 'true-false', question: '應徵暑期打工時，公司要求先寄送個人存摺和提款卡來辦理「薪資轉入」，這是正常的流程。', options: ['O (是)', 'X (非)'], answer: 'X (非)', explanation: '絕對不是！任何正常的公司都不會要求你寄送實體存摺或提款卡。這是典型的「人頭帳戶」詐騙手法。' },
            { type: 'multiple-choice', question: '在遊戲寶物交易中，對方提出用「遊戲點數」代替現金交易，你應該怎麼做？', options: ['答應他，因為點數很方便', '拒絕，並堅持使用有保障的官方交易平台', '先給他一半點數表示誠意', '請他先登入我的帳號驗貨'], answer: '拒絕，並堅持使用有保障的官方交易平台', explanation: '遊戲點數一旦給出序號就難以追回，是詐騙集團最愛用的工具。堅持使用官方或有信譽的第三方平台才是最安全的。' },
            { type: 'true-false', question: '網路上認識的朋友，如果聊得很投機，在見面前為了「證明你不是警察」，要求你買點數當保證金是合理的。', options: ['O (是)', 'X (非)'], answer: 'X (非)', explanation: '這是100%的詐騙！「買點數驗證身份」是交友詐騙的經典台詞，任何在見面前就談錢的網友，都極有可能是詐騙。' },
            { type: 'multiple-choice', question: '簡報提到的防詐黃金法則不包含以下哪一項？', options: ['暫停', '查證', '質疑', '反擊'], answer: '反擊', explanation: '黃金法則是「暫停、查證、質疑」。遇到疑似詐騙時，我們要做的是保護自己並尋求協助，而不是直接與對方衝突或反擊。' },
            { type: 'true-false', question: '如果不幸被騙了，應該覺得很丟臉，最好不要告訴任何人，自己想辦法解決。', options: ['O (是)', 'X (非)'], answer: 'X (非)', explanation: '被騙不是你的錯！詐騙集團利用的是專業的心理學技巧。感到羞恥是正常的，但一定要「消除羞恥感」，勇敢告訴家人、老師並報警，才能及時止損並獲得幫助。' },
            { type: 'multiple-choice', question: '詐騙集團冒充檢察官，打電話說你的帳戶涉及洗錢案，這是利用了影響力武器中的哪一種？', options: ['社會認同', '權威', '喜好', '貪婪'], answer: '權威', explanation: '冒充檢警、法官、政府官員等具有公權力的身份，是為了建立「權威感」，讓你因為害怕而不敢質疑，進而服從指令。' },
            { type: 'true-false', question: '「SAFE」行動計畫中的「F」代表「Fortify Your Data」，意思是鞏固你的個人資料，例如不隨意交付存摺或提款卡。', options: ['O (是)', 'X (非)'], answer: 'O (是)', explanation: '完全正確！保護好自己的個人資料，就是建立防詐防火牆最重要的一步。' }
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
        const explanationContainer = document.getElementById('explanation-container');
        const explanationText = document.getElementById('explanation-text');
        
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
                button.textContent = option;
                button.className = 'option-btn w-full bg-sky-600 text-white font-bold p-3 rounded-lg border-2 border-transparent';
                button.addEventListener('click', () => selectAnswer(button, option));
                optionsContainer.appendChild(button);
            });
        }

        function selectAnswer(selectedButton, selectedOption) {
            const currentQuestion = quizData[currentQuestionIndex];
            const isCorrect = selectedOption === currentQuestion.answer;
            
            Array.from(optionsContainer.children).forEach(button => {
                button.disabled = true;
                const optionText = button.textContent;

                if (optionText === currentQuestion.answer) {
                    button.classList.add('correct');
                    button.innerHTML += ' <span class="font-bold">✓</span>';
                } else {
                    button.classList.add('incorrect');
                }
            });
            
            if (!isCorrect) {
                selectedButton.classList.remove('incorrect');
                selectedButton.classList.add('selected-incorrect');
                selectedButton.innerHTML += ' <span class="font-bold">✗</span>';
            }

            explanationContainer.classList.remove('hidden');
            explanationText.textContent = currentQuestion.explanation;
            
            if (currentQuestionIndex < quizData.length - 1) {
                nextQuestionBtn.textContent = '下一題';
            } else {
                nextQuestionBtn.textContent = '查看結果';
            }
            nextQuestionBtn.classList.remove('hidden');
        }

        function showNextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < quizData.length) {
                showQuestion();
            } else {
                showResultScreen();
            }
        }
        
        function showResultScreen(){
            quizScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
        }

        function resetState() {
            nextQuestionBtn.classList.add('hidden');
            explanationContainer.classList.add('hidden');
            while(optionsContainer.firstChild) {
                optionsContainer.removeChild(optionsContainer.firstChild);
            }
        }

    </script>
</body>
</html>

