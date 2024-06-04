```js
clearInterval(window.timer2)

console.log = ()=>{}
const W = 87
const A = 65
const S = 83
const D = 68

const $$ = (str)=>[...document.querySelectorAll(str)]
const $ = (str)=>$$(str)[0]

let 开启auto = false
document.onkeydown = e=>e.code === 'Space' && (开启auto = true)
document.onkeyup = e=>e.code === 'Space' && (开启auto = false)

function initUI() {
    if ($('#game-page #ui'))
        return

    document.getElementById('game-page').insertAdjacentHTML('beforeend', `
<div id="ui" style="position: absolute;
    right: 0;
    background: #251b10;
    top: 0;
    z-index: 9999;
    font-size: 50px;
    left: 50%;
    transform: translate(-50%, -10%);
    padding: 10px;
    border-radius: 10px;
    border: 2px solid white;
    text-align: center;
    font-weight: bolder;">

    <div id="army">3 (3/4)</div>
    <div id="land">3 (3/4)</div>
</div>
`);

}
function init() {
    //clearInterval(timer)

    const 棋盘宽度 = $("#gameMap tbody tr").childElementCount
    const 棋盘高度 = $("#gameMap tbody").childElementCount

    $$("#gameMap td").forEach((dom,idx,arr)=>{
        const line = Math.floor(idx / 棋盘宽度)
        const row = idx % 棋盘宽度

        const left = row !== 0 && arr[idx - 1]
        const right = row !== 棋盘宽度 - 1 && arr[idx + 1]
        const top = line !== 0 && arr[idx - 棋盘宽度]
        const bottom = line !== 棋盘高度 - 1 && arr[idx + 棋盘宽度]

        Object.assign(dom, {
            idx,
            line,
            row,

            left,
            right,
            top,
            bottom,

            nodes: [left, right, top, bottom, ].filter(e=>e)
        })
    }
    )
}

let gameStaus = false

clearInterval(window.timer1)
window.timer1 = setInterval(()=>{
    const gameStaus2 = $('#gameMap') && !$('.game-end-alert')
    if (gameStaus === gameStaus2)
        return

    gameStaus = gameStaus2

    if (gameStaus) {
        init()
        initUI()

        // if($$('.center-vertical,.center-horizontal').length==0) 自动移动()
        window.timer3 = setInterval(()=>开启auto && 自动移动(), 100)
        myColor = $('.general.selectable').classList[0]
        drColor = myColor === 'lightblue' ? 'red' : 'lightblue'

        useCss(`
        .selected  {border: 10px solid black!important; }
        .${myColor}{background-color:#4363d8!important}
        .${drColor}{background-color:red!important}
        td{border-clor:red!important}
        `)
        useCss(``)

    } else {
        console.clear()
        兵营.clear()
        敌人兵营.clear()
        基地.clear()
        已经探索过的地图.clear()
        clearInterval(window.timer3)
        drArmyOld = 0
    }
}
, 100)

const 兵营 = new Set
const 敌人兵营 = new Set
const 基地 = new Set
const 已经探索过的地图 = new Set

let myColor
let drColor

const myDom = [...$$("#game-leaderboard tr:nth-child(2) td")][0]
// const diffArmy = (((drArmy - drArmy) / myArmy) * 100).toFixed(1)

let drArmyOld
let oldStyle
window.timer2 = setInterval(()=>{

    if (!gameStaus)
        return

    const [myStart,myName,myArmy,myLand] = [...$$("#game-leaderboard tr:nth-child(2) td")].map((e)=>e.innerText)
    const [drStart,drName,drArmy,drLand] = [...$$("#game-leaderboard tr:nth-child(3) td")].map((e)=>e.innerText)

    const armyDom = $('#ui #army')
    const armyDiff = myArmy - drArmy
    armyDom.innerText = `${armyDiff} (${myArmy} | ${drArmy})`
    armyDom.style.color = armyDiff > 0 ? 'blue' : 'red'

    if (drArmy - drArmyOld < -30) {
        console.info("%c敌人偷塔", "font-size:80px", drArmy, drArmyOld);

    }
    drArmyOld = drArmy

    $$('.city').forEach(city=>{
        兵营.add(city)
        if (city.classList.contains(drColor) || city.classList.contains('fog')) {
            敌人兵营.add(city)
        }
    }
    )
    $$('.general').forEach(g=>基地.add(g))
    $$('#gameMap td').filter(td=>!td.classList.contains('fog')).forEach(td=>已经探索过的地图.add(td))

    const q = [...兵营].map(city=>{
        return `tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){border: 5px solid yellow !important}`
    }
    )
    const w = [...敌人兵营].map(city=>{
        return `tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){outline: 10px solid yellow !important}`
    }
    )
    const e = [...基地].map(city=>{
        return `tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){outline: 10px solid black!important}`
    }
    )
    const r = [...已经探索过的地图].filter(e=>e.classList.contains('fog')).map(city=>{
        return `#gameMap tr:nth-child(${city.line + 1}) > td:nth-child(${city.row + 1}){background-color: rgba(255, 255, 255, 0.5)!important}`
    }
    )

    const newStyle = [...q, ...w, ...e, ...r].join('')
    if (oldStyle !== newStyle) {
        $('style').innerText = oldStyle = newStyle
    }
}
, 100)

const 防止多余操作 = new Set()
function 自动移动() {
    const t = 目标()
    if (!t)
        return
    const [l,r] = t

    if (防止多余操作.has(r))
        return
    防止多余操作.add(r)

    const 方向 = 计算方向(l, r)
    _移动(l, 方向)

    function 目标() {
        return $$('.neutral.selectable ')//.filter(e=>e.innerHTML >= 2)
        .map(e=>[e, ...e.nodes.filter(e=>e.classList.length === 0)]).find(e=>e[0].innerHTML >= 2 && e[1])
        // .find(e=>e[0].innerHTML >= 2 && e[1] && !已经移动.has(e[1]))
    }
    function 计算方向(l, r) {
        if (typeof l !== "number")
            l = l.idx

        if (typeof r !== "number")
            r = r.idx

        if (r > l) {
            if (r == l + 1)
                return D

            return S
        }

        if (r + 1 == l)
            return A

        return W
    }
}

function clickDom(dom) {
    dom.dispatchEvent(new MouseEvent("mousedown",{
        bubbles: true,
    }), )
    dom.dispatchEvent(new MouseEvent("mouseup",{
        bubbles: true,
    }), )
}

function _移动(dom, direction) {
    // console.info(dom, {
    //     87: '上',
    //     65: '左',
    //     83: "下",
    //     68: '右'
    // }[direction])

    clickDom(dom)

    document.dispatchEvent(new KeyboardEvent('keydown',{
        keyCode: direction,
        bubbles: true,
    }));
}
function useCss(textContent) {
    const style = document.createElement("style")
    style.type = "text/css"
    style.textContent = textContent
    document.head.appendChild(style)
}

```
