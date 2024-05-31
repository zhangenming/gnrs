// ==UserScript==
// @name         New Userscript
// @namespace    http://tampermonkey.net/
// @version      2024-05-28
// @description  try to take over the world!
// @author       You
// @match        https://generals.io/
// @icon         https://www.google.com/s2/favicons?sz=64&domain=generals.io
// @grant        none
// ==/UserScript==

// 当前方块颜色
const style = document.createElement("style")
style.type = "text/css"
style.textContent = ".selected  {border: 10px solid black!important; }"
document.head.appendChild(style)

function init(){
    if (zuobiao.size) return //clearInterval(timer)

    const 棋盘宽度 = $("#gameMap tbody tr").childElementCount
    const 棋盘高度 = $("#gameMap tbody").childElementCount

    $$("#gameMap td").forEach((dom, idx, arr) => {
        const line = Math.floor(idx / 棋盘宽度)
        const row = idx % 棋盘宽度

        const left = row !== 0 && arr[idx - 1]
        const right = row !== 棋盘宽度 - 1 && arr[idx + 1]
        const top = line !== 0 && arr[idx - 棋盘宽度]
        const bottom = line !== 棋盘高度 - 1 && arr[idx + 棋盘宽度]

        zuobiao.set(dom, {
            idx,
            line,
            row,

            left,
            right,
            top,
            bottom,

            nodes: [left, right, top, bottom,].filter(e=>e)
        })
    })
}

setInterval(() => {
    if($('#gameMap')){
        zuobiao.size || init()
        if($$('.center-vertical,.center-horizontal').length==0) 自动移动()
    }else{
        zuobiao.clear()
    }

}, 100)

//
// 坐标轴
window.r = 自动移动
const zuobiao = (window.zuobiao = new Map())

function 自动移动(){
  const t = 目标()
if(!t)return
const [l, r] = t

  const 方向 = 计算方向(l, r)
  移动(l, 方向)
}

const W = 87
const A = 65
const S = 83
const D = 68

const $$ = (str) => [...document.querySelectorAll(str)]
const $ = (str) => $$(str)[0]

function clickDom(dom) {
    dom.dispatchEvent(
        new MouseEvent("mousedown", {
            bubbles: true,
        }),
    )
    dom.dispatchEvent(
        new MouseEvent("mouseup", {
            bubbles: true,
        }),
    )
}

function getData(dom, key) {
    return key ? zuobiao.get(dom)[key] : zuobiao.get(dom)
}

function 移动(dom, direction){
    console.log(dom, direction)

    clickDom(dom)

    document.dispatchEvent(new KeyboardEvent('keydown', {
        keyCode: direction,
        bubbles: true,
    }));
}

function 计算方向(l, r) {
    if (typeof l !== "number") l = getData(l, "idx")

    if (typeof r !== "number") r = getData(r, "idx")

    if (r > l) {
        if (r == l + 1) return D

        return S
    }

    if (r + 1 == l) return A

    return W
}



function 目标(){
    return $$('.neutral.selectable ')
        //.filter(e=>e.innerHTML >= 2)
        .map(e=>[e, ...getData(e, "nodes").filter(e=>e.classList.length===0)])
        .find(e=>e[0].innerHTML >= 2 && e[1])
}





const myDom = [...$$("#game-leaderboard tr:nth-child(2) td")][0]

const [myStart, myName, myArmy, myLand] = [...$$("#game-leaderboard tr:nth-child(2) td")].map((e) => e.innerText)
const [drStart, drName, drArmy, drLand] = [...$$("#game-leaderboard tr:nth-child(3) td")].map((e) => e.innerText)

const diffArmy = (((drArmy - drArmy) / myArmy) * 100).toFixed(1)



