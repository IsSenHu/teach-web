<template>
    <div class="frame">
        <div id="app">
            <el-row :gutter="2">
                <el-col :span="4">
                    <div :style="menuHeight" class="menu-left">
                        <h4 style="color: gold;">视频</h4>
                        <el-menu
                                default-active="2"
                                class="el-menu-vertical-demo"
                                @open="handleOpen"
                                @close="handleClose"
                                background-color="#545c64"
                                text-color="#fff"
                                active-text-color="#ffd04b">
                            <el-submenu index="1">
                                <template slot="title">
                                    <i class="el-icon-folder-opened"></i>
                                    <span>第一章</span>
                                </template>
                                <el-menu-item index="1-1">选项1</el-menu-item>
                                <el-menu-item index="1-2">选项2</el-menu-item>
                            </el-submenu>
                        </el-menu>
                    </div>
                </el-col>
                <el-col :span="16">
                    <div :style="menuHeight" class="menu-middle">
                      <video-player class="video-player vjs-custom-skin"
                                    ref="videoPlayer"
                                    :playsinline="true"
                                    :options="playerOptions">
                      </video-player>
                    </div>
                </el-col>
                <el-col :span="4">
                    <div :style="menuHeight" class="menu-right">

                    </div>
                </el-col>
            </el-row>
        </div>
    </div>
</template>

<script>
    import Vue from 'vue';
    import ElementUI from 'element-ui';
    import 'element-ui/lib/theme-chalk/index.css';
    import VideoPlayer from 'vue-video-player'

    require('video.js/dist/video-js.css');
    require('vue-video-player/src/custom-theme.css');

    Vue.use(VideoPlayer);
    Vue.use(ElementUI);
    export default {
        name: 'app',
        data() {
            return {
                menuHeight: '',
                playerOptions: {
                    playbackRates: [0.7, 1.0, 1.5, 2.0], // 播放速度
                    autoplay: false, // 如果true,浏览器准备好时开始播放
                    muted: false, // 默认情况下将会消除任何音频
                    loop: false, // 导致视频一结束就重新开始
                    preload: 'auto', // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
                    language: 'zh-CN',
                    aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
                    fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
                    sources: [{
                        type: "video/mp4",
                        src: "http://localhost:8090/video/session1.mp4"
                    }],
                    poster: "", //你的封面地址
                    notSupportedMessage: '此视频暂无法播放，请稍后再试', //允许覆盖Video.js无法播放媒体源时显示的默认信息。
                    controlBar: {
                      timeDivider: true,
                      durationDisplay: true,
                      remainingTimeDisplay: false,
                      fullscreenToggle: true  //全屏按钮
                    }
                }
            }
        },
        created() {
            const height = document.documentElement.clientHeight;
            this.menuHeight = this.menuHeight + " height: " + height * 0.92 + 'px';
        },
        methods: {
            handleOpen(key, keyPath) {
                // eslint-disable-next-line no-console
                console.log(key, keyPath)
            },
            handleClose(key, keyPath) {
                // eslint-disable-next-line no-console
                console.log(key, keyPath)
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 30px;
    }

    .menu-left, .menu-middle, .menu-right {
        border: 2px solid darkgray;
        border-radius: 5px;
        background-color: #545C64;
    }

    .video-player {
      margin-top: 60px;
    }

    .frame {
        background-color: #FFFFFF;
    }

    html {
        background-color: #545C64;
    }
</style>
