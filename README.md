<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>三菱自動車おすすめ診断</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 0;
            background: url('https://www.mitsubishi-motors.co.jp/lineup/outlander_phev/special/future/img/sec5_img_01_sm.jpg') no-repeat center center fixed;
            background-size: cover;
            color: #fff;
            position: relative;
            overflow: hidden;
        }

        body::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1;
        }

        .container {
            position: relative;
            z-index: 2;
            margin: 100px auto;
            /* 結果表示部分を上目にするため、上の余白を調整 */
            padding: 20px;
            max-width: 600px;
            background-color: #1c1c1c;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
            border-radius: 10px;
        }

        h1 {
            color: #E60012;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        h1 img {
            width: 116px;
            margin-right: 16px;
        }

        button,
        a.button {
            padding: 10px 20px;
            margin: 10px;
            font-size: 16px;
            font-family: Arial, sans-serif;
            cursor: pointer;
            border: none;
            border-radius: 5px;
            background-color: #E60012;
            color: #fff;
            transition: background-color 0.3s;
            text-decoration: none;
            display: inline-block;
        }

        button:hover,
        a.button:hover {
            background-color: #b0000e;
        }

        .result {
            margin-top: 16px;
            font-size: 18px;
            font-weight: bold;
            display: none;
        }

        .car-image {
            margin-top: 20px;
            width: 100%;
            max-width: 300px;
            /* 画像を少し小さくしました */
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
        }

        .button-group {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-top: 20px;
        }
    </style>
</head>

<body>
    <div class="container">
        <h1>
            <img src="https://www.mitsubishi-motors.co.jp/assets/img/logo_01.svg" alt="三菱自動車ロゴ">三菱自動車お勧め診断
        </h1>
        <p>あなたにぴったりの三菱自動車を診断します。質問に答えてください。</p>

        <div id="question">
            <!-- 質問がここに表示される -->
        </div>

        <div id="result" class="result">
            <!-- 結果がここに表示される -->
            <img id="car-image" src="" alt="" class="car-image">
            <p id="car-comment"></p>
            <p id="car-recommendation"></p>
            <div class="button-group">
                <a id="details-link" href="#" target="_blank" class="button">詳細はこちら</a>
                <button onclick="restartQuiz()">もう一度試す</button>
            </div>
        </div>
    </div>

    <script>
        const questions = [
            {
                text: "Q1: 夜景を見るならどちらがいいですか？",
                options: ["高原で星を見る", "夜の街をドライブ"],
                nextYes: 1,
                nextNo: 3,
                resultYes: '',
                resultNo: ''
            },
            {
                text: "Q2: どちらが好みですか？",
                options: ["かわいいもの", "かっこいいもの"],
                nextYes: 2,
                nextNo: 5,
                resultYes: '',
                resultNo: ''
            },
            {
                text: "Q3: 夏と冬どちらが好きですか？",
                options: ["夏", "冬"],
                nextYes: '',
                nextNo: '',
                resultYes: 'デリカミニ',
                resultNo: 'デリカミニ シャモニー'
            },
            {
                text: "Q4: 旅行に行くならどちら派ですか？",
                options: ["国内派", "海外派"],
                nextYes: 4,
                nextNo: 8,
                resultYes: '',
                resultNo: ''
            },
            {
                text: "Q5: こだわりが強い方ですか？",
                options: ["はい", "いいえ"],
                nextYes: '',
                nextNo: '',
                resultYes: 'エクリプスクロス GAS',
                resultNo: 'エクリプスクロス PHEV'
            },
            {
                text: "Q6: おしゃれに我慢はつきものですか？",
                options: ["はい", "いいえ"],
                nextYes: '',
                nextNo: 6,
                resultYes: 'トライトン',
                resultNo: ''
            },
            {
                text: "Q7: 人と同じは嫌ですか？",
                options: ["はい", "いいえ"],
                nextYes: 7,
                nextNo: '',
                resultYes: '',
                resultNo: 'デリカ D:5'
            },
            {
                text: "Q8: 夏と冬どちらのアクティビティが好きですか？",
                options: ["夏", "冬"],
                nextYes: '',
                nextNo: '',
                resultYes: 'デリカ D:5 ジャスパー',
                resultNo: 'デリカ D:5 シャモニー'
            },
            {
                text: "Q9: 人と同じは嫌ですか？",
                options: ["はい", "いいえ"],
                nextYes: 9,
                nextNo: '',
                resultYes: '',
                resultNo: 'アウトランダー PHEV'
            },
            {
                text: "Q10: どちらかと言うと",
                options: ["シックなデザインが好き", "スポーティなデザインが好き"],
                nextYes: '',
                nextNo: '',
                resultYes: 'アウトランダー BLACK Edition',
                resultNo: 'アウトランダー PHEV ラリーアートモデル'
            }
        ];

        let currentQuestionIndex = 0;

        function loadQuestion(index) {
            const question = questions[index];
            const questionDiv = document.getElementById('question');
            questionDiv.innerHTML = `
                <p>${question.text}</p>
                <button onclick="answer(${index}, 'yes')">${question.options[0]}</button>
                <button onclick="answer(${index}, 'no')">${question.options[1]}</button>
            `;
        }

        function answer(index, choice) {
            const question = questions[index];
            let nextIndex;

            if (choice === 'yes') {
                if (question.resultYes) {
                    showResult(question.resultYes);
                    return;
                } else {
                    nextIndex = question.nextYes;
                }
            } else {
                if (question.resultNo) {
                    showResult(question.resultNo);
                    return;
                } else {
                    nextIndex = question.nextNo;
                }
            }

            if (nextIndex < questions.length) {
                loadQuestion(nextIndex);
            } else {
                showResult('結果が見つかりませんでした。');
            }
        }

        function showResult(carName) {
            const carImages = {
                "デリカミニ": "https://www.mitsubishi-motors.co.jp/lineup/delica_mini/special/stylebook_solo/images/img_01.jpg",
                "デリカミニ シャモニー": "https://www.mitsubishi-motors.co.jp/lineup/delica_mini/chamonix/images/gallery_img_01.png",
                "アウトランダー BLACK Edition": "https://www.mitsubishi-motors.co.jp/lineup/outlander_phev/grade/img/black_edition/car_01.png",
                "アウトランダー PHEV ラリーアートモデル": "https://www.mitsubishi-motors.co.jp/carlife/autosalon/tas2022/ralliart/images/img02.png",
                "トライトン": "https://www.mitsubishi-motors.co.jp/lineup/triton/special/assets/images/snow/snow_04.jpg",
                "エクリプスクロス GAS": "https://www.mitsubishi-motors.co.jp/lineup/eclipse-cross/common/img/img_car_08.png",
                "エクリプスクロス PHEV": "https://www.mitsubishi-motors.co.jp/lineup/eclipse-cross/grade/img/p_phev/car_01.png",
                "デリカ D:5": "https://www.mitsubishi-motors.co.jp/carlife/autosalon/tas2022/ralliart/images/img04.png",
                "デリカ D:5 シャモニー": "https://www.mitsubishi-motors.co.jp/lineup/delica_d5/special/select.mamastar/images/sec03_01.jpg",
                "デリカ D:5 ジャスパー": "https://web.goout.jp/app/uploads/2021/12/image-9-1.jpg",
                "アウトランダー PHEV": "https://img1.kakaku.k-img.com/images/productimage/fullscale/K0001395438_0001.jpg"
            };

            const carLinks = {
                "デリカミニ": "https://www.mitsubishi-motors.co.jp/lineup/delica_mini/",
                "デリカミニ シャモニー": "https://www.mitsubishi-motors.co.jp/lineup/delica_mini/chamonix/",
                "アウトランダー BLACK Edition": "https://www.mitsubishi-motors.co.jp/lineup/outlander_phev/grade/black_edition.html",
                "アウトランダー PHEV ラリーアートモデル": "https://www.mitsubishi-motors.co.jp/carlife/autosalon/tas2022/outlander_ralliart_style/",
                "トライトン": "https://www.mitsubishi-motors.co.jp/lineup/triton/",
                "エクリプスクロス GAS": "https://x.gd/jtPq8",
                "エクリプスクロス PHEV": "https://x.gd/jtPq8",
                "デリカ D:5": "https://www.mitsubishi-motors.co.jp/lineup/delica_d5/",
                "デリカ D:5 シャモニー": "https://www.mitsubishi-motors.co.jp/lineup/delica_d5/chamonix/index.html",
                "デリカ D:5 ジャスパー": "https://www.mitsubishi-motors.co.jp/lineup/delica_d5/jasper/",
                "アウトランダー PHEV": "https://www.mitsubishi-motors.co.jp/lineup/outlander_phev/"
            };

            const comments = {
                "デリカミニ": "海にも山にも夏のアクティビティに大活躍！",
                "デリカミニ シャモニー": "涼しげなルックスで冬のアクティビティにも大活躍！",
                "アウトランダー BLACK Edition": "デザイン性・利便性を兼ね備えた！",
                "アウトランダー PHEV ラリーアートモデル": "自分らしい走りやスタイリングが手に入る！",
                "トライトン": "おしゃれも遊びも全力なあなたに",
                "エクリプスクロス GAS": "最新と昔ながらのいいとこどり！",
                "エクリプスクロス PHEV": "走り好きなあなたに",
                "デリカ D:5": "大人数でアクティブに過ごすのが好きなあなたに",
                "デリカ D:5 シャモニー": "スポーティでスタイリッシュな選択！",
                "デリカ D:5 ジャスパー": "外装だけでなく、内装もアクティビティにぴったり",
                "アウトランダー PHEV": "流行に敏感なあなた！"
            };

            const imageUrl = carImages[carName] || 'https://via.placeholder.com/300?text=画像がありません';
            const comment = comments[carName] || '素晴らしい車種です。';
            const link = carLinks[carName] || '#';

            document.getElementById('car-image').src = imageUrl;
            document.getElementById('car-image').alt = carName;
            document.getElementById('car-comment').textContent = comment;
            document.getElementById('car-recommendation').textContent = `お勧めの車種は「${carName}」です。`;
            document.getElementById('details-link').href = link;

            document.getElementById('question').style.display = 'none';
            document.getElementById('result').style.display = 'block';
        }

        function restartQuiz() {
            currentQuestionIndex = 0;
            loadQuestion(currentQuestionIndex);
            document.getElementById('result').style.display = 'none';
            document.getElementById('question').style.display = 'block';
        }

        loadQuestion(currentQuestionIndex);
    </script>
</body>

</html>
