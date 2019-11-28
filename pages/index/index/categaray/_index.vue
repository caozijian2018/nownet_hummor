<template>
  <div class="taglist_box" v-loading.fullscreen.lock="fullscreenLoading">
    <div class="full_screen black" style="background:rgba(41, 216, 148, 1.0);" v-if="!is_mounted">
      <img
        src="../../../../static/img/loading.gif"
        class="position_absolute pcs"
        style="width:700px"
        alt
        srcset
      />
    </div>
    <div>
      <!-- 中间box div -->
      <div class="margin_top_10">
        <div
            class="width_80 phone_width_90 margin_auto margin_bottom_3"
            v-for="cate_obj in all_type_arr"
            :key="cate_obj.id"
        >
            <div @click="toAll(cate_obj.original_name)" class="display_flex flex_align_center phone_text_center flex_jusify_space margin_bottom_2">
              <div class="font_size_21 ">
                  {{ cate_obj.name }}
              </div>
              <div class="watch_more cursor" >
                  {{ $t("words.more") }}
                  <i class="iconfont icon-xiangyou"></i>
              </div>
            </div>
            <div class="display_flex media_phone_block">
            <div
                @click="toMp4(cate_obj.arr[0].id)"
                v-if="cate_obj.arr"
                class="media_screen_none position_relative overflow_hidden margin_right_10 width_30 text_center left_big_img"
                :style="{ height: $store.state.right_box_height,'backgroundImage': 'url('+prefix + cate_obj.arr[0].cover+')'}"
            >
                <!-- backgroundImage: 'url('+prefix + list[0]&&list[0].cover+')' -->
                <img class="height_100 pcs" :src="prefix + cate_obj.arr[0].cover" alt="">
                <div class="hoverdiv hover_box transition_4s">
                  <div class="white width_100 text_top position_absolute">
                      <div class="font_size_6 text_center">
                      {{ cate_obj.arr[0].title }}
                      </div>
                      <div class="box_button margin_top_20">Watch It</div>
                  </div>
                </div>
            </div>
            <div class="display_flex  wrop flex_1 flex_jusify_space right_div_box" v-if="cate_obj.arr">
                <video-div
                class="width_24 phone_margin_bottom_10px media_phone_margin_auto phone_width_47 "
                v-for="(item, index) in $store.state.is_pc ? cate_obj.arr.slice(1) : cate_obj.arr.slice(0,4)"
                :class="{ margin_top_10: index > 3 }"
                :item="item"
                :key="item.id"
                ></video-div>
            </div>
            </div>
        </div>
      </div>
    </div>
    <video-footer></video-footer>
  </div>
</template>

<script>
import glo_axios from "../../../../util/glo_request";
import videoDiv from "../../../../components/video_div";
import pcOrPhone from "../../../../util/get_pc_or_phone";
import getLang from "../../../../util/get_lang";
import videoFooter from "../../../../components/footer";
import get_banner from "../../../../util/get_banner";
import getCurrentApp from "../../../../util/get_current_app";
import getCurrentAppType from "../../../../util/get_current_app_type";
import getCurrentPrefix from "../../../../util/get_current_prefix";

export default {
  components: {
    videoDiv,
    videoFooter
  },
  data() {
    return {
      arr: [],
      INDEX: 0,
      list: [],
      pcOrPhone: false,
      fullscreenLoading: false,
      select_index: 0,
      country: "",
      category: "",
      page_: 1,
      tag_id: "",
      total: 0,
      is_mounted: false,
      app_type: getCurrentAppType(),
      prefix: getCurrentPrefix(),
      all_type_arr: [],
    };
  },
  props: {
    isScrollBottom: {
      type: Boolean
    }
  },
  mounted() {
    this.init();
    this.setLang();
    this.pcOrPhone = pcOrPhone();
  },
  computed:{
    all_load(){
      debugger
      return this.all_type_arr.every((v)=>{return v.arr})
    },
  },
  methods: {
    toMp4(id){
      this.$router.push({
        path: "/"+id
      })
    },
    toAll(key){
      this.fullscreenLoading = true
      this.$router.push({
        path: "/classification/"+key
      });
    },
    _getPage(page) {
      this.page_ = page;
      this.$store.state.cate_page = page;
      document.querySelector(".scroll_box").scrollTop = "0";
      this.getList();
    },
    init() {
      this.category = getCurrentApp();
      this.$http("dcb/site/", "get", {
        ordering: "-create_time",
        lang: this.$store.state.locale
      }).then(res => {
        this.select_index = this.INDEX = this.$store.state.index || 0;
        this.arr = res.results;
        this.page_ = this.$store.getters.getCatePage;
        this.initBanner();
        for (var key in this.arr) {
          var item = this.arr[key];
          var obj = {};
          obj.name = item.name;
          obj.noriginal_nameame = item.original_name;
          this.all_type_arr.push(item);
          this.getList(key);
        }
        for(var key in this.all_type_arr){
          // this.getList(key);
        }
        // [{name:'',id:'',arr:[]}]
      });
    },
    setLang() {
      this.country = getLang();
    },
    moves(e, index, id) {
      this.page_ = 1;
      this.$store.commit("catePage", this.page_);
      this.select_index = index;
      this.INDEX = index;
      this.$store.commit("setIndex", this.INDEX);
      var screleft = e.target.offsetLeft - this.$jquery("#sdcss")[0].scrollLeft;
      var scrolx = e.target.offsetLeft - (window.innerWidth / 2 - 50);
      this.$jquery("#sdcss").animate(
        {
          scrollLeft: scrolx
        },
        200
      );
      this.getList();
    },
    initBanner() {
      var current = get_banner(this.arr);
      this.arr = current.tags;
    },
    getList1() {
      this.fullscreenLoading = true;
      this.$http("dcb/album/tag/" + this.arr[this.INDEX].id + "/", "get", {
        category: this.category,
        capacity: 1000,
        lang: this.country
      }).then(res => {
        this.fullscreenLoading = false;
        this.list = res.album;
        this.page_ = this.$store.getters.getCatePage;
        document.querySelector(".scroll_box").scrollTop = "0";
      });
    },
    getList(key) {
      console.log(key)
      this.fullscreenLoading = true;
      var pg = this.page_;
      var sd = this.all_type_arr[key];
      var tag = this.all_type_arr[key].original_name;
      this.$http("dcb/album/", "get", {
        tags: tag,
        page: 1,
        category: this.app_type,
        capacity: 9,
        ordering: "-show_cnt",
        lang: this.country
      }).then(res => {
        this.fullscreenLoading = false;
        // this.all_type_arr[key].arr = res.results;
        this.$set(this.all_type_arr[key], 'arr', res.results)
        console.log("xxxxxx");
        console.log(this.all_type_arr);
        // this.total = res.count;
        // this.page_ = this.$store.getters.getCatePage;
        document.querySelector(".scroll_box").scrollTop = "0";
        this.is_mounted = true;
      });
    }
  }
};
</script>

<style lang="less">
@grayy: rgba(82, 82, 82, 1);
.taglist_box {
  .text_top {
    left: 50%;
    top: 50px;
    transform: translate(-50%, -50%);
  }
  .left_big_img {
    &:hover {
      .text_top {
        animation-name: duang;
        animation-fill-mode: forwards;
        animation-duration: 0.6s;
      }
    }
    &:hover .hover_box {
      transform: translateY(0);
    }
    .hover_box {
      transform: translateY(90%);
    }
    // background-image: url("http://assets.niceenjoy.com/DCB/Funny/WHO LET THE DAWGS OUT/cover/5c300ee0bf3411e98b720ab313eb377e.png");
    background-size: 200%;
    background-position: 50% 50%;
    // background: red
  }
  // .right_div_box > div:nth-child(5),.right_div_box > div:nth-child(6),.right_div_box > div:nth-child(7),.right_div_box > div:nth-child(8){
  //     margin-top: 10px;
  // }
  .margin_top_50px {
    margin-top: 49px;
  }
  .cate_border_bottom {
    width: 100%;
    transition: 0.5s;
    position: absolute;
    bottom: 0px;
    height: 3px;
    background: transparent;
  }
  .photntsback {
    &.top0 {
      top: 0;
    }
    z-index: 10003;
    position: fixed;
    top: 50px;
    .dsfx {
      //color:@glore;
      background: darken(@grayy, 10) !important;
      font-weight: 600;
    }
    .ittemstage {
      .selected_cate {
        background: orange;
      }
      &:hover {
        .cate_border_bottom {
          background: orange;
        }
      }
      cursor: pointer;
      position: relative;
      width: 200px;
      background: @grayy;
      color: #fff;
      font-size: 16px;
    }
    .taglibox {
      height: 50px;
      line-height: 50px;
    }
  }
  @media screen and (max-width: 800px) {
    // .cate_border_bottom {
    //      display: none;
    // }
  }
}
</style>
