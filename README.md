# cassino-niquel
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Royal Casino - Slot Machine</title>
<style>
* { box-sizing: border-box; }

body {
    margin: 0;
    min-height: 100vh;
    background: radial-gradient(circle, #35104c, #10051c 75%);
    color: white;
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
}

.casino {
    width: 95%;
    max-width: 480px;
    text-align: center;
    padding: 25px;
    border: 3px solid #e6b832;
    border-radius: 25px;
    background: linear-gradient(145deg,#341044,#170522);
    box-shadow: 0 0 35px #d89b21;
}

h1 {
    color: #ffd84b;
    font-size: 32px;
    text-shadow: 0 0 15px gold;
    margin-bottom: 5px;
}

.subtitle {
    color: #e6c9ff;
    margin-bottom: 25px;
}

.balance {
    background: #08080e;
    border: 2px solid gold;
    border-radius: 12px;
    padding: 15px;
    margin-bottom: 20px;
    font-size: 20px;
}

.balance strong {
    color: #ffdf45;
    font-size: 27px;
}

.machine {
    display: flex;
    justify-content: center;
    gap: 10px;
    background: #090711;
    padding: 18px 10px;
    border: 4px solid #dba927;
    border-radius: 18px;
    box-shadow: inset 0 0 20px #000;
}

.reel {
    width: 95px;
    height: 105px;
    background: linear-gradient(#fff,#ddd);
    border-radius: 12px;
    color: #111;
    font-size: 58px;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: inset 0 0 10px #777;
}

.spinning {
    animation: shake .12s infinite;
}

@keyframes shake {
    0% { transform: translateY(-4px); }
    50% { transform: translateY(4px); }
    100% { transform: translateY(-4px); }
}

.controls {
    margin-top: 22px;
}

button {
    border: none;
    border-radius: 10px;
    padding: 13px 20px;
    font-weight: bold;
    cursor: pointer;
    transition: .2s;
}

button:hover {
    transform: scale(1.05);
    filter: brightness(1.2);
}

.bet-btn {
    background: #49205e;
    color: white;
    border: 1px solid gold;
    margin: 5px;
}

.spin-btn {
    display: block;
    width: 100%;
    margin-top: 18px;
    padding: 18px;
    font-size: 22px;
    background: linear-gradient(#ffe76a,#d18a00);
    color: #391c00;
    box-shadow: 0 5px 0 #865000;
}

.spin-btn:disabled {
    opacity: .5;
    cursor: not-allowed;
}

.message {
    min-height: 35px;
    margin-top: 20px;
    font-size: 20px;
    font-weight: bold;
    color: #ffdf45;
}

.info {
    color: #c8b3d6;
    font-size: 13px;
    margin-top: 20px;
}

.reset {
    background: #34223d;
    color: white;
    margin-top: 15px;
}
</style>
</head>

<body>

<div class="casino">

    <h1>👑 ROYAL CASINO</h1>
    <div class="subtitle">SLOT MACHINE • GOLD EDITION</div>

    <div class="balance">
        SEUS CRÉDITOS<br>
        <strong id="credits">1000</strong>
    </div>

    <div class="machine">
        <div class="reel" id="r1">🍒</div>
        <div class="reel" id="r2">💎</div>
        <div class="reel" id="r3">7️⃣</div>
    </div>

    <div class="controls">
        <p>VALOR DA APOSTA</p>

        <button class="bet-btn" onclick="changeBet(10)">- 10</button>
        <strong id="bet">50</strong>
        <button class="bet-btn" onclick="changeBet(-10)">+ 10</button>

        <button class="spin-btn" id="spin" onclick="spin()">
            🎰 GIRAR!
        </button>
    </div>

    <div class="message" id="message">
        BOA SORTE!
    </div>

    <button class="reset" onclick="resetGame()">
        REINICIAR JOGO
    </button>

    <div class="info">
        Jogo demonstrativo com créditos fictícios.<br>
        Divirta-se no Royal Casino!
    </div>
</div>

<script>
let credits = 1000;
let bet = 50;
let spinning = false;

const symbols = ["🍒","🍋","🍇","🍉","🔔","💎","7️⃣"];

const multipliers = {
    "🍒": 2,
    "🍋": 3,
    "🍇": 4,
    "🍉": 5,
    "🔔": 8,
    "💎": 15,
    "7️⃣": 30
};

function update() {
    document.getElementById("credits").textContent = credits;
    document.getElementById("bet").textContent = bet;
}

function changeBet(value) {
    if (spinning) return;

    bet += value;
    if (bet < 10) bet = 10;
    if (bet > 500) bet = 500;

    update();
}

function spin() {
    if (spinning) return;

    if (credits < bet) {
        document.getElementById("message").textContent =
            "CRÉDITOS INSUFICIENTES!";
        return;
    }

    spinning = true;
    credits -= bet;
    update();

    const reels = [
        document.getElementById("r1"),
        document.getElementById("r2"),
        document.getElementById("r3")
    ];

    const button = document.getElementById("spin");
    button.disabled = true;

    document.getElementById("message").textContent =
        "GIRANDO...";

    reels.forEach(reel => reel.classList.add("spinning"));

    let count = 0;

    const animation = setInterval(() => {
        reels.forEach(reel => {
            reel.textContent =
                symbols[Math.floor(Math.random() * symbols.length)];
        });

        count++;

        if (count >= 15) {
            clearInterval(animation);

            const result = reels.map(() =>
                symbols[Math.floor(Math.random() * symbols.length)]
            );

            reels.forEach((reel, i) => {
                reel.textContent = result[i];
                reel.classList.remove("spinning");
            });

            checkWin(result);

            spinning = false;
            button.disabled = false;
            update();
        }
    }, 100);
}

function checkWin(result) {
    const message = document.getElementById("message");

    if (result[0] === result[1] &&
        result[1] === result[2]) {

        const prize = bet * multipliers[result[0]];
        credits += prize;

        message.textContent =
            "🏆 JACKPOT! +" + prize + " CRÉDITOS!";

    } else if (
        result[0] === result[1] ||
        result[1] === result[2] ||
        result[0] === result[2]
    ) {

        const prize = bet * 2;
        credits += prize;

        message.textContent =
            "🎉 PAR! +" + prize + " CRÉDITOS!";

    } else {
        message.textContent = "😢 NÃO FOI DESSA VEZ!";
    }

    update();
}

function resetGame() {
    if (spinning) return;

    credits = 1000;
    bet = 50;

    document.getElementById("message").textContent =
        "BOA SORTE!";

    update();
}

update();
</script>

</body>
</html>
