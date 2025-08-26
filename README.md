<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>青少年防詐騙互動問答 (最終統計版)</title>
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
        .stat-bar-container {
            transition: all 0.2s ease-in-out;
            cursor: pointer;
        }
        .stat-bar-container:hover {
            background-color: #334155; /* slate-700 */
        }
        .bar {
            transition: width 0.3s ease-in-out;
        }
        .result-bar {
             background-color: #06b6d4; /* cyan-500 */
        }
        .result-bar.low {
            background-color: #ef4444; /* red-500 */
        }
        .result-bar.medium {
            background-color: #f59e0b; /* amber-500 */
        }
        .result-bar.high {
            background-color: #22c55e; /* green-500 */
        }
    </style>
</head>
<body class="bg-slate-900 flex items-center justify-center min-h-screen p-4">

    <div class="quiz-container w-full max-w-3xl mx-auto text-white p-6 sm:p-8 rounded-2xl shadow-2xl border border-slate-700">

        <!-- Start Screen -->
        <div id="start-screen" class="text-center">
            <h1 class="text-3xl sm:text-4xl font-black text-cyan-300 mb-4">防詐知識挑戰賽</h1>
            <p class="text-slate-300 mb-8">點擊下方按鈕，開始與現場聽眾進行一場即時的防詐騙知識問答！</p>
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
            <div id="stats-container" class="space-y-3 mb-6">
                <!-- Statistics bars will be generated here -->
            </div>
            <div id="controls-container" class="mt-6 flex flex-col sm:flex-row gap-4">
                 <button id="next-question-btn" class="w-full bg-cyan-500 hover:bg-cyan-600 font-bold py-3 px-6 rounded-lg">下一題</button>
            </div>
        </div>

        <!-- Result Screen -->
        <div id="result-screen" class="hidden">
            <h2 class="text-3xl font-black text-yellow-300 mb-6 text-center">測驗結果統計</h2>
            <div id="result-summary-container" class="space-y-4 max-h-96 overflow-y-auto pr-2">
                <!-- Result summary will be generated here -->
            </div>
            <button id="restart-btn" class="w-full mt-6 bg-yellow-500 hover:bg-yellow-600 text-slate-900 font-bold py-3 px-6 rounded-lg text-lg">
                重新開始
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
        const statsContainer = document.getElementById('stats-container');
        const resultSummaryContainer = document.getElementById('result-summary-container');

        let currentQuestionIndex = 0;
        let answerCounts = {};
        let totalVotes = 0;
        const allResults = [];

        startBtn.addEventListener('click', startQuiz);
        nextQuestionBtn.addEventListener('click', showNextQuestion);
        restartBtn.addEventListener('click', startQuiz);

        function startQuiz() {
            startScreen.classList.add('hidden');
            resultScreen.classList.add('hidden');
            quizScreen.classList.remove('hidden');
            currentQuestionIndex = 0;
            allResults.length = 0; // Clear previous results
            showQuestion();
        }

        function showQuestion() {
            resetState();
            const currentQuestion = quizData[currentQuestionIndex];
            questionCounter.textContent = `問題 ${currentQuestionIndex + 1} / ${quizData.length}`;
            questionText.textContent = currentQuestion.question;

            answerCounts = {};
            totalVotes = 0;

            currentQuestion.options.forEach(option => {
                answerCounts[option] = 0;
                const barContainer = document.createElement('div');
                barContainer.className = 'stat-bar-container p-3 rounded-lg border-2 border-slate-600';
                barContainer.dataset.option = option;
                
                barContainer.innerHTML = `
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-bold">${option}</span>
                        <span class="vote-count text-lg font-mono">0 票 (0%)</span>
                    </div>
                    <div class="w-full bg-slate-600 rounded-full h-4">
                        <div class="bar bg-cyan-500 h-4 rounded-full" style="width: 0%"></div>
                    </div>
                `;
                
                barContainer.addEventListener('click', () => {
                    answerCounts[option]++;
                    totalVotes++;
                    updateStats();
                });

                statsContainer.appendChild(barContainer);
            });
            
            if (currentQuestionIndex === quizData.length - 1) {
                nextQuestionBtn.textContent = "完成測驗並查看結果";
            } else {
                nextQuestionBtn.textContent = "下一題";
            }
        }
        
        function updateStats() {
            const bars = statsContainer.querySelectorAll('.stat-bar-container');
            bars.forEach(barEl => {
                const option = barEl.dataset.option;
                const count = answerCounts[option];
                const percentage = totalVotes > 0 ? (count / totalVotes) * 100 : 0;

                barEl.querySelector('.vote-count').textContent = `${count} 票 (${percentage.toFixed(0)}%)`;
                barEl.querySelector('.bar').style.width = `${percentage}%`;
            });
        }

        function resetState() {
            while (statsContainer.firstChild) {
                statsContainer.removeChild(statsContainer.firstChild);
            }
        }

        function saveAndProceed() {
            const currentQuestion = quizData[currentQuestionIndex];
            const correctVotes = answerCounts[currentQuestion.answer] || 0;
            const correctRatio = totalVotes > 0 ? (correctVotes / totalVotes) * 100 : 0;
            
            allResults.push({
                question: currentQuestion.question,
                correctRatio: correctRatio,
                explanation: currentQuestion.explanation
            });

            currentQuestionIndex++;
            if (currentQuestionIndex < quizData.length) {
                showQuestion();
            } else {
                showResults();
            }
        }

        function showNextQuestion() {
            saveAndProceed();
        }

        function showResults() {
            quizScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
            resultSummaryContainer.innerHTML = '';

            allResults.forEach((result, index) => {
                const resultEl = document.createElement('div');
                resultEl.className = 'bg-slate-700/50 p-4 rounded-lg';

                let barColorClass = 'result-bar';
                if (result.correctRatio < 50) {
                    barColorClass += ' low';
                } else if (result.correctRatio < 80) {
                    barColorClass += ' medium';
                } else {
                    barColorClass += ' high';
                }

                resultEl.innerHTML = `
                    <p class="font-bold text-sm mb-2">Q${index + 1}: ${result.question}</p>
                    <div class="flex justify-between items-center mb-1 text-sm">
                        <span class="text-slate-300">答對率</span>
                        <span class="font-bold ${barColorClass.split(' ')[1]}--text">${result.correctRatio.toFixed(0)}%</span>
                    </div>
                    <div class="w-full bg-slate-600 rounded-full h-3">
                        <div class="${barColorClass} h-3 rounded-full" style="width: ${result.correctRatio}%"></div>
                    </div>
                    <p class="text-xs text-slate-400 mt-2"><strong>解析：</strong>${result.explanation}</p>
                `;
                resultSummaryContainer.appendChild(resultEl);
            });
        }
    </script>
</body>
</html>
