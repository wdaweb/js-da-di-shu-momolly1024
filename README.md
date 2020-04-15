# 04/06作業
打地鼠

1. 建立HTML CSS結構




2. JS
- 宣告所有會用到的元素

        const holes = document.getElementsByClassName("hole");
        const btnStart = document.getElementById("btn-start");
        const textScore = document.getElementById("text-score");
        const textHighPlayer = document.getElementById("text-highplayer");
        const textHighScore = document.getElementById("text-highscore")
        const textTime = document.getElementById("text-time");

        const rihai = new Audio();
        rihai.src = "./you.mp3";


- 新增會用到的變數 分數、是否在遊戲中、剩餘秒數、計時器、最高分、      

        //分數
        let score = 0;
        // 是否在遊戲中
        let inGame = false;
        // 剩餘秒數
        let timeleft = 20;
        // 計時器
        let timer = 0;
        // 最高分 
        let highscore = { name: '', score: 0 }

        // 將localstorage文的文字轉成可以使用的陣列
        let storage = JSON.parse(localStorage.getItem("highscore"));
        // 如果有分數紀錄存入變數，並修改網頁文字
        if (storage != null) {
            highscore.name = storage.name;
            textHighPlayer.innerText = highscore.name;
            highscore.score = storage.score;
            textHighScore.innerText = highscore.score;
        }


- 當'開始'按鈕被點擊時，要發生的所有事件


        btnStart.onclick = () => {
            // 停用開始按鈕

            btnStart.disabled = true;
            //設定每N秒變換一次
            timer = setInterval(game, 2000);
            game();
            //重製分數
            score = 0;
            textScore.innerText = score;

            //設定狀態為遊戲中
            inGame = true;
            // 重設時間
            timeleft = 20;
            textTime.innerText = timeleft;


        }
- 宣告遊戲進行時的function 


        const game = () => {
            // 清掉格子的CLASS
            for (let hole of holes) {
                hole.classList.remove("red");
                hole.classList.remove("blue");
            }
            // 隨機抽三格變紅色
            for (let i = 0; i < 3; i++) {
                const rand = Math.floor(Math.random() * 9);
                holes[rand].classList.add("red");
            }

            //新增:太陽 
            for (let j = 0; j < 2; j++) {
                const rand = Math.floor(Math.random() * 9);
                holes[rand].classList.add("blue");

            }

            // 時間減1
            timeleft--;
            textTime.innerText = timeleft;

            if (timeleft == 0) {
                // 設定狀態為:不在遊戲中
                inGame = false;
                // 停止計時器
                clearInterval(timer);
                // 清掉格子的CLASS
                for (let hole of holes) {
                    hole.classList.remove("red");
                    hole.classList.remove("blue");
                }

                // 啟用開始按鈕
                btnStart.disabled = false;
                alert(`你得到 ${score}分`);

                //如果沒有分數紀錄，或是大於目前最大分數。
                if (storage == null || score > highscore.score) {
                    rihai.play();
                    // 輸入名字
                    const input = prompt('恭喜獲得最高分，請輸入名字')

                    // 如果有輸入
                    if (input != null) {
                        // 將輸入的名字與分數存入變數中
                        highscore.score = score;
                        highscore.name = input;
                        // 修改最高分文字
                        textHighPlayer.innerText = highscore.name;
                        textHighScore.innerText = highscore.score;
                        // 將最高分數轉文字後存入localstorage 
                        localStorage.setItem("highscore", JSON.stringify(highscore));
                    }
                }



            }
        }
        
        
- 遊戲進行中，圖片被點擊要發生的事件

        for (let hole of holes) {
            hole.onclick = () => {
                // 如果當前狀態在遊戲中，且包含 red
                if (inGame && hole.classList.contains("red")) {
                    // 標記為按下去
                    hole.classList.add("blue");
                    hole.classList.remove("red");
                    // 加一分
                    score++;
                    textScore.innerHTML = score;
                }
                // 點錯扣分
                else if (inGame && hole.classList.contains("blue")) {
                    hole.classList.add("sunbad");
                    hole.classList.remove("blue");
                    score--;
                    textScore.innerHTML = score;
                }
                else if (inGame && hole.classList.contains("sunbad")) {
                    hole.classList.remove("sunbad");
                }


            }
        }



- 使用鍵盤遊玩


        document.onkeydown = (event) => {
            const key = event.key;
            //如果按鍵是數字，狀態為在遊戲中，數字小於10且大於0，
            if (!isNaN(key) && inGame && key > 0 && key < 10) {
                //找到對應的格子
                const target = document.getElementById(`hole${key}`);
                // 觸發點擊事件
                target.click();
            }
        }




