<template>
    <div v-loading.fullscreen.lock="fullscreenLoading">
        <div class="display_flex width_98 margin_auto margin_top_10 wrop flex_1 flex_jusify_space right_div_box" >
                <video-div
                class="width_24 margin_bottom_15 phone_margin_bottom_10px media_phone_margin_auto phone_width_47 "
                v-for="(item) in list"
                :item="item"
                :key="item.id"
                ></video-div>
            </div>
    </div>
</template>

<script>
import getCurrentAppType from "../../../../util/get_current_app_type";
import getLang from "../../../../util/get_lang";
import videoDiv from "../../../../components/video_div";

export default {
    components:{
        videoDiv
    },
    mounted(){
        this.tag = this.$route.params.index;
        this.init();
        this.getList()
    },
    data(){
        return {
            app_type: getCurrentAppType(),
            country: "",
            tag: "",
            fullscreenLoading: true,
            list: [],
        }
    },
    methods: {
        init(){
            this.country = getLang()
        },
        getList() {
            this.fullscreenLoading = true;
            this.$http("dcb/album/", "get", {
                tags: this.tag,
                page: 1,
                category: this.app_type,
                capacity: 100,
                ordering: "-show_cnt",
                lang: this.country
            }).then(res => {
                console.log(res)
                this.fullscreenLoading = false;
                this.list = res.results;
                // this.is_mounted = true;
            });
        }
    }
};
</script>
<style lang="less"></style>
