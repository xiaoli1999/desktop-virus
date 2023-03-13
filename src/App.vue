<template>
    <h2>欢迎来到采黎桌面</h2>
    <main>
        <div v-for="(item, index) in appList" :key="index" class="main" :title="item.desc" @click="handelApp(index)">
            <img :src="item.img" alt="">
            <div>{{ item.name }}</div>
        </div>
    </main>
    <footer>
        <div class="f-left">
            <img class="f-left-w" src="https://cdn.xiaoli.vip/img/desktop-virus/windows.png" alt="开始" title="开始" @click="handelApp(0)">
            <img class="footer-line" src="https://cdn.xiaoli.vip/img/desktop-virus/line.png" alt="">
        </div>
        <div class="f-content">
            <div v-for="(item, index) in openAppList" :key="index" :class="index === openAppList.length - 1 ? 'active' : ''" @click="handelApp(index)">
                <img :src="item.img" alt="">
            </div>
        </div>
        <div class="f-right">
            <img class="footer-line" src="https://cdn.xiaoli.vip/img/desktop-virus/line.png" alt="">
            <div class="f-right-arrow footer-bg" title="显示隐藏的图标" @click="handelApp(4)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/arrow-top.png" alt="">
            </div>
            <div class="f-right-wx footer-bg" title="微信" @click="handelApp(4)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/wx.png" alt="">
            </div>
            <div class="f-right-vol footer-bg" title="音量" @click="handelApp(2)">
                <img class="f-right-vol" src="https://cdn.xiaoli.vip/img/desktop-virus/vol.png" alt="">
            </div>
            <div class="f-right-z footer-bg" title="中/英文" @click="handelApp(5)">中</div>
            <div class="f-right-sg footer-bg" title="搜狗拼音输入法" @click="handelApp(5)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/sougou.png" alt="">
            </div>
            <div class="f-right-time footer-bg" :title="dateData.week">
                <span>{{ `${ dateData.h }:${ dateData.m }:${ dateData.s }` }}</span>
                <span>{{ `${ dateData.year }/${ dateData.month }/${ dateData.day }` }}</span>
            </div>
            <div class="f-right-msg footer-bg" title="没有新通知" @click="handelApp(1)">
                <img class="f-right-msg" src="https://cdn.xiaoli.vip/img/desktop-virus/msg.png" alt="">
            </div>
        </div>
    </footer>
    <img class="sg" src="https://cdn.xiaoli.vip/img/desktop-virus/sg.png" alt="" @click="handelApp(5)" />
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

let innerSize: { w: number, h: number } = { w: 0, h: 0 }
const getInnerSize = () => ({ w: window.innerWidth, h: window.innerHeight })


const appList = ref<{ name: string, img: string, desc: string }[]>([
    { name: 'Chrome', desc: 'Google chrome', img: 'https://cdn.xiaoli.vip/img/desktop-virus/chrome.png' },
    { name: 'WebStorm', desc: 'WebStorm 2022.3.2', img: 'https://cdn.xiaoli.vip/img/desktop-virus/Webstorm.png' },
    { name: 'Vscode', desc: 'Visual Studio Code', img: 'https://cdn.xiaoli.vip/img/desktop-virus/vscode.png' },
    { name: 'HBuilderX', desc: 'HBuilderX', img: 'https://cdn.xiaoli.vip/img/desktop-virus/HBuilder.png' },
    { name: '微信', desc: '微信', img: 'https://cdn.xiaoli.vip/img/desktop-virus/wx.png' },
    { name: '搜狗输入法', desc: '搜狗输入法', img: 'https://cdn.xiaoli.vip/img/desktop-virus/sougou.png' }
])

const openAppList = ref(appList.value.slice(0, 2))

let isClicked = false
let virusTimer: any = null

const createVirus = (item) => {
    const virus = document.createElement('div')
    console.log(innerSize.w)
    const leftRange = innerSize.w > 500 ? [8, innerSize.w - 368] : [4, innerSize.w - 220]
    const topRange = innerSize.w > 500 ? [8, innerSize.h - 232] : [4, innerSize.h - 140]
    const left = leftRange[0] + Math.round(Math.random() * leftRange[1])
    const top = topRange[0] + Math.round(Math.random() * topRange[1])

    virus.className = 'virus'
    virus.innerHTML = `
        <div class="virus">
            <div class="virus-title">Microsoft unknown error</div>
            <div class="virus-desc">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/error.png" alt="" />
                An unknown error occurred in ${ item.name }
            </div>
            <div class="virus-panel">
                <div>确定</div>
            </div>
        </div>
    `
    virus.style.left = left + 'px'
    virus.style.top = top + 'px'
    return virus
}
const startVirus = (item) => {
    const virus = createVirus(item)
    document.body.append(virus)
}

const handelApp = (i = 0) => {
    if (isClicked) return

    isClicked = true
    const item = appList.value[i]
    let num = 0
    clearInterval(virusTimer)

    virusTimer = setInterval(() =>  {
        /* 处理桌面、控制栏图标 */
        if (num < 200) {
            appList.value.push(item)
            openAppList.value.push(item)
        }

        /* 弹出警告弹窗 */
        if (num > 60) {
            startVirus(item)
        }

        /* 清除定时器 */
        if (num >= 300) clearInterval(virusTimer)
        num++
    }, 10)

}



/**
 * @function addZero 补零
 * @param { Number } num
 * @return { String | Number } 返回补零后的值
 */
const addZero = (num = 0) => (num < 10 ? `0${num}` : num)

const weekName = { 1: '星期一', 2: '星期二', 3: '星期三', 4: '星期四', 5: '星期五', 6: '星期六', 0: '星期日' }

/**
 * @function getDate 获取当前日期时间
 * @return { { year: Number,  month: Number | String,  day: Number | String, week: String, h: Number | String, m: Number | String,  s:Number | String } } 返回日期时间
 */
const getDate = (): any => {
    const date = new Date()
    const year = date.getFullYear()
    const month = date.getMonth() + 1
    const day = date.getDate()
    const week = weekName[date.getDay()]
    const h = addZero(date.getHours())
    const m = addZero(date.getMinutes())
    const s = addZero(date.getSeconds())
    return { year, month, day, week, h, m, s }
}

let dateTimer: any = null
const dateData = ref({ year: 2023, month: 3, day: 13, week: '', h: '00', m: '00', s: '00' })
const setDate = () => {
    clearInterval(dateTimer)
    dateTimer = setInterval(() => {
        /* 获取设备尺寸 */
        innerSize = getInnerSize()

        /* 设置日期时间 */
        dateData.value = getDate()
    }, 1e3)
}

onMounted(() => {
    /* 日期时间、设备尺寸 */
    dateData.value = getDate()
    setDate()

})
console.log('%c 整蛊桌面🌈 | 黎 | https://xiaoli1999.github.io/desktop-virus ', 'color: #f4f4f4;background: #444; padding:5px 0;border-radius:2px;')
</script>

<style lang="less">
//底部高亮 #655653

.transition {
    transition: all .12s;
}

.flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

h2 {
    padding: 24px 0;
    color: #f2f4fa;
    text-align: center;
    letter-spacing: 2px;
}

main {
    box-sizing: border-box;
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    font-size: 14px;
    color: #d5e3ef;
    padding: 0 12px;

    .main {
        box-sizing: border-box;
        width: 88px;
        padding: 4px;
        border: 1px solid transparent;
        .transition;

        &:hover {
            background: #6b81a780;
            box-shadow: 0 0 2px #879bbc80;
            //border-radius: 2px;
            border: 1px solid #879bbc;
        }

        > img {
            width: 36px;
            height: 36px;
            margin: 0 auto;
        }

        > div {
            text-align: center;
            line-height: 28px;
        }


    }
}

.footer-bg {
    padding: 0 4px;
    .transition;

    &:hover {
        background: #655653;
    }
}

footer {
    position: fixed;
    width: 100%;
    height: 48px;
    bottom: 0;
    left: 0;
    z-index: 999;
    background: #2e242560;
    display: flex;
    align-items: center;
    backdrop-filter: blur(1px);
    .transition;

    &:hover {
        background: #2e242599;
    }

    .footer-line {
        position: relative;
        width: 8px;
        height: 20px;
        cursor: w-resize;
        top: 1px;
    }

    .f-left {
        flex-shrink: 0;
        display: flex;
        align-items: center;
        padding: 0 12px;

        .f-left-w {
            width: 24px;
            height: 24px;
            margin-right: 12px;

            &:hover {
                opacity: .76;
            }
            .transition;
        }

        .f-left-line {
            width: 12px;
            height: 24px;
        }
    }

    .f-content {
        flex: 1;
        display: flex;
        align-items: center;
        overflow-x: auto;

        > div {
            padding: 0 12px;
            height: 48px;
            .flex-center;
            .transition;

            &:hover,
            &.active {
                background: #00000048;
            }

            > img {
                width: 24px;
            }
        }
    }

    .f-right {
        flex-shrink: 0;
        display: flex;
        align-items: center;
        padding: 0 0 0 12px;

        .f-right-arrow {
            height: 48px;
            margin-left: 12px;
            .flex-center;

            > img {
                width: 16px;
                height: 18px;
            }
        }

        .f-right-wx {
            height: 48px;
            .flex-center;

            > img {
                width: 18px;
            }
        }

        .f-right-vol {
            height: 48px;
            .flex-center;

            > img {
                position: relative;
                width: 16px;
                height: auto;
                top: 1px;
            }
        }

        .f-right-z {
            height: 48px;
            font-size: 15px;
            color: #fff;
            .flex-center;

        }

        .f-right-sg {
            height: 48px;
            .flex-center;

            > img {
                width: 18px;
                height: auto;
                margin: 0 8px;
            }
        }

        .f-right-time {
            height: 48px;
            .flex-center;
            flex-direction: column;
            color: #f2f4fa;
            font-size: 12px;
            letter-spacing: 1px;
            padding: 0 12px;

            > span {
                display: block;
            }

            > span:first-child {
                letter-spacing: 0;
                margin-bottom: 6px;
            }
        }

        .f-right-msg {
            height: 48px;
            .flex-center;

            > img {
                width: 18px;
                height: auto;
                margin: 0 8px;
            }
        }
    }
}

.sg {
    position: fixed;
    width: 180px;
    bottom: 88px;
    right: 60px;
    z-index: 999;
    mix-blend-mode: lighten;
    opacity: .9;
    cursor: pointer;
}

.virus {
    position: fixed;
    z-index: 999;
    width: 360px;
    height: 176px;
    background: #fff;
    //border: 1px solid #000;
    box-shadow: 4px 4px 8px 1px #00000048;
    border-radius: 2px;
    overflow: hidden;

    .virus-title {
        height: 42px;
        line-height: 42px;
        padding: 0 8px;
        font-size: 18px;
        border-bottom: 1px solid #eee;
    }

    .virus-desc {
        display: flex;
        align-items: center;
        padding: 30px 8px;
        font-size: 14px;
        color: red;
        font-weight: 600;

        > img {
            width: 24px;
            margin-right: 12px;
        }
    }

    .virus-panel {
        height: 48px;
        display: flex;
        align-items: center;
        justify-content: flex-end;
        background: #e5e5e5;

        > div {
            padding: 4px 12px;
            font-size: 14px;
            background: #fff;
            box-shadow: 1px 1px 4px 1px #666;
            margin-right: 12px;
            border-radius: 1px;
        }
    }
}

@media only screen and (max-width: 500px) {
    h2,
    footer,
    .virus {
        transform: scale(.6);
    }
}
</style>
