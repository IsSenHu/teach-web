<template>
    <div class="frame">
        <div id="app">
            <el-row :gutter="4">
                <el-col :span="5">
                    <div :style="menuHeight" class="menu-left">
                        <h4 style="color: deepskyblue;">视频</h4>
                        <el-menu
                                default-active="2"
                                class="el-menu-vertical-demo"
                                @open="handleOpen"
                                @close="handleClose"
                                active-text-color="#ffd04b">
                            <el-submenu v-for="item in videos" v-bind:key="item.index" :index="item.index" style="text-align: left;">
                                <template slot="title">
                                    <i class="el-icon-notebook-1"></i>
                                    <span style="font-size: 12px;">{{ item.name }}</span>
                                </template>
                                <el-submenu v-for="speak in item.speaks" v-bind:key="speak.index" :index="speak.index">
                                    <template slot="title">
                                        <i class="el-icon-headset"></i>
                                        <span style="color: deepskyblue;">{{ speak.name }}</span>
                                    </template>
                                    <el-menu-item v-for="session in speak.sessions" v-bind:key="session.index" :index="session.index" @click="handlePlay(session.index)">
                                        <template slot="title">
                                            <i class="el-icon-video-camera"></i>
                                            <span>{{ session.name }}</span>
                                        </template>
                                    </el-menu-item>
                                </el-submenu>
                            </el-submenu>
                        </el-menu>
                    </div>
                </el-col>
                <el-col :span="15">
                    <div :style="menuHeight" class="menu-middle">
                      <el-card class="player-card">
                          <video-player class="video-player vjs-custom-skin"
                                        ref="videoPlayer"
                                        :playsinline="true"
                                        :options="playerOptions"
                                        @ready="playerReadied"
                                        @timeupdate="onPlayerTimeupdate($event)">
                              >
                          </video-player>
                      </el-card>
                    </div>
                </el-col>
                <el-col :span="4">
                    <div :style="menuHeight" class="menu-right">
                        <el-card class="box-card">
                            <div slot="header" class="clearfix">
                                <span style="color: deepskyblue;">正在播放</span>
                            </div>
                            <div>
                                <span>{{ currentRecord }}</span>
                                <el-divider content-position="left">
                                    <el-link type="primary" icon="el-icon-timer" @click="playByTime">{{ lookedTime }}</el-link>
                                </el-divider>
                            </div>
                        </el-card>
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
                        src: "http://localhost:8090/video/chapter1/speak1/session1.mp4"
                    }],
                    poster: "", //你的封面地址
                    notSupportedMessage: '请选择要播放的视频', //允许覆盖Video.js无法播放媒体源时显示的默认信息。
                    controlBar: {
                      timeDivider: true,
                      durationDisplay: true,
                      remainingTimeDisplay: false,
                      fullscreenToggle: true  //全屏按钮
                    }
                },
                currentRecord: '',
                lookedTime: null,
                current: '1-1-1',
                videos: [
                    {
                        index: '1',
                        name: '基础知识第一章 健康管理概论',
                        speaks: [
                            {
                                index: '1-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '1-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '1-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '1-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '1-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '1-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '1-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '1-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '1-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '1-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '1-3-2',
                                        name: 'Session 2'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '2',
                        name: '基础知识第二章 临床医学基础知识',
                        speaks: [
                            {
                                index: '2-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '2-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '2-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '2-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '2-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '2-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '2-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '2-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '2-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '2-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '2-3-2',
                                        name: 'Session 2'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '3',
                        name: '基础知识第三章 预防医学基础知识',
                        speaks: [
                            {
                                index: '3-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '3-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '3-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '3-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '3-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '3-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '3-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '3-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '3-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '3-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '3-3-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '3-3-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '3-4',
                                name: '第四讲',
                                sessions: [
                                    {
                                        index: '3-4-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '3-4-2',
                                        name: 'Session 2'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '4',
                        name: '基础知识第四章 常见慢性病非传染性疾病',
                        speaks: [
                            {
                                index: '4-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '4-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '4-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '4-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '4-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '4-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-3-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-3-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '4-4',
                                name: '第四讲',
                                sessions: [
                                    {
                                        index: '4-4-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-4-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-4-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '4-5',
                                name: '第五讲',
                                sessions: [
                                    {
                                        index: '4-5-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-5-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-5-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '4-6',
                                name: '第六讲',
                                sessions: [
                                    {
                                        index: '4-6-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '4-6-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '4-6-3',
                                        name: 'Session 3'
                                    },
                                    {
                                        index: '4-6-4',
                                        name: 'Session 4'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '5',
                        name: '基础知识第五章 基本卫生保健',
                        speaks: [
                            {
                                index: '5-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '5-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '5-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '5-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '5-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '5-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '5-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '5-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '5-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '5-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '5-3-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '5-3-3',
                                        name: 'Session 3'
                                    },
                                    {
                                        index: '5-3-4',
                                        name: 'Session 4'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '6',
                        name: '基础知识第六章 流行病学和医学统计学基本知识',
                        speaks: [
                            {
                                index: '6-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '6-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '6-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '6-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '6-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '6-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '6-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '6-2-3',
                                        name: 'Session 3'
                                    },
                                    {
                                        index: '6-2-4',
                                        name: 'Session 4'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '7',
                        name: '基础知识第七章 健康教育学',
                        speaks: [
                            {
                                index: '7-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '7-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '7-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '7-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '7-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '7-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '7-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '7-2-3',
                                        name: 'Session 3'
                                    },
                                    {
                                        index: '7-2-4',
                                        name: 'Session 4'
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        index: '8',
                        name: '基础知识第八章 营养与食品安全',
                        speaks: [
                            {
                                index: '8-1',
                                name: '第一讲',
                                sessions: [
                                    {
                                        index: '8-1-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-1-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-1-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-2',
                                name: '第二讲',
                                sessions: [
                                    {
                                        index: '8-2-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-2-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-2-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-3',
                                name: '第三讲',
                                sessions: [
                                    {
                                        index: '8-3-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-3-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-3-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-4',
                                name: '第四讲',
                                sessions: [
                                    {
                                        index: '8-4-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-4-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-4-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-5',
                                name: '第五讲',
                                sessions: [
                                    {
                                        index: '8-5-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-5-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-5-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-6',
                                name: '第六讲',
                                sessions: [
                                    {
                                        index: '8-6-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-6-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-6-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-7',
                                name: '第七讲',
                                sessions: [
                                    {
                                        index: '8-7-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-7-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-7-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-8',
                                name: '第八讲',
                                sessions: [
                                    {
                                        index: '8-8-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-8-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-8-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-9',
                                name: '第九讲',
                                sessions: [
                                    {
                                        index: '8-9-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-9-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-9-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-10',
                                name: '第十讲',
                                sessions: [
                                    {
                                        index: '8-10-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-10-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-10-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-11',
                                name: '第十一讲',
                                sessions: [
                                    {
                                        index: '8-11-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-11-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-11-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-12',
                                name: '第十二讲',
                                sessions: [
                                    {
                                        index: '8-12-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-12-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-12-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-13',
                                name: '第十三讲',
                                sessions: [
                                    {
                                        index: '8-13-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-13-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-13-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-14',
                                name: '第十四讲',
                                sessions: [
                                    {
                                        index: '8-14-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-14-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-14-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-15',
                                name: '第十五讲',
                                sessions: [
                                    {
                                        index: '8-15-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-15-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-15-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-16',
                                name: '第十六讲',
                                sessions: [
                                    {
                                        index: '8-16-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-16-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-16-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-17',
                                name: '第十七讲',
                                sessions: [
                                    {
                                        index: '8-17-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-17-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-17-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-18',
                                name: '第十八讲',
                                sessions: [
                                    {
                                        index: '8-18-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-18-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-18-3',
                                        name: 'Session 3'
                                    }
                                ]
                            },
                            {
                                index: '8-19',
                                name: '第十九讲',
                                sessions: [
                                    {
                                        index: '8-19-1',
                                        name: 'Session 1'
                                    },
                                    {
                                        index: '8-19-2',
                                        name: 'Session 2'
                                    },
                                    {
                                        index: '8-19-3',
                                        name: 'Session 3'
                                    }
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        created() {
            const height = document.documentElement.clientHeight;
            this.menuHeight = this.menuHeight + " height: " + height * 0.92 + 'px';
            let record = localStorage.getItem('record');
            if (record) {
                let parse = JSON.parse(record);
                this.current = parse.index;
                const arr = this.current.split('-');
                let video = this.videos[arr[0] - 1];
                this.currentRecord = video.name + ' ' + video.speaks[arr[1] - 1].name + ' ' + video.speaks[arr[1] - 1].sessions[arr[2] - 1].name;
                this.lookedTime = parseInt(parse.playTime / 60 + '') + '分 ' + parseInt(parse.playTime % 60 + '') + '秒';
                this.playByTime(null);
            } else {
                this.recording(0);
            }
        },
        methods: {
            player() {
                return this.$refs.videoPlayer.player;
            },
            mapToObj(map) {
                let obj = Object.create(null);
                for (let [k, v] of map) {
                    obj[k] = v;
                }
                return obj;
            },
            objToMap(obj) {
                let map = new Map();
                Object.keys(obj).forEach(k => {
                    map.set(k, obj[k]);
                });
                return map;
            },
            handleOpen(key, keyPath) {
                // eslint-disable-next-line no-console
                console.log(key, keyPath)
            },
            handleClose(key, keyPath) {
                // eslint-disable-next-line no-console
                console.log(key, keyPath)
            },
            handlePlay(index) {
                this.current = index;
                const arr = index.split('-');
                this.playerOptions.sources[0].src = 'http://localhost:8090/video/chapter' + arr[0] + '/speak' + arr[1] + '/session' + arr[2] + '.mp4';
                let playTime = this.getPlayTime(index);
                if (playTime) {
                    this.playByTime(playTime);
                }
            },
            onPlayerTimeupdate(player) {
                this.recording(player.currentTime());
            },
            playerReadied(player) {
                // eslint-disable-next-line no-console
                console.log('the player is readied', player);
                this.showCurrentPlay(player.currentTime());
                this.recording(player.currentTime());
            },
            recording(currentTime) {
                let key = 'record';
                let record = localStorage.getItem(key);
                if (!record) {
                    record = '{}'
                }
                let parse = JSON.parse(record);
                parse.index = this.current;
                parse.playTime = currentTime;
                parse.time = new Date().getTime();
                localStorage.setItem(key, JSON.stringify(parse));
                this.showCurrentPlay(currentTime);
                this.recordingItem(currentTime);
            },
            recordingItem(currentTime) {
                let key = 'all';
                let all = localStorage.getItem(key);
                if (!all) {
                    all = '{}';
                }
                let parse = JSON.parse(all);
                let map = this.objToMap(parse);
                map.set(this.current, currentTime);
                parse = this.mapToObj(map);
                localStorage.setItem(key, JSON.stringify(parse));
            },
            getPlayTime(index) {
                let key = 'all';
                let all = localStorage.getItem(key);
                if (!all) {
                    all = '{}';
                }
                let parse = JSON.parse(all);
                let map = this.objToMap(parse);
                return map.get(index);
            },
            showCurrentPlay(currentTime) {
                const arr = this.current.split('-');
                let video = this.videos[arr[0] - 1];
                this.currentRecord = video.name + '| ' + video.speaks[arr[1] - 1].name + '| ' + video.speaks[arr[1] - 1].sessions[arr[2] - 1].name;
                this.lookedTime = parseInt(currentTime / 60 + '') + '分 ' + parseInt(currentTime % 60 + '') + '秒';
            },
            playByTime(time) {
                let key = 'record';
                let record = localStorage.getItem(key);
                if (record) {
                    if (time) {
                        let interval = setInterval(() => {
                            this.player().ready(() => {
                                this.player().currentTime(time);
                                window.clearInterval(interval);
                            });
                        }, 50);
                    } else {
                        let parse = JSON.parse(record);
                        let index = parse.index;
                        this.current = index;
                        const arr = index.split('-');
                        this.playerOptions.sources[0].src = 'http://localhost:8090/video/chapter' + arr[0] + '/speak' + arr[1] + '/session' + arr[2] + '.mp4';
                        let interval = setInterval(() => {
                            this.player().ready(() => {
                                this.player().currentTime(parse.playTime);
                                window.clearInterval(interval);
                            });
                        }, 50);
                    }
                }
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
        border: 1px solid gainsboro;
        border-radius: 5px;
        box-shadow: 1px 1px 1px 1px gray;
    }

    .menu-middle {
        background-color: black;
    }

    .menu-left {
        background-color: black;
        overflow-y: scroll;
    }

    .menu-right {
        background-color: black;
    }

    .player-card {
        width: 80%;
        margin: 60px auto 0;
        background-color: aliceblue;
    }

    .frame {
        background-color: #FFFFFF;
    }

    html {
        background-color: aliceblue;
    }
</style>
