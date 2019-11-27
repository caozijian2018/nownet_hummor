<template>
    <div @click="goWatchMp4(item.id)" class="video_box">
        <div class="hover_img position_relative text_center margin_bottom-1 overflow_hidden" :style="{height:boxheight+'px'}">
            <i class="pcs iconfont icon-ai239 white play pc_none"></i>
            <div class="width_100 height_100 img_box">
                <img src="../../static/img/ld.gif" class="pcs video_img_box" v-if="!item.showloading" alt="">
                <img @load="loadImg($event)" v-show="item.showloading" :src="prefix + item.cover" class="pcs video_img_box" style="" alt="">
            </div>
            <!-- <div class="hoverdiv mp4hover">
                <div class="white pcs">
                    WATCH IT <i class="iconfont icon-xiangyou"></i>
                </div>
            </div> -->
            <div class="hoverdiv hover_box transilatey80 transition_4s">
                <div class="white width_100 text_top position_absolute">
                    <div class="font_size_6 text_center" v-if="$store.state.is_pc">{{ item.title|first10 }}</div>
                    <div class="font_size_6 text_center" v-else>{{item.title|first8}}</div>

                    <div class="box_button margin_top_20 phone_margin_top_20">
                        Play It
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import getCurrentPrefix from "../../util/get_current_prefix"
    import appButton from "../button"
    export default {
        components:{
            appButton
        },
        props: {
            item: {
                type: Object
            }
        },
        data() {
            return {
                boxheight: "",
                boxwidth: "",
                prefix: getCurrentPrefix()
            }
        },
        mounted() {
            this.setWidthandHeight();
            if(!this.$store.state.right_box_height){
                this.setRightBoxHeight();
            }
        },
        filters: {
            yearMonthDay(val) {
                try {
                    return val.split("T")[0]
                } catch (error) {
                    return "time error"
                }
            },
            secendToTime(s) {
                var H = String(Math.floor(s / 3600)).padStart(2, 0);
                var m = String(Math.floor(s / 60) % 60).padStart(2, 0);
                var S = String(s % 60).padStart(2, 0);
                var time = H + ":" + m + ":" + S;
                return time;
            }
        },
        methods: {
            setRightBoxHeight(){
                this.$nextTick(()=>{
                    if(document.querySelector(".right_div_box")){
                        var height = getComputedStyle(document.querySelector(".right_div_box")).height;
                        this.$store.state.right_box_height = height;
                    }
                    
                })
            },
            goWatchMp4(id) {
                this.$router.push({
                    path: "/" + id
                });
            },
            loadImg(e) {
                var img = e.target;
                this.$set(this.item, 'showloading', true);
                this.$nextTick(() => {
                    var divbox = this.$jquery(img.parentNode);
                    var imgtoheightratio = parseFloat(img.width) / parseFloat(img.height);
                    var divtoheightratio = this.boxwidth / this.boxheight;
                    imgtoheightratio / divtoheightratio > 1 ? this.$jquery(img).css("height", "100%") : this.$jquery(img).css("width", "100%");
                });
            },
            setWidthandHeight() {
                var boxwidth;
                try { 
                    boxwidth = this.$jquery(".video_box")[1].offsetWidth;
                } catch (error) {
                    boxwidth = parseFloat(this.$jquery(this.$jquery('.video_box')[this.$jquery(".video_box").length - 1]).css('width'));
                }

                this.boxwidth = boxwidth; // 
                var boxheight = this.boxwidth / 1.2;
                console.log(boxheight+"xxxx")
                this.boxheight = boxheight
            }
        }
    }
</script>

<style lang='less'>
    @import "../../assets/css/current_theme";
  
    .video_box {
        .text_top{
            left: 50%;
            top: 50px;
			transform: translate(-50%, -50%);
        }
        .title_text {
            font-size: 19px;
        }
        transition-property: box-shadow;
        transition-duration: .4s;
        .img_box {
            //height:200px;
            position: relative;
            img {
                position: absolute;
                top: 50%;
                left: 50%;
                transform: translate(-50%, -50%);
            }
        }
        .pc_none {
            z-index: 10001;
        }
        .mp4hover {
            display: none;
        }
        .margin_bottom-1 {
            margin-bottom: -1px;
        }
        .video_box_icon {
            position: absolute;
            left: 0;
            bottom: 2px;
            padding: 4px 10px;
            color: #fff;
            z-index: 1004;
            background: @light_gray;
        }
        .play {
            font-size: 40px;
        }
        .box_footer {
            color: @font_gray;
        }
        @media screen and (max-width:800px){
            .text_top{
                top: 40px;
            }
        }
        
        @media screen and (min-width:800px) {
           
            .pc_none {
                display: none;
            }
            &:hover {
                box-shadow: 5px 5px 10px rgba(30, 30, 30, 1.0);
            }
            .hover_img {
                &:hover{
                    .text_top{
                        animation-name:duang;
                        animation-fill-mode:forwards;
				        animation-duration:.6s;
                    }

                }
                &:hover .hover_box{
                    transform: translateY(0);
                }
                &:hover .mp4hover {
                    display: block;
                    z-index: 10002;
                }
                &:hover img {
                    transform-origin: 50% 50%;
                    transform: translate(-50%, -50%) scale(1.4, 1.4);
                    transition: .3s;
                }
            }
        }
    }
</style>