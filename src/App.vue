<template>
    <div id="app">
        <el-row :gutter="16">
            <el-col :span="1">
                <div style="opacity: 0;">1</div>
            </el-col>
            <el-col class="co-left" :span="5">
                <div :style="menuHeight" class="menu-left">
                    <el-card class="menu-card">
                        <div slot="header" class="clearfix">
                            <span style="color: deepskyblue;">视频</span>
                        </div>
                        <el-menu
                                default-active="2"
                                class="el-menu-vertical-demo"
                                @open="handleOpen"
                                @close="handleClose"
                                active-text-color="#ffd04b">
                            <el-submenu v-for="item in videos" v-bind:key="item.index" :index="item.index"
                                        style="text-align: left;">
                                <template slot="title">
                                    <i class="el-icon-notebook-1"></i>
                                    <span style="font-size: 12px;">{{ item.name }}</span>
                                </template>
                                <el-submenu v-for="speak in item.speaks" v-bind:key="speak.index" :index="speak.index">
                                    <template slot="title">
                                        <i class="el-icon-headset"></i>
                                        <span style="color: deepskyblue;">{{ speak.name }}</span>
                                    </template>
                                    <el-menu-item v-for="session in speak.sessions" v-bind:key="session.index"
                                                  :index="session.index" @click="handlePlay(session.index)">
                                        <template slot="title">
                                            <i class="el-icon-video-camera"></i>
                                            <span>{{ session.name }}</span>
                                        </template>
                                    </el-menu-item>
                                </el-submenu>
                            </el-submenu>
                        </el-menu>
                    </el-card>
                </div>
            </el-col>
            <el-col :span="13">
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
                    <el-card>
                        <div slot="header" class="clearfix">
                            <span style="color: deepskyblue;">正在播放</span>
                        </div>
                        <div>
                            <span>{{ currentRecord }}</span>
                            <el-divider content-position="left">
                                <el-link type="primary" icon="el-icon-timer" @click="playByTime">{{ lookedTime }}
                                </el-link>
                            </el-divider>
                        </div>
                    </el-card>
                    <el-card class="functionCard">
                        <div class="changeMoc">
                            <el-button size="small" type="success" @click="changeMock">切换动画</el-button>
                            <el-button size="small" type="warning" @click="changeBg">切换背景</el-button>
                        </div>
                    </el-card>
                </div>
            </el-col>
        </el-row>
    </div>
</template>
<script>
    import Vue from 'vue';
    import ElementUI from 'element-ui';
    import 'element-ui/lib/theme-chalk/index.css';
    import VideoPlayer from 'vue-video-player'
    import axios from 'axios'

    require('video.js/dist/video-js.css');
    require('vue-video-player/src/custom-theme.css');

    Vue.use(VideoPlayer);
    Vue.use(ElementUI);

    const HTTP_CLIENT = axios.create({
        baseURL: 'http://localhost:8090',
        withCredentials: false
    });
    const baseUrl = 'http://localhost:8090';
    const moc = [
        {
            target: 'live2d-widget-model-epsilon2_1',
            json: 'Epsilon2.1.model.json'
        }, {
            target: 'live2d-widget-model-haru_1',
            json: 'haru01.model.json'
        }, {
            target: 'live2d-widget-model-haru_2',
            json: 'haru02.model.json'
        }, {
            target: 'live2d-widget-model-haruto',
            json: 'haruto.model.json'
        }, {
            target: 'live2d-widget-model-hibiki',
            json: 'hibiki.model.json'
        }, {
            target: 'live2d-widget-model-hijiki',
            json: 'hijiki.model.json'
        }, {
            target: 'live2d-widget-model-izumi',
            json: 'izumi.model.json'
        }, {
            target: 'live2d-widget-model-koharu',
            json: 'koharu.model.json'
        }, {
            target: 'live2d-widget-model-miku',
            json: 'miku.model.json'
        }, {
            target: 'live2d-widget-model-ni-j',
            json: 'ni-j.model.json'
        }, {
            target: 'live2d-widget-model-nico',
            json: 'nico.model.json'
        }, {
            target: 'live2d-widget-model-nietzsche',
            json: 'nietzche.model.json'
        }, {
            target: 'live2d-widget-model-nipsilon',
            json: 'nipsilon.model.json'
        }, {
            target: 'live2d-widget-model-nito',
            json: 'nito.model.json'
        }, {
            target: 'live2d-widget-model-shizuku',
            json: 'shizuku.model.json'
        }, {
            target: 'live2d-widget-model-tororo',
            json: 'tororo.model.json'
        }, {
            target: 'live2d-widget-model-unitychan',
            json: 'unitychan.model.json'
        }, {
            target: 'live2d-widget-model-wanko',
            json: 'wanko.model.json'
        }, {
            target: 'live2d-widget-model-z16',
            json: 'z16.model.json'
        }
    ];

    const bg = [
        '1.jpg',
        '226ddf680017dcca5d205c9363e2eb9d.jpg',
        '0639b4dc6535c63bc023b6f21a42ab75.jpg',
        '20170611161251_ktJFS.jpeg',
        '143753vkdbjsck3t05t94d.jpg',
        '20140825051426_3AuWs.jpeg',
        'bg.jpeg',
        'bg1.jpg',
        'bg2.jpg',
        'bg4.jpg',
        'bg5.jpg',
        'bg6.jpg',
        'dc4046c9f30c15104cff5b1fd514d63e.jpg',
        'e81999978a1897e4375dd12c7b2f252b.jpg'
    ];

    const videos = [
        {
            "index": "1",
            "name": "基础知识第一章 健康管理概论",
            "speaks": [
                {
                    "index": "1-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "1-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "1-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "1-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "1-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "1-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "1-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "1-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "1-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "1-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "1-3-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "2",
            "name": "基础知识第二章 临床医学基础知识",
            "speaks": [
                {
                    "index": "2-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "2-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "2-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "2-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "2-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "2-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "2-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "2-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "2-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "2-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "2-3-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "3",
            "name": "基础知识第三章 预防医学基础知识",
            "speaks": [
                {
                    "index": "3-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "3-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "3-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "3-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "3-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "3-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "3-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "3-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "3-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "3-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "3-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "3-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "3-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "3-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "3-4-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "4",
            "name": "基础知识第四章 常见慢性病非传染性疾病",
            "speaks": [
                {
                    "index": "4-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "4-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "4-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "4-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "4-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "4-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "4-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "4-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "4-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "4-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-5-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-5-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "4-6",
                    "name": "第六讲",
                    "sessions": [
                        {
                            "index": "4-6-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "4-6-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "4-6-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "4-6-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "5",
            "name": "基础知识第五章 基本卫生保健",
            "speaks": [
                {
                    "index": "5-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "5-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "5-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "5-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "5-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "5-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "5-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "5-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "5-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "5-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "5-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "5-3-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "5-3-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "6",
            "name": "基础知识第六章 流行病学和医学统计学基本知识",
            "speaks": [
                {
                    "index": "6-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "6-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "6-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "6-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "6-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "6-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "6-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "6-2-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "6-2-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "7",
            "name": "基础知识第七章 健康教育学",
            "speaks": [
                {
                    "index": "7-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "7-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "7-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "7-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "7-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "7-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "7-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "7-2-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "7-2-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "8",
            "name": "基础知识第八章 营养与食品安全",
            "speaks": [
                {
                    "index": "8-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "8-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "8-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "8-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "8-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "8-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-5-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-5-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-6",
                    "name": "第六讲",
                    "sessions": [
                        {
                            "index": "8-6-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-6-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-6-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-7",
                    "name": "第七讲",
                    "sessions": [
                        {
                            "index": "8-7-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-7-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-7-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-8",
                    "name": "第八讲",
                    "sessions": [
                        {
                            "index": "8-8-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-8-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-8-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-9",
                    "name": "第九讲",
                    "sessions": [
                        {
                            "index": "8-9-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-9-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-9-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-10",
                    "name": "第十讲",
                    "sessions": [
                        {
                            "index": "8-10-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-10-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-10-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-11",
                    "name": "第十一讲",
                    "sessions": [
                        {
                            "index": "8-11-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-11-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-11-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-12",
                    "name": "第十二讲",
                    "sessions": [
                        {
                            "index": "8-12-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-12-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-12-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-13",
                    "name": "第十三讲",
                    "sessions": [
                        {
                            "index": "8-13-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-13-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-13-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-14",
                    "name": "第十四讲",
                    "sessions": [
                        {
                            "index": "8-14-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-14-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-14-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-15",
                    "name": "第十五讲",
                    "sessions": [
                        {
                            "index": "8-15-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-15-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-15-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-16",
                    "name": "第十六讲",
                    "sessions": [
                        {
                            "index": "8-16-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-16-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-16-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-17",
                    "name": "第十七讲",
                    "sessions": [
                        {
                            "index": "8-17-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-17-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-17-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-18",
                    "name": "第十八讲",
                    "sessions": [
                        {
                            "index": "8-18-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-18-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-18-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "8-19",
                    "name": "第十九讲",
                    "sessions": [
                        {
                            "index": "8-19-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "8-19-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "8-19-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "9",
            "name": "基础知识第九章 身体活动基本知识",
            "speaks": [
                {
                    "index": "9-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "9-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "9-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "9-1-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "10",
            "name": "基础知识第十章 心理健康",
            "speaks": [
                {
                    "index": "10-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "10-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "10-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "10-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "10-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "10-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "10-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "10-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "10-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "10-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "10-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "10-3-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "10-3-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "11",
            "name": "基础知识第十一章 中医养生学",
            "speaks": [
                {
                    "index": "11-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "11-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "11-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "11-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "11-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "11-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "11-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "11-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "11-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "11-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "11-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "11-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "11-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "11-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "11-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "11-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "11-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "11-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "11-5-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "12",
            "name": "基础知识第十二章 康复医学基础知识",
            "speaks": [
                {
                    "index": "12-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "12-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "12-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "12-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "12-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "12-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "12-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "12-2-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "13",
            "name": "基础知识第十三章 健康信息学",
            "speaks": [
                {
                    "index": "13-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "13-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "13-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "13-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "13-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "13-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "13-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "13-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "13-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "13-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "13-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "13-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "13-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "13-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "13-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "13-4-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "13-4-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "14",
            "name": "基础知识第十四章 医学伦理学与健康管理职业道德",
            "speaks": [
                {
                    "index": "14-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "14-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "14-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "14-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "14-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "14-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "14-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "14-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "14-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "14-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "14-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "14-3-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "14-3-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "15",
            "name": "基础知识第十五章 健康保险与健康管理",
            "speaks": [
                {
                    "index": "15-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "15-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "15-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "15-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "15-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "15-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "15-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "15-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "15-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "15-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "15-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "15-3-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "15-3-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "16",
            "name": "基础知识第十六章 健康管理服务营销与相关健康产品",
            "speaks": [
                {
                    "index": "16-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "16-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "16-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "16-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "16-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "16-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "16-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "16-2-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "16-2-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "17",
            "name": "基础知识第十七章 健康管理相关法律、法规知识",
            "speaks": [
                {
                    "index": "17-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "17-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "17-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "17-1-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "17-1-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "18",
            "name": "三级技能第一章 健康监测",
            "speaks": [
                {
                    "index": "18-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "18-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "18-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "18-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "18-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "18-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "18-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "18-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "18-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "18-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "18-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "18-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "18-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "18-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "18-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "18-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "18-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "18-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "18-5-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "19",
            "name": "三级技能第二章 健康风险评估和分析",
            "speaks": [
                {
                    "index": "19-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "19-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "19-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "19-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "19-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "19-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "19-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "19-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "19-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "19-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "19-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "19-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "19-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "19-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "19-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "19-4-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "20",
            "name": "三级技能第三章 健康指导",
            "speaks": [
                {
                    "index": "20-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "20-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "20-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "20-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "20-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "20-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "20-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "20-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "20-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "20-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "20-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "20-3-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "21",
            "name": "三级技能第四章 健康危险因素干预",
            "speaks": [
                {
                    "index": "21-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "21-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "21-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "21-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "21-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "21-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-5-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-5-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-6",
                    "name": "第六讲",
                    "sessions": [
                        {
                            "index": "21-6-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-6-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-6-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "21-7",
                    "name": "第七讲",
                    "sessions": [
                        {
                            "index": "21-7-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "21-7-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "21-7-3",
                            "name": "Session 3"
                        }
                    ]
                }
            ]
        },
        {
            "index": "22",
            "name": "三级技能 实习",
            "speaks": [
                {
                    "index": "22-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "22-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "22-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "22-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "22-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "22-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "22-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "22-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "22-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "22-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "22-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "22-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "22-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "22-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "22-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "22-4-3",
                            "name": "Session 3"
                        },
                        {
                            "index": "22-4-4",
                            "name": "Session 4"
                        }
                    ]
                }
            ]
        },
        {
            "index": "23",
            "name": "教材区别",
            "speaks": [
                {
                    "index": "23-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "23-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "23-1-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "24",
            "name": "基础直播串讲",
            "speaks": [
                {
                    "index": "24-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "24-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "24-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "24-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "24-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "24-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "24-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "24-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "24-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "24-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "24-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "24-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "24-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "24-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "24-4-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        },
        {
            "index": "25",
            "name": "技能直播串讲",
            "speaks": [
                {
                    "index": "25-1",
                    "name": "第一讲",
                    "sessions": [
                        {
                            "index": "25-1-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-1-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-1-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-2",
                    "name": "第二讲",
                    "sessions": [
                        {
                            "index": "25-2-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-2-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-2-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-3",
                    "name": "第三讲",
                    "sessions": [
                        {
                            "index": "25-3-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-3-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-3-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-4",
                    "name": "第四讲",
                    "sessions": [
                        {
                            "index": "25-4-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-4-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-4-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-5",
                    "name": "第五讲",
                    "sessions": [
                        {
                            "index": "25-5-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-5-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-5-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-6",
                    "name": "第六讲",
                    "sessions": [
                        {
                            "index": "25-6-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-6-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-6-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-7",
                    "name": "第七讲",
                    "sessions": [
                        {
                            "index": "25-7-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-7-2",
                            "name": "Session 2"
                        },
                        {
                            "index": "25-7-3",
                            "name": "Session 3"
                        }
                    ]
                },
                {
                    "index": "25-8",
                    "name": "第八讲",
                    "sessions": [
                        {
                            "index": "25-8-1",
                            "name": "Session 1"
                        },
                        {
                            "index": "25-8-2",
                            "name": "Session 2"
                        }
                    ]
                }
            ]
        }
    ];

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
                        type: 'video/mp4',
                        src: baseUrl + '/video/chapter1/speak1/session1.mp4'
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
                videos: videos
            }
        },
        created() {
            this.createdOnInitBg();
            this.createdOnInitMoc();
            this.createdOnHeight();
            this.createdOnPlay();
        },
        methods: {
            player() {
                return this.$refs.videoPlayer.player;
            },
            request(method, url, data, then) {
                HTTP_CLIENT({
                    method: method,
                    url: url,
                    data: data
                })
                    .then(then)
                    .catch(error => {
                        // eslint-disable-next-line no-console
                        console.error(error)
                    })
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
            createdOnInitBg() {
                const defaultIndex = 6;
                let lastBg = this.getLastBg();
                lastBg = lastBg ? lastBg % bg.length : defaultIndex;
                this.saveLastBg(lastBg);
                this.initBg(bg[lastBg]);
            },
            createdOnInitMoc() {
                const defaultIndex = 14;
                setTimeout(() => {
                    let lastMoc = this.getLastMoc();
                    let length = moc.length;
                    lastMoc = lastMoc ? lastMoc % length : defaultIndex;
                    this.saveLastMoc(lastMoc);
                    this.initMoc(moc[lastMoc].target, moc[lastMoc].json);
                }, 1000);
            },
            createdOnHeight() {
                const height = document.documentElement.clientHeight;
                this.menuHeight = this.menuHeight + " height: " + height * 0.86 + 'px';
            },
            createdOnPlay() {
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
            getLastMoc() {
              let item = localStorage.getItem('lastMoc');
              return item ? item : null;
            },
            saveLastMoc(index) {
                localStorage.setItem('lastMoc', index);
            },
            changeMock() {
                let lastMoc = this.getLastMoc();
                lastMoc = lastMoc ? lastMoc : 0;
                let nextMoc = parseInt(lastMoc) + 1;
                this.saveLastMoc(nextMoc);
                location.reload();
            },
            initMoc(target, json) {
                const config = {
                    pluginRootPath: 'static/live2dw/',
                    pluginJsPath: 'lib/',
                    pluginModelPath: target + '/assets/',
                    tagMode: false,
                    debug: false,
                    model: {jsonPath: '../static/live2dw/' + target + '/assets/' + json},
                    display: {position: 'right', width: 300, height: 650},
                    mobile: {show: true},
                    log: false
                };
                window.L2Dwidget.init(config);
            },
            getLastBg() {
                let item = localStorage.getItem('lastBg');
                return item ? item : null;
            },
            saveLastBg(index) {
                localStorage.setItem('lastBg', index);
            },
            changeBg() {
                let lastBg = this.getLastBg();
                lastBg = lastBg ? lastBg : 0;
                let nextBg = parseInt(lastBg) + 1;
                this.saveLastBg(nextBg);
                let length = bg.length;
                this.initBg(bg[nextBg % length]);
            },
            initBg(pic) {
                let html = document.getElementsByTagName('html');
                html[0].style = 'background-image: url(\'static/' + pic + '\')';
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
                this.playerOptions.sources[0].src = baseUrl + '/video/chapter' + arr[0] + '/speak' + arr[1] + '/session' + arr[2] + '.mp4';
                let playTime = this.getPlayTime(index);
                if (playTime) {
                    this.playByTime(playTime);
                }
            },
            onPlayerTimeupdate(player) {
                this.recording(player.currentTime());
            },
            playerReadied(player) {
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
                        this.playerOptions.sources[0].src = baseUrl + '/video/chapter' + arr[0] + '/speak' + arr[1] + '/session' + arr[2] + '.mp4';
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
        margin-top: 60px;
        border-radius: 10px;
    }

    .menu-card {
        height: 77%;
        overflow-y: scroll;
    }

    .menu-left {
        background: rgba(0, 0, 0, 0);
        filter: Alpha(opacity=0);
    }

    .menu-middle {
        background: rgba(0, 0, 0, 0);
        filter: Alpha(opacity=0);
    }

    .menu-right {
        background: rgba(0, 0, 0, 0);
        filter: Alpha(opacity=0);
    }

    .player-card {
        width: 90%;
        border: none;
        margin: 40px auto 0;
        background: rgba(0, 0, 0, 0);
        filter: Alpha(opacity=0);
    }

    .video-player {
        border: 2px yellow solid;
        border-radius: 5px;
        opacity: 0.9;
    }

    html {
        background-repeat: no-repeat;
        background-size: 100%;
    }

    .functionCard {
        margin-top: 20px;
        background: rgba(0, 0, 0, 0.2);
        filter: Alpha(opacity=20);
        border: none;
    }
</style>
