<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>筋トレクイズ</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;600;700&display=swap');
        body { font-family: 'Noto Sans JP', sans-serif; }
        .quiz-card {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
        }
        .option-btn {
            transition: all 0.3s ease;
            transform: translateY(0);
        }
        .option-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.15);
        }
        .correct {
            background: linear-gradient(135deg, #4ade80, #22c55e);
            animation: pulse 0.5s ease-in-out;
        }
        .incorrect {
            background: linear-gradient(135deg, #f87171, #ef4444);
            animation: shake 0.5s ease-in-out;
        }
        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-5px); }
            75% { transform: translateX(5px); }
        }
        .progress-bar {
            transition: width 0.5s ease-in-out;
        }
    </style>
</head>
<body class="min-h-screen bg-gradient-to-br from-purple-50 to-blue-50">
    <div class="container mx-auto px-4 py-8 max-w-4xl">
        <!-- ヘッダー -->
        <div class="text-center mb-8">
            <h1 class="text-4xl font-bold text-gray-800 mb-2">💪 筋トレクイズ</h1>
            <p class="text-gray-600">筋トレの知識をテストしよう！</p>
        </div>

        <!-- プログレスバー -->
        <div class="mb-8">
            <div class="flex justify-between items-center mb-2">
                <span class="text-sm font-medium text-gray-700">進捗</span>
                <span class="text-sm font-medium text-gray-700" id="progress-text">1 / 10</span>
            </div>
            <div class="w-full bg-gray-200 rounded-full h-3">
                <div class="progress-bar bg-gradient-to-r from-purple-500 to-blue-500 h-3 rounded-full" id="progress-bar" style="width: 10%"></div>
            </div>
        </div>

        <!-- クイズカード -->
        <div class="quiz-card rounded-2xl p-8 text-white mb-8" id="quiz-card">
            <div class="text-center mb-6">
                <div class="text-6xl mb-4" id="question-emoji">🤔</div>
                <h2 class="text-2xl font-bold mb-4" id="question-text">問題を読み込み中...</h2>
            </div>

            <div class="space-y-4" id="options-container">
                <!-- オプションがここに動的に追加されます -->
            </div>
        </div>

        <!-- 結果表示エリア -->
        <div class="hidden bg-white rounded-2xl p-8 shadow-lg" id="result-card">
            <div class="text-center">
                <div class="text-6xl mb-4" id="result-emoji">🎉</div>
                <h3 class="text-2xl font-bold text-gray-800 mb-2" id="result-title">お疲れ様でした！</h3>
                <p class="text-gray-600 mb-6" id="result-text">あなたのスコア: 0/10</p>
                <button onclick="restartQuiz()" class="bg-gradient-to-r from-purple-500 to-blue-500 text-white px-8 py-3 rounded-full font-semibold hover:from-purple-600 hover:to-blue-600 transition-all duration-300 transform hover:scale-105">
                    もう一度挑戦
                </button>
            </div>
        </div>

        <!-- 解説エリア -->
        <div class="hidden bg-white rounded-2xl p-6 shadow-lg mb-6" id="explanation-card">
            <div class="flex items-start space-x-4">
                <div class="text-3xl" id="explanation-emoji">💡</div>
                <div>
                    <h4 class="font-bold text-gray-800 mb-2">解説</h4>
                    <p class="text-gray-600" id="explanation-text"></p>
                </div>
            </div>
            <button onclick="nextQuestion()" class="mt-4 bg-gradient-to-r from-green-500 to-blue-500 text-white px-6 py-2 rounded-full font-semibold hover:from-green-600 hover:to-blue-600 transition-all duration-300">
                次の問題へ
            </button>
        </div>
    </div>

    <script>
        const quizData = [
            {
                question: "筋肉をつけるために一番大事な栄養素は？",
                options: ["炭水化物", "タンパク質", "脂質"],
                correct: 1,
                explanation: "筋肉は主にタンパク質から構成されており、筋肥大にはタンパク質の摂取が不可欠です。",
                emoji: "🥩"
            },
            {
                question: "筋トレ初心者がまず意識するべき回数は？",
                options: ["毎日やる", "週に2〜3回", "月に1回集中してやる"],
                correct: 1,
                explanation: "週2〜3回のトレーニングが筋肉の回復と成長に適しており、初心者にも続けやすい頻度です。",
                emoji: "📅"
            },
            {
                question: "筋肉を育てるのに欠かせないのは？",
                options: ["毎日の追い込み", "栄養と休息", "サプリメント"],
                correct: 1,
                explanation: "筋肉はトレーニング後の栄養と休息で回復・成長するため、この2つが非常に重要です。",
                emoji: "😴"
            },
            {
                question: "たんぱく質を筋トレ後に摂ると効果的なタイミングは？",
                options: ["トレーニング後30分以内", "寝る前", "トレーニング前"],
                correct: 0,
                explanation: "トレーニング後30分以内は『ゴールデンタイム』と呼ばれ、栄養吸収が高まりやすい時間帯です。",
                emoji: "⏰"
            },
            {
                question: "筋肉を増やす際に、1日何gのタンパク質摂取が推奨されるか？（体重1kgあたり）",
                options: ["0.8〜1.0g", "1.2〜1.5g", "1.6〜2.2g"],
                correct: 2,
                explanation: "通常は1.0〜1.5gですが、筋肉を増やす際には数回に分けて1.6〜2.2g摂取することをお勧めします。",
                emoji: "⚖️"
            },
            {
                question: "「筋肉痛がある＝筋トレが効果的」というのは？",
                options: ["正しい", "間違い", "わからない"],
                correct: 1,
                explanation: "筋肉痛がなくても筋肉は成長します。痛みの有無よりも内容や継続が大切です。",
                emoji: "🤕"
            },
            {
                question: "筋肉はどのタイミングで成長する？",
                options: ["トレーニング中", "回復中", "有酸素運動中"],
                correct: 1,
                explanation: "筋トレによる破壊後の回復過程で筋肉は強く大きくなります。",
                emoji: "🔄"
            },
            {
                question: "同じ部位の筋トレは何日あけるのが理想？",
                options: ["毎日", "1日おき（48〜72時間）", "1週間後"],
                correct: 1,
                explanation: "筋肉が回復・成長するには最低でも48〜72時間の休息が必要です。",
                emoji: "⏳"
            },
            {
                question: "食事を抜くと筋肉は？",
                options: ["減りやすくなる", "増えやすくなる", "関係ない"],
                correct: 0,
                explanation: "エネルギー不足により筋肉の分解が進みやすくなります。",
                emoji: "🍽️"
            },
            {
                question: "食事で筋肉を育てるとき、最も避けたい行動は？",
                options: ["朝ごはんを抜く", "夜にたくさん食べる", "野菜を多く摂る"],
                correct: 0,
                explanation: "朝食を抜くと代謝が下がり、筋肉の分解も進みやすくなります。",
                emoji: "🌅"
            }
        ];

        let currentQuestion = 0;
        let score = 0;
        let answered = false;

        function loadQuestion() {
            const question = quizData[currentQuestion];
            document.getElementById('question-emoji').textContent = question.emoji;
            document.getElementById('question-text').textContent = question.question;
            document.getElementById('progress-text').textContent = `${currentQuestion + 1} / ${quizData.length}`;
            document.getElementById('progress-bar').style.width = `${((currentQuestion + 1) / quizData.length) * 100}%`;

            const optionsContainer = document.getElementById('options-container');
            optionsContainer.innerHTML = '';

            question.options.forEach((option, index) => {
                const button = document.createElement('button');
                button.className = 'option-btn w-full bg-white bg-opacity-20 hover:bg-opacity-30 text-white p-4 rounded-xl text-left font-medium backdrop-blur-sm border border-white border-opacity-20';
                button.innerHTML = `<span class="font-bold">${String.fromCharCode(65 + index)}.</span> ${option}`;
                button.onclick = () => selectAnswer(index);
                optionsContainer.appendChild(button);
            });

            answered = false;
            document.getElementById('explanation-card').classList.add('hidden');
        }

        function selectAnswer(selectedIndex) {
            if (answered) return;
            answered = true;

            const question = quizData[currentQuestion];
            const buttons = document.querySelectorAll('.option-btn');
            
            buttons.forEach((button, index) => {
                if (index === question.correct) {
                    button.classList.add('correct');
                } else if (index === selectedIndex && index !== question.correct) {
                    button.classList.add('incorrect');
                }
                button.style.pointerEvents = 'none';
            });

            if (selectedIndex === question.correct) {
                score++;
                document.getElementById('explanation-emoji').textContent = '✅';
            } else {
                document.getElementById('explanation-emoji').textContent = '❌';
            }

            document.getElementById('explanation-text').textContent = question.explanation;
            document.getElementById('explanation-card').classList.remove('hidden');
        }

        function nextQuestion() {
            currentQuestion++;
            if (currentQuestion < quizData.length) {
                loadQuestion();
            } else {
                showResults();
            }
        }

        function showResults() {
            document.getElementById('quiz-card').classList.add('hidden');
            document.getElementById('explanation-card').classList.add('hidden');
            
            const resultCard = document.getElementById('result-card');
            const resultEmoji = document.getElementById('result-emoji');
            const resultTitle = document.getElementById('result-title');
            const resultText = document.getElementById('result-text');

            let emoji, title;
            const percentage = (score / quizData.length) * 100;

            if (percentage >= 80) {
                emoji = '🏆';
                title = '素晴らしい！筋トレマスター！';
            } else if (percentage >= 60) {
                emoji = '👍';
                title = 'よくできました！';
            } else {
                emoji = '💪';
                title = 'もう少し頑張りましょう！';
            }

            resultEmoji.textContent = emoji;
            resultTitle.textContent = title;
            resultText.textContent = `あなたのスコア: ${score}/${quizData.length} (${Math.round(percentage)}%)`;
            
            resultCard.classList.remove('hidden');
        }

        function restartQuiz() {
            currentQuestion = 0;
            score = 0;
            document.getElementById('result-card').classList.add('hidden');
            document.getElementById('quiz-card').classList.remove('hidden');
            loadQuestion();
        }

        // 初期化
        loadQuestion();
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9783dfc1e2d92390',t:'MTc1NjcyMDMzOC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
