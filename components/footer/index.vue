<template>
    <div class="describeBox text_center font_size_15 ">
        <!-- <a :href="'http://secretsgarden.net/?country='+platform"  class="white">{{$t('words.home')}}</a> -
        <a class="white"  :href="'http://static.secretsgarden.net/'+platform+'/terms.html'">{{$t('words.terms')}}</a>
        <a href="#" class="white" @click="cancelSub"> - {{$t('words.cancel')}}</a>-->
        <div class="width_80 padding_top_10 text_left display_flex phone_block margin_auto phone_width_90">
            <div class="flex_1">
                <h3>
                    {{$t('words.navigation')}}
                </h3>
                <div v-for="item in arr" class="margin_bottom_3px cursor" @click="getList(item.original_name)" :key="item.id">{{item.name}}</div>
            </div>
            <div class="flex_2 margin_left_15 phone_margintop_20 margin_top_20">
                <div class="point margin_bottom_3px cursor" @click="goWatchMp4(item.id)" v-for="item in list" :key="item.id">
                    {{item.title}}
                </div>
            </div>
            <div class="flex_4 margin_left_15">
                <div class="margin_bottom_3px font_weight_600 font_size_20">
                    {{$t('words.about_ketchupbox')}}
                </div>
                {{$t('words.descc')}}
            </div>
        </div>
        <div class=" text_center margin_top_20">
            © 2020 All right reserved
        </div>
    </div>
</template>

<script>
import getCountry from "../../util/get_country";
import go4url from "../../util/go4url";
import getLang from "../../util/get_lang";
import getBanner from "../../util/get_banner";
import getCurrentAppType from "../../util/get_current_app_type";



export default {
    data() {
        return {
            platform: "",
            arr: [],
            lang: "",
            list: [],
            app_type: getCurrentAppType(),
        };
    },
    mounted() {
        this.setCountry();
        this.getCates();
    },
    methods: {
        goWatchMp4(id) {
            this.$router.push({
                    path: "/" + id
            });
        },
        getList(tag_name = this.arr[0].original_name) {
            this.$http("dcb/album/", "get", {
                tags: tag_name,
                page: 1,
                category: this.app_type,
                capacity: 5,
                ordering: "-show_cnt",
                lang: this.lang
            }).then(res => {
                console.log(res)
                // this.fullscreenLoading = false;
                this.list = res.results;
                // this.is_mounted = true;
            });
            // this.$http("album", "get", {
            //     tag_id,
            //     capacity: 6,
            //     lang: this.lang
            // }).then(res => {
            //     this.list = res.results;
            // });
        },
        getCates() {
            this.$nextTick(() => {
                console.log(444)
                this.lang = getLang();
                this.$http("dcb/site/", "get", {
                    ordering: "-create_time",
                    lang: this.lang,
                    capacity: 100
                }).then(res => {
                    // this.arr = res;
                    console.log(777);
                    console.log(res);
                    console.log(999);
                    var obj = getBanner(res.results);
                    console.log(obj.tags)
                    this.arr = obj.tags;
                    this.getList();
                });
            });
        },
        setCountry() {
            this.$nextTick(() => {
                this.platform = getCountry();
            });
        },
    }
};
</script>

<style lang='less'>
@import "../../assets/css/current_theme.less";
.describeBox {
    background: @light_gray;
    padding: 20px;
    color: #fff;
}
.margin_bottom_3px{
    margin-bottom: 3px;
}
@media screen and (max-width: 800px) {
    .phone_width_90 {
        width: 90%;
    }
}
</style>