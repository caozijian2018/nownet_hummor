<template>
    <div class="width_100">
        <mp4 class="width_100" :mp4-src="mp4_src+'?AuthInfo='+$store.state.file_token"></mp4>
        <div class="font_size_15 width_90 margin_auto margin_top_10 margin_bottom_15" style="text-indent: 20px">
            {{desc}}
        </div>
        <video-footer v-if="show_footer"></video-footer>
    </div>
</template>
<script>
    import mp4 from "../../../components/mp4"
    import go4url from "../../../util/go4url"
    import getCountry from "../../../util/get_country"
    import init_token from "../../../util/init_token"
    import bus from "../../../util/bus"
    import getCurrentPrefix from "../../../util/get_current_prefix"
    import videoFooter from "../../../components/footer";


    export default {
        components: {
            mp4,
            videoFooter
        },
        data() {
            return {
                mp4_src: '',
                op: "",
                desc: "",
                show_footer: true
            }
        },
        mounted() {
            var is_need_login = this.$route.query.type;
            if(is_need_login){
                this.getMp4src()
            }else{
                this.haslogin();
            }
            this.whatchLoginSuccess();
            this.show_footer = this.$route.query.type != 'mt';
        },
        beforeRouteLeave(to,from,next){
            bus.$emit('showLoginOrHide',false)
            next()
        },
        methods: {
            isNeedLogin(){
                
            },
            whatchLoginSuccess(){
                bus.$on("loginSuccess",()=>{
                    this.getMp4src();
                })
            },
            showLoginOrHide(v){
                bus.$emit('showLoginOrHide',v)
            },
            haslogin() {
                if (!localStorage.video_token) {
                    this.$nextTick(()=>{
                        this.showLoginOrHide(true)
                    })
                } else {
                    this.getMp4src()
                }
            },
            saveGogo4videoUrlid() {
                localStorage.mp4id = this.$route.params.mp4id;
            },
            getMp4src() {
                var mp4id = this.$route.params.mp4id;
                this.$http(`dcb/album/${mp4id}/`).then(res => {
                    if (res.resources.length) {
                        var src = res.resources[0];
                        this.mp4_src = getCurrentPrefix() + src;
                        this.desc = res.desc
                    } else {
                        this.$msg(this.$t('words.vip_be_overdue'), 'warning');
                        this.$router.go(-1);
                    }
                }).catch(err => {
                    if (err.code == '401') {
                        this.$msg('token overdue please login again!', 'warning');
                        localStorage.video_token = "";
                        init_token();
                    }
                    this.$router.go(-1);
                })
            }
    
        }
    }
</script>

<style lang='less' scoped>
    @import "../../../assets/css/current_theme.less";
    body {
        background: @light_gray
    }
</style>