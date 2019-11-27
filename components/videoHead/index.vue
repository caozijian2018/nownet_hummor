<template>
    <div class="bigbox">
        <div class="head_box">
            <div class="display_flex head_innerbox flex_jusify_space">
                <div class="display_flex flex_align_center">
                    <img
                        src="../../static/img/logo.png"
                        @click="goHome"
                        class="cursor head_img padding_left_20"
                        alt
                    />
                    <div class="display_flex flex_align_center classfication_pc cursor">
                        <!-- <div
                            class="cate_div"
                            @mouseover="orderselect('to_cate')"
                            @click="toCate()"
                            @mouseleave="selectOrder=''"
                        >
                            <div
                                class="hover_cate_back cataback"
                                 
                            ></div>
                            <span class="pcs">{{$t('words.home')}}</span>
                        </div> -->
                        <div
                            class="cate_div"
                            v-if="show_login_button"
                            @mouseover="orderselect('login')"
                            @click="showLogin()"
                            @mouseleave="selectOrder=''"
                        >
                            <div
                                class="hover_cate_back cataback"
                                
                            ></div>
                            <span class="pcs">{{$t('words.login')}}</span>
                        </div>
                        <div
                            class="cate_div"
                            v-if="show_unlogin_button"
                            @mouseover="orderselect('login')"
                            @click="unLogin()"
                            @mouseleave="selectOrder=''"
                        >
                            <div
                                class="hover_cate_back cataback"
                                
                            ></div>
                            <span class="pcs">{{$t('words.unlogin')}}</span>
                        </div>
                    </div>
                </div>
                <div class="head_icon display_flex phone_show" @click.stop="showWichbox('option')">
                    <!-- <i :class="{selectback_ground:showWhich=='search'}" @click.stop="showWichbox('search')" class="iconfont navigator icon-fangdajing"></i> -->
                    <i
                        class="iconfont icon-chacha"
                        style="font-size:22px"
                        v-if="showWhich=='option'"
                    ></i>
                    <i v-else class="iconfont icon-daohang" style="font-size:25px"></i>
                </div>
            </div>
        </div>
        <div class="option_box" :class="{select:showWhich=='option'}">
            <div class="width_90 margin_auto">
                <video-button
                    v-if="show_unlogin_button"
                    :button-text="$t('words.unlogin')"
                    @click.native="unLogin()"
                    class="margin_top_1"
                ></video-button>
                <video-button
                    v-if="show_login_button"
                    :button-text="$t('words.login')"
                    @click.native="showLogin()"
                    class="margin_top_1"
                ></video-button>
            </div>
        </div>
        <div class="search_box" :class="{select:showWhich=='search'}">
            <div class="width_90 margin_auto">
                <el-input @keyup.native.13="Search" class="video_input" v-model="search_word"></el-input>
                <video-button
                    :button-text="$t('words.search')"
                    @click.native="Search"
                    class="margin_top_1"
                ></video-button>
            </div>
        </div>
        <div
            class="hoverdiv head_hover"
            style="z-index:10002"
            v-if="showWhich!=''"
            @click="showWhich=''"
        ></div>
    </div>
</template>

<script>
import videoButton from "../button";
import getCountry from "../../util/get_country";
import bus from "../../util/bus";
import unlogin from "../../util/unlogin";
import getOp from "../../util/get_country";

export default {
    data() {
        return {
            search_word: "",
            clickSelect: "",
            selectOrder: "",
            showWhich: "",
            op: "",
            show_login_button: false,
            show_unlogin_button: false,
            arr: [
                {
                    name: this.$t("words.most_recent"),
                    id: "-create_time"
                },
                {
                    name: this.$t("words.most_liked"),
                    id: "-favor_num"
                },
                {
                    name: this.$t("words.most_viewed"),
                    id: "-show_num"
                }
            ]
        };
    },
    components: {
        videoButton
    },
    mounted() {
        this.getOp();
        this.watchBus();
    },
    methods: {
        Introduction(url) {
            this.showWhich = "";
            this.$router.push({
                path: url
            });
        },
        watchBus() {
            bus.$on("loginSuccess", () => {
                this.diffrentOp();
            });
        },
        unLogin() {
            unlogin();
            this.showWhich = "";
            this.diffrentOp();
            this.$msg(this.$t("words.unlogin_success"));
        },
        diffrentOp() {
            switch (this.op) {
                default:
                    (() => {
                        var show_unlogin = Boolean(localStorage.video_token);
                        this.show_login_button = !show_unlogin;
                        this.show_unlogin_button = show_unlogin;
                    })();
                    break;
            }
        },
        getOp() {
            this.$nextTick(() => {
                this.op = getOp();
                this.diffrentOp();
            });
        },
        Search() {
            bus.$emit("search", this.search_word);
            this.showWhich = "";
            this.$store.commit("search", this.search_word);
            this.search_word = "";
            this.$router.push({
                path: "/"
            });
        },
        goHome() {
            this.$router.push({
                path: "/"
            });
        },
        showLogin() {
            this.showWhich = "";
            this.$emit("showLogin");
        },
        toCate() {
            this.showWhich = "";
            this.$router.push({
                path: "/"
            });
        },
        orderselect(ordering) {
            this.clickSelect = "";
            this.selectOrder = ordering;
            // alert(ordering);
        },
        getList(ordering, pc_ph) {
            if (pc_ph == "pc") {
                this.clickSelect = ordering;
            } else if (pc_ph == "ph") {
                this.showWhich = "";
            }
            if (this.$route.name != "index-index") {
                this.$router.push({
                    name: "index-index"
                });
            }
            bus.$emit("ordering", ordering);
        },
        showWichbox(type, mouse) {
            if (mouse) {
                if (screen.width < 800) {
                    return;
                }
                this.showWhich = "";
            } else {
                this.showWhich = type == this.showWhich ? "" : type;
            }
        }
    }
};
</script>

<style lang='less'>
@import "../../assets/css/current_theme";
.bigbox {
    overflow: hidden;
    height: 50px;
    &.height0 {
        height: 0px;
    }
    transition-duration: 0.4s;
    transition-property: height;
    .head_box {
        .cate_div {
            height: 30px;
            border-radius: 15px;
            background: @head_orange;
        }
        .classfication_pc {
            display: none;
        }
        .line_height_50 {
            line-height: 50px;
        }
        .padding10 {
            height: 50px;
            display: inline-block;
            padding: 0 10px;
        }
        .selectback_ground {
            background: @light_gray;
        }
        position: relative;
        z-index: 10005;
        .head_icon {
            transition-duration: 0.4s;
            i {
                display: inline-block;
                height: 50px;
                line-height: 50px;
                padding: 0 10px;
            }
            & .line_box > div {
                transition-duration: 0.4s;
            }
            // rgba(102, 102, 102, 1.0)
            color: #fff;
            .selected_op {
                .first {
                    transform-origin: 0 0;
                    transform: translateX(4px) rotate(39deg);
                }
                .secend {
                    opacity: 0;
                    display: none;
                }
                .last {
                    transform-origin: 0 100%;
                    transform: translateX(4px) rotate(-39deg);
                }
            }
            .option_animate {
                // transition-duration: 0.4s;
                padding: 0 10px;
                height: 50px;
                width: 35px;
                & > div {
                    width: 100%;
                    height: 25px;
                    div {
                        width: 100%;
                        height: 3px;
                        border-radius: 1.5px;
                        background: #fff;
                    }
                }
            }
        }
        .head_img {
            height: 60px;
        }
        .navigator {
            font-size: 24px;
        }
        background: @dark;
        .head_innerbox {
            height: 50px;
            color: #fff;
        }
    }
    .option_box {
        padding-top: 10px;
        padding-bottom: 20px;
        .singole_tag {
            background: @dark;
            font-weight: 600;
            font-size: 19px;
            margin-bottom: 8px;
            height: 40px;
            line-height: 40px;
        }
        width: 100%;
        z-index: 10004;
        position: absolute;
        background: @light_gray;
        transition-duration: 0.4s;
        transition-property: top;
        top: -330px;
        &.select {
            top: 50px;
        }
    }
    .search_box {
        .video_input {
            .el-input__inner {
                background: mix(@light_gray, #ddd, 90%);
            }
        }
        padding: 10px 0;
        transition: 0.3s;
        position: absolute;
        top: -150px;
        width: 100%;
        height: 100px;
        z-index: 10004;
        background: @light_gray;
        &.select {
            top: 50px;
        }
    }
    @media screen and (min-width: 800px) {
        .head_box {
            .head_icon {
                i {
                    padding: 0 50px;
                }
                .pc_option_none {
                    display: none;
                }
            }
            .classfication_pc {
                display: flex;
                height: 50px;
                line-height: 50px;
                margin-left: 20px;
                & > div {
                    overflow: hidden;
                    position: relative;
                    .hover_cate_back {
                        left: 0;
                        top: 0;
                        border-bottom-left-radius: 150px;
                        border-bottom-right-radius: 150px;
                        right: 0;
                        background: lighten(
                            @head_option_back,
                            10%
                        ); //@head_option_back_select;
                        bottom: 50px;
                        transition-property: bottom;
                        transition-duration: 0.6s;
                        position: absolute;
                        &.cataback {
                            background: saturate(@head_orange, 50%);
                        }
                    }
                    .hover_cate_back_select {
                        bottom: -100px;
                    }
                    width: 200px;
                    text-align: center;
                    font-size: 17px;
                    height: 30px;
                    // line-height:50px;
                    background: @head_option_back;
                }
            }
            .head_hover {
                display: none;
            }
        }
        .option_box {
            width: 30%;
            right: 0;
        }
        .search_box {
            width: 30%;
            right: 0;
        }
    }
}
</style>