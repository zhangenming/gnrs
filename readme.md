```js
// aaOfVYRbM zem2821
const lightblue = "#4363d8";
clearInterval(window.timer2);
const 游戏结束 = ".alert.center";

const log = console.info;
console.log = () => {};
console.group = () => {};
const W = 87;
const A = 65;
const S = 83;
const D = 68;

const $$ = (str) => [...document.querySelectorAll(str)];
const $ = (str) => $$(str)[0];

let 开启auto = false;
document.onkeydown = (e) => e.code === "Space" && (开启auto = true);
document.onkeyup = (e) => e.code === "Space" && (开启auto = false);

const 我的用户名 = "zem";

function init() {
  //clearInterval(timer)

  const 棋盘宽度 = $("#gameMap tbody tr").childElementCount;
  const 棋盘高度 = $("#gameMap tbody").childElementCount;

  $$("#gameMap td").forEach((dom, idx, arr) => {
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

      nodes: [left, right, top, bottom].filter((e) => e),
    });
  });
}

let gameStaus = false;

clearInterval(window.timer1);
window.timer1 = setInterval(() => {
  const gameStaus2 = $("#gameMap") && !$(".game-end-alert");
  if (gameStaus === gameStaus2) return;

  gameStaus = gameStaus2;

  if (gameStaus) {
    init();
    $$("#gameMap td").forEach(obDom);

    obDom2(document.querySelector("#game-leaderboard"));

    window.timer3 = setInterval(() => 开启auto && 自动移动(), 100);
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
            top: 150px;
            left: auto;
            right: 0px
        }
        #game-leaderboard {top:10px}
        .relative{
            left:0px!important;top:0px!important;
            transform-origin: left top;
            transform: scale(var(--scale));
        }
        .chat-messages-container  p:nth-child(1){ display: none }
        .敌人基地 {zIndex:111!important; outline: 5px solid red}
        .敌人主力 {background-color: yellow !important;color: black!important;font-weight: 900;}
        .可被吃掉 {background-color: #ef08e0 !important;color: #65f500!important;font-weight: 900;}
        
        `);
  } else {
    兵营.clear();
    敌人兵营.clear();
    基地.clear();
    已经探索过的地图.clear();
    clearInterval(window.timer3);
    drArmyOld = 0;
    突出敌人动向.clear();
    偷塔次数 = 0;
  }
}, 100);

const 兵营 = new Set();
const 敌人兵营 = new Set();
const 基地 = new Set();
const 已经探索过的地图 = new Set();

let 我的颜色;
let 敌人颜色;

const myDom = [...$$("#game-leaderboard tr:nth-child(2) td")][0];

let drArmyOld;
let oldStyle;
let 偷塔次数 = 0;
window.timer2 = setInterval(() => {
  if (!gameStaus) return;

  const [myStart, myName, myArmy, myLand] = [
    ...$$("#game-leaderboard tr:nth-child(2) td"),
  ].map((e) => e.innerText);
  const [drStart, drName, drArmy, drLand] = [
    ...$$("#game-leaderboard tr:nth-child(3) td"),
  ].map((e) => e.innerText);

  const armyDom = $("#ui #army");
  const armyDiff = myArmy - drArmy;

  drArmyOld = drArmy;

  $$(".city").forEach((city) => {
    兵营.add(city);
    if (city.classList.contains(敌人颜色) || city.classList.contains("fog")) {
      敌人兵营.add(city);
    }
  });
  $$(".general").forEach((g) => 基地.add(g));
  $$("#gameMap td")
    .filter((td) => !td.classList.contains("fog"))
    .forEach((td) => 已经探索过的地图.add(td));

  const q = [...兵营].map((city) => {
    return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${
      city.row + 1
    }){border: 5px solid yellow !important}`;
  });
  const w = [...敌人兵营].map((city) => {
    return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${
      city.row + 1
    }){outline: 10px solid yellow !important}`;
  });
  const r = [...已经探索过的地图]
    .filter((e) => e.classList.contains("fog"))
    .map((city) => {
      return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${
        city.row + 1
      }){background-color: rgba(255, 255, 255, 0.5)!important}`;
    });

  const t = [...突出敌人动向].map((dom) => {
    return `#gameMap tr:nth-child(${dom.line + 1}) > td:nth-child(${
      dom.row + 1
    }){outline: 10px solid black !important; z-index: 30!important}`;
  });

  const newStyle = [...q, ...w, ...r, ...t].join("");
  if (oldStyle !== newStyle) {
    $("style").innerText = oldStyle = newStyle;
  }
}, 100);

setInterval(() => {
  防止多余敌人操作.clear();
}, 5000);
const 防止多余空地操作 = new Set();
const 防止多余敌人操作 = new Set();

function is空地(dom) {
  return (
    dom.classList.length === 0 ||
    (dom.classList.length === 1 && dom.classList.contains("attackable"))
  );
}
function is敌人(dom) {
  return dom.classList.contains(敌人颜色);
}
function is我的(dom) {
  return dom.classList.contains(我的颜色);
}

function useInterval(fn, time, _timer) {
  const timer = `${_timer}Timer`;

  clearInterval(window[timer]);

  window[timer] = setInterval(()=>{
    if(gameStaus3 === false) return
    fn()
  }, time);
}
let backItem

const d = 0
document.onkeydown = e=>d && log(e.key)
document.onclick = ({target})=>{
  d && log('手动点击1', backItem?.idx,  $('.selected')?.idx, target.idx)
  clickDom(target)
  d && log('手动点击2', backItem?.idx,  $('.selected')?.idx, target.idx)
}


function 自动移动feat() {
  useInterval(()=>{
    if(Number($("#turn-counter").innerHTML) < 35) return

    
    const base = 
        $$(".selectable.neutral") // 排除 基地/城市
          .filter((e) => !e.classList.contains("selected")) // 不自动走 选中的
    
    const 可扩展地块 = [...get可扩展敌人(), ...get可扩展空地()]
    const [, , , , , , start] = get兵力()
    start.innerText = 可扩展地块.length+'|'
    start.style.color = lightblue

    const [l, r] = 可扩展地块[0] || []
    if (l && $$(".center-vertical,.center-horizontal").length === 0) {
      log(1,l)
      backItem = $(".selected");
      d && log('备份', backItem?.idx, $('.selected')?.idx)

      clickDom(l);
      d && log('移动auto',  backItem?.idx, $('.selected')?.idx, l.idx)
      
      移动(l, r);
      // l.classList.remove("selected");
      // r.classList.remove("selected");
      
      clickDom(backItem)
      d && log('还原',  backItem?.idx, $('.selected')?.idx)
      
      // selectedItemBackup ? clickDom(selectedItemBackup) : r.classList.remove('selected')
    }
  
    
    function get可扩展空地() {
      return (
          base.filter((e) => e.innerHTML >= 2 )
          .filter((e) => e.innerHTML <= 10)
          .sort((q, w) => q.innerText - w.innerText)
          .slice(0, -1) // 过滤主力部队
          .flatMap((e) => e.nodes.filter(is空地).map((ee) => [e, ee]))
      );
    }
    function get可扩展敌人() {
      return (
        base.sort((q, w) => q.innerText - w.innerText)
          .slice(0, -1) // 过滤主力部队
          .flatMap((e) => e.nodes.filter(敌人=>is敌人(敌人) && (Number(e.innerText) >= Number(敌人.innerText) + 2)).map((ee) => [e, ee]))
      );
    }
    
  },10,'s')
    
  useInterval(
    () => {
    },
    10,
    "自动移动feat"
  );
}

function clickDom(dom) {
  if (!dom) return;

  dom.dispatchEvent(
    new MouseEvent("mousedown", {
      bubbles: true,
    })
  );
  dom.dispatchEvent(
    new MouseEvent("mouseup", {
      bubbles: true,
    })
  );
}
function 移动(l, r) {
  key(计算方向(l, r));

  function 计算方向(l, r) {
    if (typeof l !== "number") l = l.idx;

    if (typeof r !== "number") r = r.idx;

    if (r > l) {
      if (r == l + 1) return D;

      return S;
    }

    if (r + 1 == l) return A;

    return W;
  }

}

function key(k) {
  document.dispatchEvent(
    new KeyboardEvent("keydown", {
      keyCode: typeof k === 'number' ? k : k.toUpperCase().charCodeAt(0),
      bubbles: true,
    })
  );
}
function useCss(textContent) {
  const style = document.createElement("style");
  style.type = "text/css";
  style.textContent = textContent;
  document.head.appendChild(style);
}

let 突出敌人动向 = new Set();
function obDom(dom) {
  const config = {
    childList: true,
    subtree: true,
    characterData: true,
    characterDataOldValue: true,
  };

  const callback = ([MutationRecord]) => {
    if (!is敌人(dom)) return;

    if (!dom.innerText) return;
    //fog

    if (MutationRecord.target.nodeValue - MutationRecord.oldValue === 1) return;
    //25和回合自动增加1

    const 敌军 = $$("#gameMap ." + 敌人颜色);
    const max = Math.max(...敌军.map((e) => e.innerText));
    if (dom.innerText != max) return;

    突出敌人动向.add(dom);
    setTimeout(() => {
      突出敌人动向.delete(dom);
    }, 2000);
  };
  dom.o?.disconnect();
  dom.o = new MutationObserver(callback);
  dom.o.observe(dom, config);
}

function get兵力() {
  const [陆地1, 军队1, 名字1, star1] = [
    ...$("#game-leaderboard > tbody > tr:nth-child(2)").children,
  ].reverse();
  const [陆地2, 军队2, 名字2, star2] = [
    ...$("#game-leaderboard > tbody > tr:nth-child(3)").children,
  ].reverse();

  const [我的名字, 我的军队, 我的陆地, 敌人名字, 敌人军队, 敌人陆地, start] =
    名字1.innerText.includes(我的用户名)
      ? [名字1, 军队1, 陆地1, 名字2, 军队2, 陆地2, star1]
      : [名字2, 军队2, 陆地2, 名字1, 军队1, 陆地1, star2];

  return [我的名字, 我的军队, 我的陆地, 敌人名字, 敌人军队, 敌人陆地, start];
}
function obDom2(dom = document.querySelector("#game-leaderboard")) {
  const callback = (MutationRecords, MutationObserver) => {
    const [我的名字, 我的军队, 我的陆地, 敌人名字, 敌人军队, 敌人陆地] =
      get兵力();

    let 我的军队change = 0;
    let 我的陆地change = 0;
    let 敌人军队change = 0;
    let 敌人陆地change = 0;

    const 回合 = $("#turn-counter").innerHTML;
    const is增长回合 = !回合.includes(".");
    const is25回合 = 回合.slice(5) % 25 === 0;
    const 我的塔数量 = Number(
      $$(".general, .city").filter((e) => e.classList.contains(我的颜色)).length
    );
    const 我的自然增长 = is增长回合
      ? 我的塔数量 + (is25回合 ? Number(我的陆地.innerText) : 0)
      : 0;
    const 敌人塔数量 = Number(
      $$(".general, .city").filter((e) => e.classList.contains(敌人颜色)).length
    );
    const 敌人自然增长 = is增长回合
      ? 敌人塔数量 + (is25回合 ? Number(敌人陆地.innerText) : 0)
      : 0;

    MutationRecords.forEach((m) => {
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
    });

    if (敌人军队change < -30 && 我的军队change > -20) {
        if(敌人军队.innerText == 0) return //hack

        const dom = $("#game-leaderboard > tbody > tr:nth-child(1) > td:nth-child(2)")
        if(dom.innerText === 'Player') dom.innerText = ''

        dom.innerText += Math.abs(敌人军队change)+',' // TODO 闪烁
        flash(dom)
        log(
          `%c${敌人军队change} `,
          "font-size:50px",
          $("#turn-counter").innerText,敌人军队.innerText
        );
    }

  };

  dom.o?.disconnect();
  dom.o = new MutationObserver(callback);
  dom.o.observe(dom, {
    childList: true,
    subtree: true,
    characterData: true,
    characterDataOldValue: true,
  });
}

    function flash(dom) {
      let flag = 0
      const timer = setInterval(()=>{
        flag++
        dom.style.color = (flag%2) ? 'white' : 'black'
        dom.style.background = (flag%2) ? 'black' : 'white'
      }, 100)

      setTimeout(()=>clearInterval(timer), 1500)
    }
obDomRoot();
const utils = {
  比较兵力(l, r) {
    return Number(l.innerText) >= Number(r.innerText);
  },
  添加样式(dom, style) {
    $$("." + style).forEach((dom) => dom.classList.remove(style));
    dom.classList.add(style);
    // todo 闪烁
    // setTimeout(()=>{
    //     dom.classList.remove(style)
    // }, 400)
  },
};
const get = {
  所有敌人() {
    return $$("#gameMap ." + 敌人颜色).map((e) => e.innerText);
  },
};
let gameStaus3 = false;

let 所有对局;
let 输了;
let 当前回合;
let 我的基地;
let 敌人基地;
function obDomRoot() {
  const config = {
    childList: true,
    subtree: true,
    characterData: true,
    characterDataOldValue: true,
  };

  const callback = (MutationRecords) => {
    // 游戏结束
    if ($(".alert.center")) {
      if (gameStaus3 === true) {
        gameStaus3 = false;
        gameEnd();
      }

      // 输了自动邀请
      // 输了 && $('.game-end-alert button').click()
    }
    // 游戏开始
    else if ($(".general")) {
      if (gameStaus3 === false) {
        gameStaus3 = true;
        gameStart();
      }

      // 游戏中
      MutationRecords.forEach((m) => {
        if (0) {
        }
        // 回合
        else if (isBelong(m, "#turn-counter")) 回合feat();
        // 记分板
        else if ($("#game-leaderboard").contains(m.target)) 兵力diff();
        // 棋盘
        else if ($("#gameMap").contains(m.target)) ddd(m);
        else {
          // log(m)
        }
      });
    }
  };

  document.o?.disconnect();
  document.o = new MutationObserver(callback);
  document.o.observe(document, config);

  function ddd(m) {
    const { parentNode } = m.target;

    if (is敌人(parentNode)) {
      if (utils.比较兵力(parentNode, 我的基地)) {
        // log('发现危险分子')
        // utils.添加样式(parentNode, '可被吃掉')
      }

      const 所有敌人 = get.所有敌人();
      const 敌人主力 = Math.max(...所有敌人);
      if (
        Number(parentNode.innerText) === 敌人主力 &&
        所有敌人.filter((e) => e == 敌人主力).length === 1
      ) {
        utils.添加样式(parentNode, "敌人主力");
      }

      // 发现基地
      if (parentNode.classList.contains("general")) {
        withStyle(parentNode, { zIndex: '111', outline: "8px solid red" });
      }
    }
  }
}

const clearDoms = new Set()

function gameEnd() {
  输了 =
    document.querySelector("#game-page > div.alert.center > center > h1")
      .innerText !== "You won!";
  addGameResult();
}

function withStyle(dom, style) {
  clearDoms.add(dom);

  Object.entries(style).forEach(([k, v]) =>{
    dom.style.setProperty(k, v, "important");
  });
}

function isB(MutationRecords, selector) {
  const childs = [...$(selector).childNodes];
  return MutationRecords.some((m) => childs.includes(m.target));
}
function isBelong({ target }, selector, fn) {
  return target.parentNode === $(selector);
}
function addGameResult() {
  所有对局.push({
    type: "1v1",
    ranking: 输了
      ? [
          {
            name: get兵力().map((e) => e.innerText)[3],
          },
          {
            name: 我的用户名,
          },
        ]
      : [
          {
            name: 我的用户名,
          },
          {
            name: get兵力().map((e) => e.innerText)[3],
          },
        ],
  });
}
function gameStart() {
  console.clear();

  // clear上盘数据
  clearDoms.forEach((dom) => (dom.style = null));
  $("#game-leaderboard > tbody > tr:nth-child(1) > td:nth-child(2)").innerHTML = ''

  log(
    "地图大小",
    $("#gameMap tbody tr").childElementCount,
    $("#gameMap tbody").childElementCount
  );

  我的基地 = $(`.general`);
  我的颜色 = 我的基地.classList.contains("red") ? "red" : "lightblue";
  敌人颜色 = 我的颜色 === "red" ? "lightblue" : "red";

  const [我的名字, 我的军队, 我的陆地, 敌人名字] = get兵力();
  我的名字.style.setProperty("background-color", "#4363d8", "important");
  敌人名字.style.setProperty("background-color", "red", "important");

  胜率feat();
  自动移动feat();

  $(".chat-messages-container").classList.add("minimized"); // 聊天窗口缩小

  setTimeout(() => {
    document.documentElement.style.setProperty(
      "--scale",
      document.body.clientHeight / $(".relative").offsetHeight
    );
  }, 500);
}

let 当前回合flag; // 防止无限循环
function 兵力diff() {
  if (当前回合flag === $("#turn-counter").innerText.slice(5)) return;
  当前回合flag = $("#turn-counter").innerText.slice(5);

  const [我的名字, 我的军队, 我的陆地, 敌人名字, 敌人军队, 敌人陆地] =
    get兵力().map((e) => Number(e.innerText));

  f($("#game-leaderboard td:nth-child(3)"), 我的军队, 敌人军队);
  f($("#game-leaderboard td:nth-child(4)"), 我的陆地, 敌人陆地);

  function f(dom, l, r) {
    if (r == 0) return;
    //hack 赢了的时候是0

    dom.innerHTML = g(l, r);
    dom.style.color = "white";
    dom.style.background = l > r ? lightblue : "red";
  }
  function g(l, r) {
    const diff = l - r;
    const per = ((diff / l) * 100).toFixed()
    // return `${per}(${Math.abs(diff)})`;
    return `${per}%<span style="font-size:10px">${Math.abs(diff)}个</span>`
  }
}

function 回合feat() {
  // 25爆兵
  // 20吞兵
  // 10出击
  // 10收集
  createDom();

  const 回合 = $("#turn-counter")
  当前回合 = 回合.innerText.slice(5);
  const x = 当前回合 * 2 + (当前回合.includes(".") ? 1 : 0);
  const w = (x % 50) * 2 + 1
  document.documentElement.style.setProperty("--width", w + '%');

  const dom = $("#game-leaderboard > tbody > tr:nth-child(1) > td:nth-child(1) > span > span")
  dom.innerText = 50 - (x % 50)
  Object.assign(dom.style, {
    color: 'white',
    background: 'black',
    fontweight: 900,
    fontsize: '21px',
    textalign: 'center'
  })

  function createDom() {
    $("#turn-flag") ||
      $("#game-page").insertAdjacentHTML(
        "beforeend",
        `<div id="turn-flag"></div>`
      );
    // 元素最后
  }
}

async function 胜率feat() {
  所有对局 = 所有对局 || (await 获取所有对局());
  const 对方用户名 = $$("td.leaderboard-name")
    .map((e) => e.innerText)
    .find((e) => !e.includes(我的用户名));
  const 总对局 = 所有对局
    .filter((e) => e.type === "1v1")
    .filter((e) => e.ranking.some((ee) => ee.name === 对方用户名));
  const 胜利 = 总对局.filter((e) => e.ranking[0].name.includes(我的用户名));
  const 胜率 = ((胜利.length / 总对局.length) * 100).toFixed(1) + "%";
  log("胜率", 总对局.length, 胜率);

  async function 获取所有对局() {
    let count = 50;
    let offset = -count;
    const datas = [];

    async function fetchData() {
      const p = fetch(
        `https://generals.io/api/replaysForUsername?u=zem2821&offset=${(offset +=
          count)}&count=${count}`
      );
      const data = await (await p).json();
      datas.push(...data);
      if (data.length === count) await fetchData();

      return datas;
    }

    return await fetchData();
  }
}

```
