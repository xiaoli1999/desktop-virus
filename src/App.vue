<!--
  项目介绍
  名称: 前端桌面
  版本: v0.1.0
  体验:
      1. 打开应用
      2. 点赞＋收藏🙏🙏🙏
  技术栈: vue3、ts、less
 -->
<template>
    <div :class="showMask ? 'mask active' : 'mask'">
        <div class="loading"></div>
    </div>
    <h3 :class="showMask ? '' : 'active'">欢迎来到采黎桌面</h3>
    <main>
        <div v-for="(item, index) in appList" :key="index" class="main" :title="item.desc" @click="openApp(index)">
            <img :src="item.img" alt="">
            <div>{{ item.name }}</div>
        </div>
    </main>
    <footer>
        <div class="f-left">
            <img class="f-left-w" src="https://cdn.xiaoli.vip/img/desktop-virus/windows.png" alt="开始" title="开始" @click="openApp(0)">
            <img class="footer-line" src="https://cdn.xiaoli.vip/img/desktop-virus/line.png" alt="">
        </div>
        <div class="f-content">
            <div v-for="(item, index) in openAppList" :key="index" :class="index === openAppList.length - 1 ? 'active' : ''" @click="openApp(index)">
                <img :src="item.img" alt="">
            </div>
        </div>
        <div class="f-right">
            <img class="footer-line" src="https://cdn.xiaoli.vip/img/desktop-virus/line.png" alt="">
            <div class="f-right-arrow footer-bg" title="显示隐藏的图标" @click="openApp(4)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/arrow-top.png" alt="">
            </div>
            <div class="f-right-wx footer-bg" title="微信" @click="openApp(4)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/wx.png" alt="">
            </div>
            <div class="f-right-vol footer-bg" title="音量" @click="openApp(2)">
                <img class="f-right-vol" src="https://cdn.xiaoli.vip/img/desktop-virus/vol.png" alt="">
            </div>
            <div class="f-right-z footer-bg" title="中/英文" @click="openApp(5)">中</div>
            <div class="f-right-sg footer-bg" title="搜狗拼音输入法" @click="openApp(5)">
                <img src="https://cdn.xiaoli.vip/img/desktop-virus/sougou.png" alt="">
            </div>
            <div class="f-right-time footer-bg" :title="dateData.week">
                <span>{{ `${ dateData.h }:${ dateData.m }:${ dateData.s }` }}</span>
                <span>{{ `${ dateData.year }/${ dateData.month }/${ dateData.day }` }}</span>
            </div>
            <div class="f-right-msg footer-bg" title="没有新通知" @click="openApp(1)">
                <img class="f-right-msg" src="https://cdn.xiaoli.vip/img/desktop-virus/msg.png" alt="">
            </div>
        </div>
    </footer>
    <img class="sg" src="https://cdn.xiaoli.vip/img/desktop-virus/sg.png" alt="" @click="openApp(5)" />
    <div id="virusList"></div>
    <div :class="showDialog ? 'dialog active' : 'dialog'">
        <div class="dialog-content">
            <div class="dialog-title">采黎有言</div>
            <div class="dialog-desc">
                愿大家在工作之余能些许放松，劳逸结合。<br />
                提前祝大家愚人节快乐，<b>一键点赞+收藏！</b> <br />
                桌面会在3s后恢复正常哦。
            </div>
        </div>
    </div>
    <template v-if="appSrc">
        <img :src="appSrc" :class="openAppLoading ? 'open-app active' : 'open-app'" alt="" @click="handelApp">
        <div v-show="openAppLoading" class="loading" :class="openAppLoading ? 'open-loading active' : 'open-loading'"></div>
    </template>

</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

let innerSize: { w: number, h: number } = { w: 0, h: 0 }
const getInnerSize = () => ({ w: window.innerWidth, h: window.innerHeight })

const showMask = ref(true)
const loaded = () => setTimeout(() => showMask.value = false, 2400)

const appList = ref<{ name: string, img: string, desc: string, app: string }[]>([
    { name: 'Chrome', desc: 'Google chrome', img: 'https://cdn.xiaoli.vip/img/desktop-virus/chrome.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/chrome.jpeg' },
    { name: 'WebStorm', desc: 'WebStorm 2022.3.2', img: 'https://cdn.xiaoli.vip/img/desktop-virus/Webstorm.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/Webstorm.jpg' },
    { name: 'Vscode', desc: 'Visual Studio Code', img: 'https://cdn.xiaoli.vip/img/desktop-virus/vscode.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/vscode.jpg' },
    { name: 'HBuilderX', desc: 'HBuilderX', img: 'https://cdn.xiaoli.vip/img/desktop-virus/HBuilder.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/HBuilder.jpg' },
    { name: '微信', desc: '微信', img: 'https://cdn.xiaoli.vip/img/desktop-virus/wx.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/wx.png' },
    { name: '搜狗输入法', desc: '搜狗输入法', img: 'https://cdn.xiaoli.vip/img/desktop-virus/sougou.png', app: 'https://cdn.xiaoli.vip/img/desktop-virus/app/sougou.jpeg' }
])

const openAppList = ref(appList.value.slice(0, 2))
const appSrc = ref('')
const openAppLoading = ref(false)
const currentIndex = ref(0)

let isClicked = false
let virusTimer: any = null
const showDialog = ref(false)

/**
 * @function createVirus 创建病毒弹窗
 * @param { Object } item
 * @return { Object } virusElement 返回dom节点
 */
const createVirus = (item) => {
    const virusElement = document.createElement('div')
    const leftRange = innerSize.w > 500 ? [8, innerSize.w - 368] : [4, innerSize.w - 224]
    const topRange = innerSize.w > 500 ? [52, innerSize.h - 284] : [52, innerSize.h - 216]
    const left = leftRange[0] + Math.round(Math.random() * leftRange[1])
    const top = topRange[0] + Math.round(Math.random() * topRange[1])

    virusElement.className = 'virus'
    virusElement.innerHTML = `
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
    virusElement.style.left = left + 'px'
    virusElement.style.top = top + 'px'
    return virusElement
}
const startVirus = (item) => {
    const virus = createVirus(item)
    const virusList: any = document.getElementById('virusList')
    virusList.append(virus)
}

const openApp = (i = 0) => {
    currentIndex.value = i
    const item = appList.value[i]

    appSrc.value = item.app
    openAppLoading.value = true

    setTimeout(() => openAppLoading.value = false, 1600)
}

const handelApp = () => {
    if (isClicked || openAppLoading.value) return

    isClicked = true

    const item = appList.value[currentIndex.value]

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
        if (num >= 340) {
            clearInterval(virusTimer)
            setTimeout(() => showDialog.value = true, 1600)
            setTimeout(() => {
                showDialog.value = false

                /* 清除dom */
                const virusList: any = document.getElementById('virusList')
                virusList.innerHTML = ''
                appList.value = appList.value.slice(0, 6)
                openAppList.value = openAppList.value.slice(0, 2)

                /* 关闭应用 */
                appSrc.value = ''

                /* 开启点击 */
                isClicked = false
            }, 7600)
        }
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
    /* 初始化加载 */
    loaded()

    /* 加载背景 */
    document.body.style.background = '#2e2425 url("https://cdn.xiaoli.vip/img/desktop-virus/rise.jpg") no-repeat center'

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

.mask {
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    z-index: -1;
    opacity: 0;
    background: #000000bb;
    filter: blur(2px);
    transition: all .68s linear;
    .flex-center;

    &.active {
        opacity: 1;
        z-index: 1000;
    }
}

.loading {
    width: 54px;
    aspect-ratio: 1;
    border-radius: 50%;
    margin: 12px;
    background:
            radial-gradient(farthest-side,#fff 94%,#0000) top/6px 6px no-repeat,
            conic-gradient(#0000 30%, #fff);
    -webkit-mask: radial-gradient(farthest-side, #0000 calc(100% - 6px), #000 0);
    animation: loading 1s infinite linear;
}

@keyframes loading {
    to {
        rotate: 1turn;
    }
}

h3 {
    padding: 24px 0;
    color: #f4f7fa;
    font-size: 24px;
    text-align: center;
    letter-spacing: 2px;
    transition: all .68s linear;
    transform: translateY(-68px);
    font-family: fangsong, sans-serif;

    &.active {
        transform: translateY(0);
    }
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
    height: 174px;
    background: #fff;
    box-shadow: 4px 4px 8px 1px #00000048;
    border-radius: 2px;
    overflow: hidden;

    .virus-title {
        box-sizing: border-box;
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
        background: #fff;

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

.dialog {
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    z-index: 1000;
    background: transparent;
    transition: all .24s linear;
    transform: scale(0);
    .flex-center;

    .dialog-content {
        width: 480px;
        background: #fff;
        padding: 8px 16px;
        margin-bottom: 120px;
        border-radius: 4px;
        box-shadow: 1px 1px 8px #00000040;
        letter-spacing: 1px;
        transition: all .48s linear;

        .dialog-title {
            line-height: 42px;
            font-size: 18px;
            color: #000;
        }

        .dialog-desc {
            padding: 0 8px;
            font-size: 14px;
            line-height: 20px;
            color: #232323;

            > b {
                color: #1e80ff;
            }
        }
    }

    &.active {
        background: #00000080;
        transform: scale(1);
    }
}

.open-app {
    position: fixed;
    max-width: 80%;
    max-height: 80%;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    margin: auto;
    border-radius: 8px;
    box-shadow: 2px 2px 16px 2px #00000048;
    transition: all .48s linear;
    z-index: 100;

    &.active {
        background: #000000cc;
        filter: blur(3px);
    }
}

.open-loading {
    .loading;

    position: fixed;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    margin: auto;
    transition: all .48s linear;
    background:
            radial-gradient(farthest-side,#1e80ff 94%,#0000) top/6px 6px no-repeat,
            conic-gradient(#0000 30%, #1e80ff);
    -webkit-mask: radial-gradient(farthest-side, #0000 calc(100% - 6px), #000 0);
    z-index: 100;

    &.active {
        filter: blur(3px);
    }
}

@media only screen and (max-width: 500px) {
    main {
        font-size: 12px;
        padding: 0 8px;

        .main {
            width: 70px;
            padding: 3px;

            > img {
                width: 32px;
                height: 32px;
            }

            > div {
                line-height: 24px;
            }


        }
    }

    .sg {
        position: fixed;
        width: 88px;
        bottom: 68px;
        right: 20px;
    }

    .virus {
        width: 216px;
        height: 106px;

        .virus-title {
            height: 24px;
            line-height: 24px;
            padding: 0 4px;
            font-size: 14px;
        }

        .virus-desc {
            height: 54px;
            padding: 0 4px;
            font-size: 12px;
            line-height: 18px;

            > img {
                width: 14px;
                margin-right: 8px;
            }
        }

        .virus-panel {
            height: 28px;

            > div {
                padding: 3px 6px;
                font-size: 12px;
                box-shadow: 1px 1px 4px 1px #666;
                margin-right: 8px;
            }
        }
    }

    .dialog {

        .dialog-content {
            width: 320px;
            background: #fff;
            padding: 8px;
        }
    }
}
</style>
