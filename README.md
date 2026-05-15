```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة تحدي البروتينات 🧠🍎</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Tajawal', sans-serif;
            background: linear-gradient(135deg, #fdfbfb 0%, #ebedee 100%);
            min-height: 100vh;
        }
        
        .fade-in {
            animation: fadeIn 0.5s ease-in-out;
        }

        .bounce {
            animation: bounceIn 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes bounceIn {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }

        .option-btn {
            transition: all 0.3s ease;
        }

        .option-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
        }

        .correct-answer {
            background-color: #10B981 !important;
            color: white !important;
            border-color: #059669 !important;
            transform: scale(1.02);
        }

        .wrong-answer {
            background-color: #EF4444 !important;
            color: white !important;
            border-color: #DC2626 !important;
            animation: shake 0.4s;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-5px); }
            50% { transform: translateX(5px); }
            75% { transform: translateX(-5px); }
        }
        
        /* Confetti */
        .confetti {
            position: absolute;
            width: 10px;
            height: 10px;
            background-color: #f2d74e;
            opacity: 0;
        }
    </style>
</head>
<body class="flex items-center justify-center p-4">

    <div id="app" class="w-full max-w-2xl bg-white rounded-3xl shadow-2xl overflow-hidden relative">
        
        <!-- Start Screen -->
        <div id="start-screen" class="p-10 text-center flex flex-col items-center justify-center min-h-[400px] bounce">
            <div class="text-6xl mb-4">🥩💪</div>
            <h1 class="text-4xl font-extrabold text-slate-800 mb-4">تحدي البروتينات</h1>
            <p class="text-slate-600 mb-8 text-lg">اختبر معلوماتك من محاضرة أساسيات التغذية بطريقة ممتعة!</p>
            <button onclick="startGame()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-4 px-10 rounded-full text-xl transition-colors shadow-lg hover:shadow-xl transform hover:-translate-y-1">
                ابدأ اللعب الآن 🚀
            </button>
        </div>

        <!-- Game Screen -->
        <div id="game-screen" class="hidden flex-col h-full fade-in">
            <!-- Header (Progress & Score) -->
            <div class="bg-indigo-50 p-6 border-b border-indigo-100 flex justify-between items-center">
                <div>
                    <span class="text-sm font-bold text-indigo-500 uppercase tracking-wider">السؤال</span>
                    <div class="text-2xl font-black text-indigo-900"><span id="current-q-num">1</span> <span class="text-lg text-indigo-400">/ 10</span></div>
                </div>
                <div class="text-left">
                    <span class="text-sm font-bold text-emerald-500 uppercase tracking-wider">النتيجة</span>
                    <div class="text-2xl font-black text-emerald-600 flex items-center justify-end gap-2">
                        <span id="score-display">0</span> <span>⭐</span>
                    </div>
                </div>
            </div>

            <!-- Progress Bar -->
            <div class="h-2 w-full bg-slate-100">
                <div id="progress-bar" class="h-full bg-indigo-500 transition-all duration-300" style="width: 10%;"></div>
            </div>

            <!-- Question Area -->
            <div class="p-8">
                <h2 id="question-text" class="text-2xl md:text-3xl font-bold text-slate-800 mb-8 leading-tight">
                    <!-- Question goes here -->
                </h2>

                <!-- Options -->
                <div id="options-container" class="space-y-4">
                    <!-- Options injected via JS -->
                </div>
            </div>
            
            <!-- Next Button (Hidden initially) -->
            <div class="p-6 pt-0 text-left">
                <button id="next-btn" onclick="nextQuestion()" class="hidden w-full md:w-auto bg-slate-800 hover:bg-slate-900 text-white font-bold py-3 px-8 rounded-xl transition-colors shadow-md">
                    السؤال التالي ⬅️
                </button>
            </div>
        </div>

        <!-- Result Screen -->
        <div id="result-screen" class="hidden p-10 text-center flex-col items-center justify-center min-h-[400px]">
            <div id="result-emoji" class="text-7xl mb-4 bounce">🏆</div>
            <h2 id="result-title" class="text-3xl font-extrabold text-slate-800 mb-2">أحسنت!</h2>
            <p class="text-slate-600 mb-6 text-lg">لقد أكملت الاختبار.</p>
            
            <div class="bg-slate-50 border-2 border-slate-100 rounded-2xl p-6 w-full max-w-sm mx-auto mb-8">
                <div class="text-sm text-slate-500 font-bold mb-1">نتيجتك النهائية</div>
                <div class="text-5xl font-black text-indigo-600 mb-2"><span id="final-score">0</span> <span class="text-2xl text-slate-400">/ 10</span></div>
            </div>

            <button onclick="resetGame()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-8 rounded-full text-lg transition-colors shadow-lg hover:shadow-xl inline-flex items-center gap-2">
                <span>🔄</span> العب مرة أخرى
            </button>
        </div>

    </div>

    <script>
        // Quiz Data based on the presentation
        const quizData = [
            {
                question: "أي من العناصر الغذائية التالية يعتبر الوحيد الذي يحتوي على عنصر النيتروجين؟",
                options: ["الكربوهيدرات", "البروتينات", "الدهون", "الفيتامينات"],
                correctAnswer: 1
            },
            {
                question: "كم عدد الأحماض الأمينية الأساسية (Essential) التي يجب الحصول عليها من النظام الغذائي؟",
                options: ["21 حمضاً", "12 حمضاً", "9 أحماض", "15 حمضاً"],
                correctAnswer: 2
            },
            {
                question: "ما هو الإنزيم الموجود في المعدة والذي يبدأ عملية الهضم الكيميائي للبروتينات؟",
                options: ["البيبسين (Pepsin)", "الأميليز (Amylase)", "اللايباز (Lipase)", "التربسين (Trypsin)"],
                correctAnswer: 0
            },
            {
                question: "كم عدد السعرات الحرارية التي يوفرها جرام واحد من البروتين؟",
                options: ["9 سعرات حرارية", "7 سعرات حرارية", "سعرتان حراريتان", "4 سعرات حرارية"],
                correctAnswer: 3
            },
            {
                question: "ماذا يسمى دمج أطعمة تحتوي على بروتينات غير مكتملة لتكوين بروتين مكتمل (مثل الأرز مع الفاصوليا)؟",
                options: ["البروتينات الحيوانية", "البروتينات التكميلية (Complementary)", "الأحماض غير الأساسية", "البروتينات الصناعية"],
                correctAnswer: 1
            },
            {
                question: "ما هو المرض الناتج عن نقص البروتين والذي يتميز بتراكم الدهون في الكبد وحدوث وذمة (Edema)؟",
                options: ["ماراسمس (Marasmus)", "الكساح", "كواشيوركور (Kwashiorkor)", "هشاشة العظام"],
                correctAnswer: 2
            },
            {
                question: "أي عضو في الجسم يقوم بتحويل الأمونيا السامة (الناتجة عن استقلاب البروتين) إلى يوريا للتخلص منها؟",
                options: ["الكلى", "الكبد", "المعدة", "البنكرياس"],
                correctAnswer: 1
            },
            {
                question: "ما هو الاحتياج اليومي المتوسط من البروتين لكل كيلوجرام من وزن الجسم للبالغين الأصحاء؟",
                options: ["0.8 جرام", "1.2 جرام", "2.0 جرام", "0.5 جرام"],
                correctAnswer: 0
            },
            {
                question: "أي من الحالات التالية تؤدي إلى توازن نيتروجين إيجابي (Positive Nitrogen Balance)؟",
                options: ["الحمى الشديدة", "الحروق", "المجاعة", "فترات الحمل والنمو"],
                correctAnswer: 3
            },
            {
                question: "الإفراط في تناول البروتين قد يزيد من إفراز أي من المعادن التالية، مما يرفع خطر الإصابة بهشاشة العظام؟",
                options: ["الحديد", "الكالسيوم", "الصوديوم", "البوتاسيوم"],
                correctAnswer: 1
            }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let isAnswered = false;

        // DOM Elements
        const startScreen = document.getElementById('start-screen');
        const gameScreen = document.getElementById('game-screen');
        const resultScreen = document.getElementById('result-screen');
        const questionText = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const currentQNum = document.getElementById('current-q-num');
        const scoreDisplay = document.getElementById('score-display');
        const progressBar = document.getElementById('progress-bar');
        const nextBtn = document.getElementById('next-btn');

        function startGame() {
            startScreen.classList.add('hidden');
            resultScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            currentQuestionIndex = 0;
            score = 0;
            updateScoreDisplay();
            loadQuestion();
        }

        function loadQuestion() {
            isAnswered = false;
            nextBtn.classList.add('hidden');
            
            const currentQ = quizData[currentQuestionIndex];
            
            // Update UI
            currentQNum.innerText = currentQuestionIndex + 1;
            progressBar.style.width = `${((currentQuestionIndex) / quizData.length) * 100}%`;
            
            // Apply animation to question text
            questionText.classList.remove('fade-in');
            void questionText.offsetWidth; // Trigger reflow
            questionText.classList.add('fade-in');
            questionText.innerText = currentQ.question;

            // Clear and load options
            optionsContainer.innerHTML = '';
            currentQ.options.forEach((option, index) => {
                const btn = document.createElement('button');
                btn.className = 'option-btn w-full text-right p-4 rounded-xl border-2 border-slate-200 bg-white text-slate-700 font-bold hover:bg-indigo-50 hover:border-indigo-300 focus:outline-none';
                btn.innerText = option;
                btn.onclick = () => selectOption(index, btn);
                
                // Add staggered animation
                btn.style.animationDelay = `${index * 0.1}s`;
                btn.classList.add('fade-in');
                
                optionsContainer.appendChild(btn);
            });
        }

        function selectOption(selectedIndex, btnElement) {
            if (isAnswered) return;
            isAnswered = true;

            const currentQ = quizData[currentQuestionIndex];
            const allOptions = optionsContainer.children;

            if (selectedIndex === currentQ.correctAnswer) {
                // Correct Answer
                btnElement.classList.add('correct-answer');
                score++;
                updateScoreDisplay();
                playCorrectSound();
            } else {
                // Wrong Answer
                btnElement.classList.add('wrong-answer');
                // Highlight the correct one
                allOptions[currentQ.correctAnswer].classList.add('correct-answer');
            }

            // Disable all buttons
            for (let btn of allOptions) {
                btn.style.cursor = 'default';
                if (!btn.classList.contains('correct-answer') && !btn.classList.contains('wrong-answer')) {
                    btn.style.opacity = '0.5';
                }
            }

            // Show next button
            nextBtn.classList.remove('hidden');
            nextBtn.classList.add('fade-in');
            progressBar.style.width = `${((currentQuestionIndex + 1) / quizData.length) * 100}%`;
        }

        function nextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < quizData.length) {
                loadQuestion();
            } else {
                showResult();
            }
        }

        function updateScoreDisplay() {
            scoreDisplay.innerText = score;
            // Pop animation
            scoreDisplay.parentElement.classList.remove('bounce');
            void scoreDisplay.parentElement.offsetWidth;
            scoreDisplay.parentElement.classList.add('bounce');
        }

        function showResult() {
            gameScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
            resultScreen.classList.add('fade-in');
            
            document.getElementById('final-score').innerText = score;
            
            const resultTitle = document.getElementById('result-title');
            const resultEmoji = document.getElementById('result-emoji');
            
            if (score === quizData.length) {
                resultTitle.innerText = "علامة كاملة! عبقري تغذية!";
                resultEmoji.innerText = "🏆🔥";
                triggerConfetti();
            } else if (score >= 7) {
                resultTitle.innerText = "أداء ممتاز جداً!";
                resultEmoji.innerText = "🌟👏";
            } else if (score >= 5) {
                resultTitle.innerText = "أداء جيد، تحتاج لبعض المراجعة";
                resultEmoji.innerText = "👍📚";
            } else {
                resultTitle.innerText = "حاول مرة أخرى بعد قراءة المحاضرة";
                resultEmoji.innerText = "💪📖";
            }
        }

        function resetGame() {
            startGame();
        }

        // Simple audio feedback simulation (using web audio api if needed, but visual is enough here, keeping function for extensibility)
        function playCorrectSound() {
            // Optional: Add sound effects here in the future
        }

        // Simple Confetti Effect
        function triggerConfetti() {
            const colors = ['#10B981', '#3B82F6', '#F59E0B', '#EF4444', '#8B5CF6'];
            for (let i = 0; i < 50; i++) {
                createConfettiPiece(colors[Math.floor(Math.random() * colors.length)]);
            }
        }

        function createConfettiPiece(color) {
            const piece = document.createElement('div');
            piece.className = 'confetti';
            piece.style.backgroundColor = color;
            piece.style.left = Math.random() * 100 + 'vw';
            piece.style.top = '-10px';
            document.body.appendChild(piece);

            const animation = piece.animate([
                { transform: `translate3d(0,0,0) rotate(0deg)`, opacity: 1 },
                { transform: `translate3d(${Math.random()*200 - 100}px, 100vh, 0) rotate(${Math.random()*360}deg)`, opacity: 0 }
            ], {
                duration: Math.random() * 2000 + 1500,
                easing: 'cubic-bezier(.37,0,.63,1)'
            });

            animation.onfinish = () => piece.remove();
        }
    </script>
</body>
</html>

```
