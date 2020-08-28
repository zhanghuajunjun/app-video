<template>
    <view>
        <uni-search-bar placeholder="请输入电影信息" @confirm="search" confirm-type="search" @cancel="cancel"
            v-model="keywords"></uni-search-bar>
        <view class="box">
            <view class="container">
                <view v-for="(item,index) in searchList" :key="index" class="content" :class="{'active':(index-1)%3==0}">
                    <image :src="item.cover" class="img" @click="goDetail(item)"></image>
                    <view class="item_name">{{item.name}}</view>
                </view>
            </view>
        </view>
    </view>
</template>

<script>
    import uniSearchBar from '@/components/uni-search-bar/uni-search-bar.vue'
    export default {
        name: "",
        components: {
            uniSearchBar
        },
        props: {},
        data() {
            return {
                keywords: '',
                page: 1,
                pageSize: 15,
                searchList: []
            }
        },
        methods: {
            postData() {
                uni.request({
                    url: `${this.$api}/search/list?qq=1844532477&keywords=${this.keywords}&page=${this.page}&pageSize=${this.pageSize}`,
                    method: 'POST',
                    data: {},
                    success: res => {
                        this.searchList = res.data.data.rows
                    },
                })
            },
            search(e) {
                this.keywords = e.value
                this.postData()
            },
            cancel() {
                this.keywords = ''
                this.postData()
            },
            goDetail(item) {
                uni.navigateTo({
                    url: `/pages/detail/detail?item=${JSON.stringify(item)}`
                })
            }
        },
        mounted() {

        },
        onLoad() {
            this.postData()
        },
        filters: {

        },
        computed: {

        },
        watch: {

        },
        directives: {

        }
    }
</script>

<style scoped lang="scss">
    .box{
        padding: 0 20rpx;
    }
    .container{
        display: flex;
        flex-wrap: wrap;
    }
    .content{
       width: 220rpx;
       height: 380rpx; 
       margin-top: 20rpx;
    }
    .img {
        width: 220rpx;
        height: 300rpx;
    }
    .item_name {
        font-size: 28rpx;
    }
    .active {
        margin-left: 25rpx;
        margin-right: 25rpx;
    }
</style>
