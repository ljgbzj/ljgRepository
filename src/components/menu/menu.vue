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
                  :class="{chosed:thirdMenu.id == ischosed}"
                  @click="chosed(thirdMenu,secondMenu,index)"
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
        <img src="@/assets/img/menu/allmenu.png" @click="toggleAll" />
      </div>
    </div>

    <!-- 弹出框 -->
    <div class="out" ref="out" @mouseleave="hiddenAll">
      <div>
        <!-- 一级菜单 -->
        <ul class="su-menu-list FirstMenu">
          <li
            v-for="(item, index) in menu"
            :key="index"
            class="su-menu-item"
            :class="{active: isActive == index}"
            v-if="item.meta.title !== '首页'"
          >
            <div class="item-container">
              <div class="img-container">
                <img :src="require(`@/assets/img/menu/${item.meta.icon}.png`)" />
              </div>

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
                  <li v-for="(thirdMenu, key) in secondMenu.children" :key="key" class="thirdItem">
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
      isChecked: null,
      ischosed: null,
      visible: false
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
    chosed(thirdMenu, secondMenu, index) {
      this.ischosed = thirdMenu.id
      this.isChecked = secondMenu.id
      this.isActive = index
    },
    active(index) {
      this.isActive = index
      this.ischosed = null
    },
    inactive() {
      this.isActive = null
    },
    toggleAll() {
      this.visible = !this.visible
      if (this.visible == true) {
        this.$refs.out.style.visibility = 'visible'
      } else {
        this.$refs.out.style.visibility = 'hidden'
      }
    },
    hiddenAll() {
      this.visible = false
      this.$refs.out.style.visibility = 'hidden'
    }
  }
}
</script>

<style lang="less" scoped>
.su-menu-container {
  display: flex;
  height: 64px;
  position: relative;
  // 顶部导航条
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
              padding: 0 14px;

              div.secondMenuTitle {
                color: #333333;

                & + .thirdMenu {
                  /* background-color: #fff; */
                  .thirdItem {
                    padding-left: 14px;
                  }

                  .thirdItem:hover,
                  .thirdItem.chosed {
                    border-radius: 4px;
                    background-color: #6d62ff;
                    a {
                      color: #fff;
                    }
                  }
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
                & > a {
                  color: #6d62ff;
                }
              }
            }
          }
        }

        .item-container {
          display: flex;
          align-items: center;
          justify-content: space-around;
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
  .out {
    width: 708px;
    height: unset;
    position: absolute;
    right: -200px;
    top: 50px;
    z-index: 999;
    box-shadow: 0px 12px 16px 0px rgba(170, 170, 170, 0.4);
    border-radius: 4px;
    background: #fff;
    visibility: hidden;

    .su-menu-list.FirstMenu {
      display: flex;
      flex-wrap: wrap;
      background-color: #fff;
      border-radius: 4px;
      padding-top: 28px;

      .su-menu-item {
        margin-left: 36px;
        margin-bottom: 12px;

        .item-container {
          display: flex;
          flex-direction: column;
          align-items: center;
          width: 96px;
          height: 96px;
          padding-top: 12px;
          &:hover {
            background-color: #f2f2f2;
          }

          .img-container {
            background-color: #6d62ff;
            width: 48px;
            height: 48px;
            line-height: 48px;
            text-align: center;
          }
          span {
            line-height: 32px;
          }
        }

        .secondMenu {
          display: none;
        }
        &:hover {
          .secondMenu {
            position: absolute;
            display: block;
            background-color: #fff;
            width: 200px;
            border-radius: 4px;
            box-shadow: 0px 12px 16px 0px rgba(170, 170, 170, 0.4);
            padding: 17px 16px;
            z-index: 999;
            max-height: 500px;
            overflow-y: scroll;

            @scrollBarSize: 6px;
            /* 定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
            &::-webkit-scrollbar {
              width: @scrollBarSize;
              height: @scrollBarSize;
              background-color: transparent;
            }
            /* 定义滚动条轨道 */
            &::-webkit-scrollbar-track {
              background-color: #f0f0f0;
            }
            /* 定义滑块 */
            &::-webkit-scrollbar-thumb {
              background-color: #eee;
              box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);

              &:hover {
                background-color: #bbb;
              }

              &:active {
                background-color: #888;
              }
            }

            li.secondItem {
              padding-left: 14px;

              div.secondMenuTitle {
                & + .thirdMenu {
                  /* background-color: #fff; */
                  .thirdItem {
                    padding-left: 14px;
                  }

                  .thirdItem:hover,
                  .thirdItem.chosed {
                    border-radius: 4px;
                    background-color: #6d62ff;
                    a {
                      color: #fff;
                    }
                  }
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
                & > a {
                  color: #6d62ff;
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>
