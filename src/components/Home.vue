<template>
    <el-container class="home-container">
        <!--头部区域-->
        <el-header>
            <div>
                <img src="../assets/icon.jpg" alt="">
                <span>电商后台管理系统</span>
            </div>
            <el-button type="info" @click="logout" style="margin-right: 15px">退出</el-button>
        </el-header>
        <!--页面主题区域-->
        <el-container>
            <!--侧边栏-->
            <el-aside :width="isCollapse ? '64px' : '200px'">
                <div class="toggle-button" @click="toggleCollapse">|||</div>
                <!--侧边栏菜单区域-->
                <el-menu background-color="#333744" text-color="#fff" active-text-color="#409EFF"
                         :unique-opened="true" :collapse="isCollapse" :collapse-transition="false"
                         :router="true" :default-active="activePath">
                    <!--一级菜单-->
                    <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id">
                        <!--一级菜单的模板区-->
                        <template slot="title">
                            <!--图标-->
                            <i :class="iconObj[item.id  ]"></i>
                            <!--文本-->
                            <span>{{item.authName}}</span>
                        </template>
                        <!--二级菜单-->
                        <el-menu-item :index="'/'+subItem.path" v-for="subItem in item.  children"
                                      :key="subItem.id" @click="saveNavState('/'+subItem.path)">
                            <template slot="title">
                                <!--图标-->
                                <i class="el-icon-menu"></i>
                                <!--文本-->
                                <span>{{subItem.authName}}</span>
                            </template>
                        </el-menu-item>
                    </el-submenu>
                </el-menu>
            </el-aside>
            <!--右侧内容主题-->
            <el-main>
                <!--路由占位符-->
                <router-view></router-view>
            </el-main>
        </el-container>
    </el-container>
</template>

<script>
    export default {
        data() {
            return {
                menuList: [],
                iconObj: {
                    '125': 'el-icon-user',
                    '103': 'el-icon-lock',
                    '101': 'el-icon-shopping-bag-1',
                    '102': 'el-icon-tickets',
                    '145': 'el-icon-coin'
                },
                //是否折叠
                isCollapse: false,
                //被激活的链接地址
                activePath: '',
            }
            //左侧菜单数据
        },
        created() {
            this.getMenuList()
            this.activePath = window.sessionStorage.getItem('activePath')
        },
        methods: {
            logout() {
                window.sessionStorage.clear()
                this.$router.push("/login")
            },
            async getMenuList() {
                const {data: res} = await this.$http.get('menus')
                if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
                this.menuList = res.data
            },
            //点击按钮，切换菜单的折叠与展开
            toggleCollapse() {
                this.isCollapse = !this.isCollapse
            },
            //保存链接的激活状态
            saveNavState(activePath) {
                window.sessionStorage.setItem('activePath', activePath)
                this.activePath = activePath
            }
        }
    }
</script>

<style lang="less" scoped>
    .home-container {
        height: 100%;
    }

    .el-header {
        background-color: #373D41;
        display: flex;
        justify-content: space-between;
        padding: 0;
        align-items: center;
        color: #fff;
        font-size: 20px;

        > div {
            display: flex;
            align-items: center;

            span {
                margin-left: 10px;
            }
        }
    }

    .el-aside {
        background-color: #333744;

        .el-menu {
            border-right: none;
        }
    }

    .el-main {
        background-color: #EAEDF1;
    }

    .toggle-button {
        background-color: #4A5064;
        font-size: 10px;
        line-height: 24px;
        color: #ffffff;
        text-align: center;
        letter-spacing: 0.2em;
        cursor: pointer;
    }
</style>