<template>
  <div class="su-menu-container">
    <div class="menu-scroll-wrapper">
      <!-- 一级菜单 -->
      <ul class="su-menu-list FirstMenu" ref="menuList">
        <li
          v-for="(item, index) in menu"
          :key="index"
          ref="secondaryMenu"
          class="su-menu-item"
          :class="{active: isActive == index}"
          v-if="item.meta.title !== '首页'"
        >
          <div class="item-container">
            <img :src="require(`@/assets/img/menu/${item.meta.icon}.png`)" />
            <span>{{item.meta.title}}</span>
          </div>

          <!-- 二级菜单 -->
          <ul v-if="item.children" class="secondMenu">
            <li
              v-for="(secondMenu, i) in item.children"
              :key="i"
              class="secondItem"
              :class="{checked: secondMenu.id == isChecked}"
              @click="checked(secondMenu, index)"
            >
              <keep-alive v-if="secondMenu.meta.keepAlive">
                <router-link :to="secondMenu.path" v-if="!secondMenu.children">
                  <div>{{secondMenu.meta.title}}</div>
                </router-link>
                <div class="secondMenuTitle" v-else>
                  {{secondMenu.meta.title}}
                  <span></span>
                </div>
              </keep-alive>

              <!-- 三级菜单 -->
              <ul v-if="secondMenu.children" class="thirdMenu">
                <li 
                  v-for="(thirdMenu, key) in secondMenu.children" 
                  :key="key"
                  class="thirdItem"
                  >
                  <keep-alive>
                    <router-link :to="thirdMenu.path">
                      <div>{{thirdMenu.meta.title}}</div>
                    </router-link>
                  </keep-alive>
                </li>

              </ul>
            </li>
          </ul>
        </li>
      </ul>
    </div>

    <div class="su-menu-control">
      <div class="scroll">
        <img src="@/assets/img/menu/up.png" @click="up" />
        <img src="@/assets/img/menu/down.png" @click="down" />
      </div>
      <div class="all">
        <img src="@/assets/img/menu/allmenu.png" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SuMenu',
  data() {
    return {
      menuTop: 0,
      menuheight: 0, // 菜单实际高度
      isActive: null,
      isChecked: null
    }
  },
  props: {
    menu: {
      type: Array,
      required: true
    },
    theme: {
      type: String,
      required: false,
      default: 'dark'
    },
    mode: {
      type: String,
      required: false,
      default: 'inline'
    },
    collapsed: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  watch: {
    menuheight: function(val) {
      this.menuheight = val
      this.$refs.menuList.style.top = 0
    }
  },
  mounted() {
    const that = this
    window.onresize = () => {
      return (() => {
        if (this.$refs.menuList) {
          this.menuheight = this.$refs.menuList.clientHeight
        }
      })()
    }
  },
  methods: {
    update() {
      console.log(this.menu)
      console.log(this.$refs.menuList.clientHeight)
    },
    up() {
      this.$refs.menuList.style.top = `${this.menuTop}px`
      let top = this.$refs.menuList.style.top
      top = Number(top.replace('px', ''))
      let menuHeight = this.$refs.menuList.clientHeight
      if (top < 0) {
        top += 64
        this.$refs.menuList.style.top = top + 'px'
        this.menuTop = top
      } else {
        console.log('到顶了')
      }
    },
    down() {
      this.$refs.menuList.style.top = `${this.menuTop}px`
      let top = this.$refs.menuList.style.top
      top = Number(top.replace('px', ''))
      let menuHeight = this.$refs.menuList.clientHeight
      if (menuHeight + top > 64) {
        top += -64
        this.$refs.menuList.style.top = top + 'px'
        this.menuTop = top
      } else {
        console.log('到底了')
      }
    },
    checked(item, index) {
      this.isChecked = item.id
      this.isActive = index
    },
    active(index) {
      this.isActive = index
    },
    inactive() {
      this.isActive = null
    }
  }
}
</script>

<style lang="less" scoped>
.su-menu-container {
  display: flex;
  height: 64px;
  .menu-scroll-wrapper {
    flex: 1 1;
    position: relative;

    .su-menu-list.FirstMenu {
      position: absolute;
      top: 0;
      margin-bottom: 0;
      display: flex;
      flex-wrap: wrap;

      .su-menu-item {
        width: 120px;
        color: #fff;
        padding: 10px;
        height: 64px;
        width: 120px;
        box-sizing: border-box;
        line-height: 44px;

        .secondMenu {
          display: none;
        }
        &:hover {

          .secondMenu {
            position: relative;
            display: block;
            background-color: #fff;
            width: 200px;
            border-radius: 4px;
            box-shadow: 0px 12px 16px 0px rgba(170, 170, 170, 0.4);
            padding: 17px 16px;
            z-index: 999;

            li.secondItem {
              padding-left: 14px;

              div.secondMenuTitle{
                color: #333333;
                & + .thirdMenu{
                  background-color: #fff;
                  padding-left: 14px;
                }


              }

              a {
                color: #333333;
                text-decoration: none;
              }
              &:hover,
              &.checked {
                background: rgba(109, 98, 255, 0.1);
                border-radius: 4px;
                &>a {
                  color: #6d62ff;
                }
              }



            }
          }
        }

        .item-container {
          display: flex;
          align-items: center;
          justify-content: center;
          img {
            width: 24px;
            height: 24px;
          }
        }
        &:hover .item-container,
        &.active .item-container {
          cursor: pointer;
          background-color: rgba(0, 0, 0, 0.1);
          border-radius: 4px;
        }
      }
    }
  }
  .su-menu-control {
    width: 104px;
    display: flex;
    .scroll {
      height: 64px;
      width: 16px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      margin: 0 24px;
      img {
        height: 8px;
        width: 16px;
        opacity: 0.4;
        &:hover {
          cursor: pointer;
          opacity: 1;
        }
      }
    }
    .all {
      display: flex;
      justify-content: center;
      flex-direction: column;
      height: 64px;
      img {
        width: 16px;
        height: 16px;
        opacity: 0.4;
        &:hover {
          cursor: pointer;
          opacity: 1;
        }
      }
    }
  }
}
</style>
