<style scoped>
    .layout{
        border: 1px solid #d7dde4;
        background: #f5f7f9;
        position: relative;
        border-radius: 4px;
        overflow: hidden;
        height: 100%;
        width: 100%;
    }
    .container{
        height: 100%;
    }
    .layout-breadcrumb{
        padding: 8px 15px 0;
    }
    .layout-content{
        width: 100%;
        height: 100%;
        overflow: hidden;
        background: #F0F0F0;
        border-radius: 4px;
        position: relative;
    }
    .layout-copy{
        text-align: center;
        padding: 10px 0 20px;
        color: #9ea7b4;
    }
    .layout-menu-left{
        background: #464c5b;
        height: 100%;
    }
    .layout-header{
        height: 60px;
        background: #fff;
        box-shadow: 0 1px 1px rgba(0,0,0,.1);
        position: relative;
    }
    .navicon-con{
        margin: 6px;
        display: inline-block;
    }
    .header-middle-con{
        position: absolute;
        left: 60px;
        top: 0;
        right: 200px;
        bottom: 0;
        padding: 10px;
        overflow: hidden;
    }
    .layout-logo-left{
        width: 90%;
        height: 30px;
        background: #5b6270;
        border-radius: 3px;
        margin: 15px auto;
    }
    .layout-ceiling-main a{
        color: #9ba7b5;
    }
    .ivu-col{
        transition: width .2s ease-in-out;
    }
    .tags-con{
        height: 40px;
        padding: 2px 10px;
    }
    .single-page-con{
        position: absolute;
        top: 40px;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: white;
    }
</style>
<template>
    <div class="layout" :class="{'layout-hide-text': spanLeft < 4}">
        <Row type="flex" class="container">
            <Col :span="spanLeft" class="layout-menu-left">
                <sidebar-menu :routers="menuList" :iconSize="iconSize">
                    <div slot="top" class="layout-logo-left">logo</div>
                </sidebar-menu>
            </Col>
            <Col :span="spanRight">
                <div class="layout-header">
                    <div class="navicon-con">
                        <Button type="text" @click="toggleClick">
                            <Icon type="navicon" size="32"></Icon>
                        </Button>
                    </div>
                    <div class="header-middle-con">
                        <div class="layout-breadcrumb">
                            <breadcrumb-nav :currentPath="currentPath"></breadcrumb-nav>
                        </div>
                    </div>
                </div>
                
                <div class="layout-content">
                    <div class="tags-con">
                        <tags-page-opened :pageTagsList="page_tags_list"></tags-page-opened>
                    </div>
                    <div class="single-page-con">
                        <router-view></router-view>
                    </div>
                </div>
                <div class="layout-copy">
                    2011-2016 &copy; TalkingData
                </div>
            </Col>
        </Row>
    </div>
</template>
<script>
    import sidebarMenu from './main_components/sidebarMenu.vue';
    import tagsPageOpened from './main_components/tagsPageopened.vue';
    import breadcrumbNav from './main_components/breadcrumbNav.vue';
    import util from './util.js';
    
    export default {
        components: {
            sidebarMenu,
            tagsPageOpened,
            breadcrumbNav
        },
        data () {
            return {
                spanLeft: 4,
                spanRight: 20,
                menuList: this.$store.state.app_router,
                tags_list: this.$store.state.tags_list,  //所有页面的页面对象
                page_tags_list: this.$store.state.pageOpenedList,  //打开的页面的页面对象
                currentPath: this.$store.state.currentPath,  //当前面包屑数组
                currentPageName: ''
            }
        },
        computed: {
            iconSize () {
                return this.spanLeft === 4 ? 14 : 24;
            }
        },
        methods: {
            init() {
                let tagsList = [];
                this.menuList.map((item) => {
                    if(item.children.length <= 1){
                        tagsList.push(item);
                    }else{
                        tagsList.push(...item.children)
                    }
                })
                this.$store.commit('setTagsList', tagsList);
                this.$store.commit('setCurrentPageName', this.$route.name);

                let pathArr = util.setCurrentPath(this, this.$route.name);
                if(pathArr.length>2){
                    this.$store.commit('addOpenSubmenu', pathArr[1].name);
                }
            },
            toggleClick () {
                if (this.spanLeft === 4) {
                    this.spanLeft = 1;
                    this.spanRight = 23;
                } else {
                    this.spanLeft = 4;
                    this.spanRight = 20;
                }
            }
        },
        watch: {
            '$route' (to, from) {
                this.$store.commit('setCurrentPageName', to.name)
            }
        },
        mounted () {
            this.init()
        }
    }
</script>
