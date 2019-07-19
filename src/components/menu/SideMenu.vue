<template>
  <a-layout-sider
    :class="['sider', isDesktop() ? null : 'shadow', theme, fixSiderbar ? 'ant-fixed-sidemenu' : null ]"
    width="222px"
    :collapsible="collapsible"
    v-model="collapsed"
    :trigger="null"
  >
    <logo />
    <s-menu
      :collapsed="collapsed"
      :menu="menus"
      :theme="theme"
      @select="onSelect"
      :mode="mode"
      :style="smenuStyle"
    ></s-menu>
  </a-layout-sider>
</template>

<script>
import ALayoutSider from 'ant-design-vue/es/layout/Sider'
import Logo from '../tools/Logo'
import SMenu from './index'
import { mixin, mixinDevice } from '@/utils/mixin.js'

export default {
  name: 'SideMenu',
  components: { ALayoutSider, Logo, SMenu },
  mixins: [mixin, mixinDevice],
  props: {
    mode: {
      type: String,
      required: false,
      default: 'inline'
    },
    theme: {
      type: String,
      required: false,
      default: 'dark'
    },
    collapsible: {
      type: Boolean,
      required: false,
      default: false
    },
    collapsed: {
      type: Boolean,
      required: false,
      default: false
    },
    menus: {
      type: Array,
      required: true
    }
  },
  computed: {
    smenuStyle() {
      let style = { paddingTop: '12px' }
      if (this.fixSiderbar) {
        style['height'] = 'calc(100% - 59px)'
        style['overflow'] = 'auto'
        style['overflow-x'] = 'hidden'
      }
      return style
    }
  },
  methods: {
    onSelect(obj) {
      this.$emit('menuSelect', obj)
    }
  }
}
</script>
<style lang="scss" scoped>
/* update_begin author:sunjianlei date:20190509 for: 修改侧边导航栏滚动条的样式 */
.sider {
  $scrollBarSize: 10px;
  :global(.ant-layout-sider-children){
    >:global(.ant-menu) {
        >:global(li.ant-menu-submenu.ant-menu-submenu-inline){
          :global(.ant-menu-submenu-title){
            font-size: 16px;
          }
        }
      
    }
  }
  :global(.ant-menu.ant-menu-inline.ant-menu-sub) {
    font-size: 16px;

    :global(li.ant-menu-item) {
      padding: 0 !important;
    }

    :global(li.ant-menu-item.ant-menu-item-selected > a) {
      border-radius: 4px !important;
      /* text-align: center; */
    }
    :global(li.ant-menu-item > a) {
      /* text-align: center; */
      padding-left: 23px;
      margin: 0 auto;
    }
    // 二级菜单
    /* :global(.ant-menu-item > a ) {
      text-align: center;
      width: 174px;
      margin: 0 auto;
      border-radius: 4px;
    } */
    // 二级菜单标题
    /* :global(li>div){
      text-align: center;
      width: 174px;
    } */

    /* :global(.ant-menu-item, .ant-menu-item.ant-menu-item-selected) {
      padding: 0 !important;
    } */
  }

  ul.ant-menu {
    /* 定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
    &::-webkit-scrollbar {
      width: $scrollBarSize;
      height: $scrollBarSize;
      background-color: transparent;
      display: none;
    }

    & .-o-scrollbar {
      display: none;
    }

    /* 兼容IE */
    -ms-overflow-style: none;
    -ms-scroll-chaining: chained;
    -ms-content-zooming: zoom;
    -ms-scroll-rails: none;
    -ms-content-zoom-limit-min: 100%;
    -ms-content-zoom-limit-max: 500%;
    -ms-scroll-snap-type: proximity;
    -ms-scroll-snap-points-x: snapList(100%, 200%, 300%, 400%, 500%);

    /* 定义滚动条轨道 */
    &::-webkit-scrollbar-track {
      background-color: transparent;
    }

    /* 定义滑块 */
    &::-webkit-scrollbar-thumb {
      border-radius: $scrollBarSize;
      background-color: #eee;
      box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);

      &:hover {
        background-color: #dddddd;
      }

      &:active {
        background-color: #bbbbbb;
      }
    }
  }

  /** 暗色系滚动条样式 */
  &.dark ul.ant-menu {
    &::-webkit-scrollbar-thumb {
      background-color: #666666;

      &:hover {
        background-color: #808080;
      }

      &:active {
        background-color: #999999;
      }
    }
  }
}
:global(.ant-menu-item > a) {
  border-radius: 4px !important;
  text-align: center !important;
}
/* update_end author:sunjianlei date:20190509 for: 修改侧边导航栏滚动条的样式 */
</style>