<template>
  <div
    class="d2-layout-header-aside-group"
    :style="styleLayoutMainGroup"
    :class="{ grayMode: grayActive }"
  >
    <!-- 半透明遮罩 -->
    <div class="d2-layout-header-aside-mask"></div>
    <!-- 主体内容 -->
    <div class="d2-layout-header-aside-content" flex="dir:top">
      <!-- 顶栏 -->
      <div
        class="d2-theme-header"
        :style="{ opacity: this.searchActive ? 0.5 : 1 }"
        flex-box="0"
        flex
      >
        <router-link
          to="/index"
          :class="{ 'logo-group': true, 'logo-transition': asideTransition }"
          :style="{ width: asideCollapse ? asideWidthCollapse : asideWidth }"
          flex-box="0"
        >
          <h2
            v-show="hidden"
            style="color: dodgerblue;font-size: 1.25em"
            v-if="
              themeActiveSetting.name === 'd2' ||
                themeActiveSetting.name === 'line'
            "
          >
            Blog Adain
          </h2>
          <h2 style="color: white;font-size: 1.15em" v-else>My Blog</h2>
        </router-link>
        <div
          v-show="hidden"
          class="toggle-aside-btn"
          @click="handleToggleAside"
          flex-box="0"
        >
          <d2-icon name="bars" />
        </div>
        <!-- <d2-menu-header flex-box="1"/> -->
        <!-- 顶栏右侧 -->
        <div class="d2-header-right">
          <!-- 如果你只想在开发环境显示这个按钮请添加 v-if="$env === 'development'" -->
          <!-- <d2-header-search @click="handleSearchClick"/> -->
          <template v-show="hidden">
            <d2-header-user />
            <d2-header-log />
          </template>
          <d2-header-show />
        </div>
      </div>
      <div v-show="hidden" style="width: 100%;height: 100%;">
        <!-- 下面 主体 -->
        <div
          class="d2-theme-container"
          flex-box="1"
          flex
          style="width: 100%;height: 100%;"
        >
          <!-- 主体 侧边栏 -->
          <div
            flex-box="0"
            ref="aside"
            :class="{
              'd2-theme-container-aside': true,
              'd2-theme-container-transition': asideTransition
            }"
            :style="{
              width: asideCollapse ? asideWidthCollapse : asideWidth,
              opacity: this.searchActive ? 0.5 : 1
            }"
          >
            <d2-menu-side />
          </div>
          <!-- 主体 -->
          <div class="d2-theme-container-main" flex-box="1" flex>
            <!-- 搜索 -->
            <transition name="fade-scale">
              <div
                v-if="searchActive"
                class="d2-theme-container-main-layer"
                flex
              >
                <d2-panel-search ref="panelSearch" @close="searchPanelClose" />
              </div>
            </transition>
            <!-- 内容 -->
            <transition name="fade-scale">
              <div
                v-if="!searchActive"
                class="d2-theme-container-main-layer"
                flex="dir:top"
              >
                <!-- tab -->
                <div class="d2-theme-container-main-header" flex-box="0">
                  <d2-tabs />
                </div>
                <!-- 页面 -->
                <div class="d2-theme-container-main-body" flex-box="1">
                  <transition :name="transitionActive ? 'fade-transverse' : ''">
                    <keep-alive :include="keepAlive">
                      <router-view />
                    </keep-alive>
                  </transition>
                </div>
              </div>
            </transition>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import d2Tabs from "./components/tabs";
import d2MenuSide from "./components/menu-side";
import d2HeaderUser from "./components/header-user";
import d2HeaderLog from "./components/header-log";
import d2HeaderShow from "./components/header-show";
import { mapActions, mapGetters, mapState } from "vuex";
import mixinSearch from "./mixins/search";

export default {
  name: "d2-layout-header-aside",
  mixins: [mixinSearch],
  components: {
    d2MenuSide,
    d2HeaderUser,
    d2HeaderLog,
    d2HeaderShow,
    d2Tabs
  },
  data() {
    return {
      // [侧边栏宽度] 正常状态
      asideWidth: "200px",
      // [侧边栏宽度] 折叠状态
      asideWidthCollapse: "60px"
    };
  },
  computed: {
    ...mapState("d2admin", {
      keepAlive: state => state.page.keepAlive,
      grayActive: state => state.gray.active,
      transitionActive: state => state.transition.active,
      asideCollapse: state => state.menu.asideCollapse,
      asideTransition: state => state.menu.asideTransition,
      hidden: state => state.common.hidden
    }),
    ...mapGetters("d2admin", {
      themeActiveSetting: "theme/activeSetting"
    }),
    /**
     * @description 最外层容器的背景图片样式
     */
    styleLayoutMainGroup() {
      return this.themeActiveSetting.backgroundImage
        ? {
            backgroundImage: `url('${this.$baseUrl}${this.themeActiveSetting.backgroundImage}')`
          }
        : {};
    }
  },
  methods: {
    ...mapActions("d2admin/menu", ["asideCollapseToggle"]),
    /**
     * 接收点击切换侧边栏的按钮
     */
    handleToggleAside() {
      this.asideCollapseToggle();
    }
  },
  mounted() {
    console.log(this.hidden);
  }
};
</script>

<style lang="scss">
// 注册主题
@import "~@/assets/style/theme/register.scss";

.d2-header-right {
  width: 100%;
  display: flex;
  padding: 0 0 0.5rem 0.5rem;
  flex-direction: row-reverse;
}
</style>
