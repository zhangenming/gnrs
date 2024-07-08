```js
// aaOfVYRbM zem2821
const lightblue = "#4363d8";
clearInterval(window.timer2);
const 游戏结束 = ".alert.center";

const log = console.info;
console.log = ()=>{}
;
console.group = ()=>{}
;
const W = 87;
const A = 65;
const S = 83;
const D = 68;

const $$ = (str)=>[...document.querySelectorAll(str)];
const $ = (str)=>$$(str)[0];

const 我的用户名 = "zem";

function useCss(textContent) {
    const style = document.createElement("style");
    style.type = "text/css";
    style.textContent = textContent;
    document.head.appendChild(style);
}
useCss(``);
useCss(``);
useCss(`
    .lightblue {background-color:red!important}
    .general {z-index: 200!important;outline: 10px solid red;}
    .selectable {background-color:#4363d8!important}
    .selected  {z-index: 200!important;outline: 8px solid #ff8c00; }
    .attackable {opacity:1!important;}
    .center-vertical, .center-horizontal {color:#ff8c00!important;font-size:25px}
    #replay-ad-container {display:none!important}
    ${游戏结束}:hover {opacity:20}
    #turn-flag{
        position: absolute;
        top: 0;
        background: red;
        height: 7px;
        z-index: 9999;
        width: var(--width)
    }
    #turn-counter{
        top: 45%;
        left: auto;
        right: 0px;
        display: none
    }
    #game-leaderboard {top:25%}
    .relative{
        left:0px!important;top:15px!important;
        transform-origin: left top;
        transform: scale(var(--scale));
    }
    .server-chat-message{ display: none }
    .敌人基地 {zIndex:111!important; outline: 5px solid red}
    .敌人主力 {background-color: #9C27B0 !important;color: white!important;font-weight: 900;}
    .可被吃掉 {background-color: #ef08e0 !important;color: #65f500!important;font-weight: 900;}
    
    `);

function init() {
    const 棋盘宽度 = $("#gameMap tbody tr").childElementCount;
    const 棋盘高度 = $("#gameMap tbody").childElementCount;

    $$("#gameMap td").forEach((dom,idx,arr)=>{
        const line = Math.floor(idx / 棋盘宽度);
        const row = idx % 棋盘宽度;

        const left = row !== 0 && arr[idx - 1];
        const right = row !== 棋盘宽度 - 1 && arr[idx + 1];
        const top = line !== 0 && arr[idx - 棋盘宽度];
        const bottom = line !== 棋盘高度 - 1 && arr[idx + 棋盘宽度];

        Object.assign(dom, {
            idx,
            line,
            row,

            left,
            right,
            top,
            bottom,

            nodes: [left, right, top, bottom].filter((e)=>e),
        });
    }
    );
}

const 基地 = new Set();
const 已经探索过的地图 = new Set();

let 我的颜色;
let 敌人颜色;

const 兵营 = new Set()
// 会被隐藏 所以要全局保持
const 敌人兵营 = new Set()
// 会被隐藏 所以要全局保持
let oldStyle;
window.timer2 = setInterval(()=>{
    if (!gameStaus)
        return;

    $$(".general").forEach((g)=>基地.add(g));
    $$("#gameMap td").filter((td)=>!td.className.includes("fog")).forEach((td)=>已经探索过的地图.add(td));

    const q = [...兵营].map((city)=>{
        return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){border: 5px solid yellow !important; z-index:100!important}`;
    }
    );
    const w = [...敌人兵营].filter(city=>!city.className.includes(我的颜色)).map((city)=>{
        return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){outline: 10px solid yellow !important; z-index:100!important}`;
    }
    );
    const r = [...已经探索过的地图].filter((e)=>e.className.includes("fog")).map((city)=>{
        return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){background-color: rgba(255, 255, 255, 0.5)!important}`;
    }
    );

    const t = [...突出敌人动向].map((dom)=>{
        return `#gameMap tr:nth-child(${dom.line + 1}) > td:nth-child(${dom.row + 1}){outline: 10px solid black !important; z-index: 30!important}`;
    }
    );

    const newStyle = [...q, ...w, ...r, ...t].join("");
    if (oldStyle !== newStyle) {
        $("style").innerText = oldStyle = newStyle;
    }
}
, 100);

function is空地(dom) {
    return (dom.classList.length === 0 || (dom.classList.length === 1 && dom.className.includes("attackable")));
}
function is敌人(dom) {
    return dom.className.includes(敌人颜色);
}
function is我的(dom) {
    return dom.className.includes(我的颜色);
}

function useInterval(fn, time, _timer) {
    const timer = `${_timer}Timer`;

    clearInterval(window[timer]);

    window[timer] = setInterval(()=>{
        if (gameStaus === false)
            return
        fn()
    }
    , time);
}
let backItem

const d = 0
document.onkeydown = e=>d && log(e.key)
document.onclick = ({target})=>{
    d && log('手动点击1', backItem?.idx, $('.selected')?.idx, target.idx)
    clickDom(target)
    d && log('手动点击2', backItem?.idx, $('.selected')?.idx, target.idx)
}

let 可扩展地块 = []
function 自动移动feat() {
    useInterval(()=>{
        if ($$(".center-vertical,.center-horizontal").length)
            return
        // if(Number(回合dom.innerHTML.slice(5)) < 40) return

        const [,,,,,,start] = get兵力()
        start.innerText = 可扩展地块.length + '|'
        start.style.color = lightblue

        if (可扩展地块.length === 0)
            return

        const [l,r] = 可扩展地块[0]

        backItem = $(".selected");
        d && log('备份', backItem?.idx, $('.selected')?.idx)

        clickDom(l);
        d && log('移动auto', backItem?.idx, $('.selected')?.idx, l.idx)

        移动(l, r);

        clickDom(backItem)
        d && log('还原', backItem?.idx, $('.selected')?.idx)

    }
    , 10, 's')
}

function clickDom(dom) {
    if (!dom)
        return;

    dom.dispatchEvent(new MouseEvent("mousedown",{
        bubbles: true,
    }));
    dom.dispatchEvent(new MouseEvent("mouseup",{
        bubbles: true,
    }));
}
function 移动(l, r) {
    key(计算方向(l, r));

    function 计算方向(l, r) {
        if (typeof l !== "number")
            l = l.idx;

        if (typeof r !== "number")
            r = r.idx;

        if (r > l) {
            if (r == l + 1)
                return D;

            return S;
        }

        if (r + 1 == l)
            return A;

        return W;
    }

}

function key(k) {
    document.dispatchEvent(new KeyboardEvent("keydown",{
        keyCode: typeof k === 'number' ? k : k.toUpperCase().charCodeAt(0),
        bubbles: true,
    }));
}

let 突出敌人动向 = new Set();
function obDom(dom) {
    const config = {
        childList: true,
        subtree: true,
        characterData: true,
        characterDataOldValue: true,
    };

    const callback = ([MutationRecord])=>{
        if (!is敌人(dom))
            return;

        if (!dom.innerText)
            return;
        //fog

        if (MutationRecord.target.nodeValue - MutationRecord.oldValue === 1)
            return;
        //25和回合自动增加1

        const 敌军 = $$("#gameMap ." + 敌人颜色);
        const max = Math.max(...敌军.map((e)=>e.innerText));
        if (dom.innerText != max)
            return;

        突出敌人动向.add(dom);
        setTimeout(()=>{
            突出敌人动向.delete(dom);
        }
        , 2000);
    }
    ;
    dom.o?.disconnect();
    dom.o = new MutationObserver(callback);
    dom.o.observe(dom, config);
}

function get兵力() {
    const [陆地1,军队1,名字1,star1] = [...$("#game-leaderboard > tbody > tr:nth-child(2)").children, ].reverse();
    const [陆地2,军队2,名字2,star2] = [...$("#game-leaderboard > tbody > tr:nth-child(3)").children, ].reverse();

    const [我的名字,我的军队,我的陆地,敌人名字,敌人军队,敌人陆地,start] = 名字1.innerText.includes(我的用户名) ? [名字1, 军队1, 陆地1, 名字2, 军队2, 陆地2, star1] : [名字2, 军队2, 陆地2, 名字1, 军队1, 陆地1, star2];

    return [我的名字, 我的军队, 我的陆地, 敌人名字, 敌人军队, 敌人陆地, start];
}
function flash(dom) {
    let flag = 0
    const timer = setInterval(()=>{
        flag++
        dom.style.color = (flag % 2) ? 'white' : 'black'
        dom.style.background = (flag % 2) ? 'black' : 'white'
    }
    , 100)

    setTimeout(()=>clearInterval(timer), 1500)
}

const utils = {
    比较兵力(l, r) {
        return Number(l.innerText) >= Number(r.innerText);
    },
    添加样式(dom, style) {
        $$("." + style).forEach((dom)=>dom.classList.remove(style));
        dom.classList.add(style);
        // todo 闪烁
    },
};
const get = {
    所有敌人() {
        return $$("#gameMap ." + 敌人颜色).map((e)=>e.innerText);
    },
};

let gameStaus = false;
let 所有对局;
let 输了;
let 当前回合;
let 我的基地;
let 敌人基地;
obDomRoot();
function obDomRoot() {
    const config = {
        childList: true,
        subtree: true,
        characterData: true,
        characterDataOldValue: true,
    };

    const callback = (MutationRecords)=>{
        // 游戏结束
        if ($(".alert.center")) {
            if (gameStaus === true) {
                gameStaus = false;
                log('game over')
                gameEnd();
            }

            // 输了自动邀请
            // 输了 && $('.game-end-alert button').click()
        }// 游戏开始
        else if ($(".general")) {
            if (gameStaus === false) {
                gameStaus = true;
                log('game start')
                gameStart();
            }

            let dddRun = false
            // 游戏中
            MutationRecords.forEach((m)=>{
                if (0) {}// 回合
                else if (isBelong(m, "#turn-counter"))
                    回合feat();
                    // // 记分板
                else if ($("#game-leaderboard").contains(m.target))
                    兵力diff(MutationRecords);
                    // 棋盘
                else if ($("#gameMap").contains(m.target)) {
                    ddd(m)
                    dddRun = true

                    function ddd(m) {
                        const {parentNode} = m.target;

                        // 发现兵营
                        if (parentNode.className.includes("city")) {
                            兵营.add(parentNode)
                            if (parentNode.className.includes(敌人颜色)) {
                                敌人兵营.add(parentNode)
                            }
                        }

                        if (is敌人(parentNode)) {
                            if (utils.比较兵力(parentNode, 我的基地)) {// log('发现危险分子')
                            // utils.添加样式(parentNode, '可被吃掉')
                            }

                            const 所有敌人 = get.所有敌人();
                            const 敌人主力 = Math.max(...所有敌人);
                            if (Number(parentNode.innerText) === 敌人主力 && 所有敌人.filter((e)=>e == 敌人主力).length === 1) {
                                utils.添加样式(parentNode, "敌人主力");
                            }

                            // 发现基地
                            if (parentNode.className.includes("general")) {
                                withStyle(parentNode, {
                                    zIndex: '111',
                                    outline: "8px solid red"
                                });
                            }
                        }

                        if (dddRun)
                            return
                        const base = $$(".selectable.neutral")// 排除 基地/城市
                        .filter((e)=>!e.className.includes("selected"))
                        // 不自动走 选中的

                        可扩展地块 = [...get可扩展敌人(), ...get可扩展空地()]
                        function get可扩展空地() {
                            return (base.filter((e)=>e.innerHTML >= 2)// .filter((e) => e.innerHTML <= 10)
                            .sort((q,w)=>q.innerHTML - w.innerHTML).slice(0, -1)// 过滤主力部队
                            .flatMap((e)=>e.nodes.filter(is空地).map((ee)=>[e, ee])));
                        }
                        function get可扩展敌人() {
                            return (base.sort((q,w)=>q.innerHTML - w.innerHTML).slice(0, -1)// 过滤主力部队
                            .flatMap((e)=>e.nodes.filter(敌人=>is敌人(敌人) && (Number(e.innerHTML) >= Number(敌人.innerHTML) + 2)).map((ee)=>[e, ee])));
                        }

                    }
                } else {// log(m)
                }
            }
            );
        }
    }
    ;

    document.o?.disconnect();
    document.o = new MutationObserver(callback);
    document.o.observe(document, config);

}

const clearDoms = new Set()

function withStyle(dom, style) {
    clearDoms.add(dom);

    Object.entries(style).forEach(([k,v])=>{
        dom.style.setProperty(k, v, "important");
    }
    );
}

function isB(MutationRecords, selector) {
    const childs = [...$(selector).childNodes];
    return MutationRecords.some((m)=>childs.includes(m.target));
}
function isBelong({target}, selector, fn) {
    return target.parentNode === $(selector);
}
function addGameResult() {
    所有对局.push({
        type: "1v1",
        ranking: 输了 ? [{
            name: get兵力().map((e)=>e.innerText)[3],
        }, {
            name: 我的用户名,
        }, ] : [{
            name: 我的用户名,
        }, {
            name: get兵力().map((e)=>e.innerText)[3],
        }, ],
    });
}

function gameEnd() {
    兵营.clear()
    敌人兵营.clear()
    基地.clear();
    已经探索过的地图.clear();
    突出敌人动向.clear();

    输了 = document.querySelector("#game-page > div.alert.center > center > h1").innerText !== "You won!";
    addGameResult();
}

function gameStart() {
    console.clear();
    init();
    $$("#gameMap td").forEach(obDom);

    // clear上盘数据
    clearDoms.forEach((dom)=>(dom.style = null));
    $("#game-leaderboard > tbody > tr:nth-child(1) > td:nth-child(2)").innerHTML = ''
    $(".chat-messages-container").classList.add("minimized");
    // 聊天窗口缩小

    log("地图大小", $("#gameMap tbody tr").childElementCount, $("#gameMap tbody").childElementCount);

    回合dom = $("#turn-counter")
    我的基地 = $(`.general`);
    我的颜色 = 我的基地.className.includes("red") ? "red" : "lightblue";
    敌人颜色 = 我的颜色 === "red" ? "lightblue" : "red";

    const [我的名字,我的军队,我的陆地,敌人名字] = get兵力();
    我的名字.style.setProperty("background-color", "#4363d8", "important");
    敌人名字.style.setProperty("background-color", "red", "important");

    胜率feat();
    自动移动feat();

    setTimeout(()=>{
        document.documentElement.style.setProperty("--scale", document.body.clientHeight / $(".relative").offsetHeight);
    }
    , 500);
}

let 当前回合flag;
// 防止无限循环
let 回合dom
function 兵力diff(MutationRecords) {
    if (当前回合flag === 回合dom.innerText.slice(5))
        return;
    当前回合flag = 回合dom.innerText.slice(5);

    f1()
    f2()
    function f1() {
        const [我的名字,我的军队,我的陆地,敌人名字,敌人军队,敌人陆地] = get兵力();

        let 我的军队change = 0;
        let 我的陆地change = 0;
        let 敌人军队change = 0;
        let 敌人陆地change = 0;

        const 回合 = 回合dom.innerHTML;
        const is增长回合 = !回合.includes(".");
        const is25回合 = 回合.slice(5) % 25 === 0;
        const 我的塔数量 = Number($$(".general, .city").filter((e)=>e.className.includes(我的颜色)).length);
        const 我的自然增长 = is增长回合 ? 我的塔数量 + (is25回合 ? Number(我的陆地.innerText) : 0) : 0;
        const 敌人塔数量 = Number($$(".general, .city").filter((e)=>e.className.includes(敌人颜色)).length);
        const 敌人自然增长 = is增长回合 ? 敌人塔数量 + (is25回合 ? Number(敌人陆地.innerText) : 0) : 0;

        MutationRecords.forEach((m)=>{
            if (m.target === 我的军队.firstChild) {
                我的军队change = m.target.nodeValue - m.oldValue;
            }
            if (m.target === 我的陆地.firstChild) {
                我的陆地change = m.target.nodeValue - m.oldValue;
            }
            if (m.target === 敌人军队.firstChild) {
                敌人军队change = m.target.nodeValue - m.oldValue;
            }
            if (m.target === 敌人陆地.firstChild) {
                敌人陆地change = m.target.nodeValue - m.oldValue;
            }
            if (gameStaus === false) {
                // (m.target.nodeValue == 0) && log(m.oldValue)
                log(m.target.nodeValue, m.oldValue)
            }
        }
        );

        if (敌人军队change < -30 && 我的军队change > -20) {
            if (敌人军队.innerText == 0)
                return
            //hack end
            if (回合dom.innerText.slice(5) == 0)
                return
            //hack start

            const dom = $("#game-leaderboard > tbody > tr:nth-child(1) > td:nth-child(2)")
            if (dom.innerText === 'Player')
                dom.innerText = ''

            dom.innerText += Math.abs(敌人军队change) + ','
            // TODO 闪烁
            flash(dom)
            // log(
            //   `%c${敌人军队change} `,
            //   "font-size:50px",
            //   回合dom.innerText
            // );
        }
    }
    function f2() {
        const [我的名字,我的军队,我的陆地,敌人名字,敌人军队,敌人陆地] = get兵力().map((e)=>Number(e.innerText));

        f($("#game-leaderboard td:nth-child(3)"), 我的军队, 敌人军队);
        f($("#game-leaderboard td:nth-child(4)"), 我的陆地, 敌人陆地);

        function f(dom, l, r) {
            if (r == 0)
                return;
            //hack 赢了的时候是0

            dom.innerHTML = g(l, r);
            dom.style.color = "white";
            dom.style.background = l > r ? lightblue : "red";
        }
        function g(l, r) {
            const diff = l - r;
            const per = ((diff / l) * 100).toFixed()
            return `${per}%<span style="font-size:10px">${Math.abs(diff)}个</span>`
        }
    }

}

function 回合feat() {
    // 25爆兵
    // 20吞兵
    // 10出击
    // 10收集

    当前回合 = 回合dom.innerText.slice(5);
    const x = 当前回合 * 2 + (当前回合.includes(".") ? 1 : 0);
    const w = (x % 50) * 2 + 1

    const dom = createDom();
    dom.style.width = w + '%'
    dom.innerText = 50 - (x % 50)

    function createDom() {
        if (!$("#turn-flag")){
          $("#gameMap").style.position = 'relative'
          $("#gameMap").insertAdjacentHTML("beforeend", `<div id="turn-flag" style="
         position: absolute;
      top: -15px;
      background: black;
      color: white;
      height: 15px;
      z-index: 9999;
      text-align: right;"></div>`);
        }

        return $("#turn-flag")
    }
}

async function 胜率feat() {
    所有对局 = 所有对局 || (await 获取所有对局());
    const 对方用户名 = $$("td.leaderboard-name").map((e)=>e.innerText).find((e)=>!e.includes(我的用户名));
    const 总对局 = 所有对局.filter((e)=>e.type === "1v1").filter((e)=>e.ranking.some((ee)=>ee.name === 对方用户名));
    const 胜利 = 总对局.filter((e)=>e.ranking[0].name.includes(我的用户名));
    const 胜率 = ((胜利.length / 总对局.length) * 100).toFixed(1) + "%";
    log("胜率", 总对局.length, 胜率);

    async function 获取所有对局() {
        let count = 50;
        let offset = -count;
        const datas = [];

        async function fetchData() {
            const p = fetch(`https://generals.io/api/replaysForUsername?u=zem2821&offset=${(offset += count)}&count=${count}`);
            const data = await (await p).json();
            datas.push(...data);
            if (data.length === count)
                await fetchData();

            return datas;
        }

        return await fetchData();
    }
}

```
