<template>
    <div class="line-box">
        <div class="title">
            <p>搜索热词</p>
            <span>{{datas[name].title}}</span>
        </div>
        <div class="input-box">
            <draggable
                    class="dragArea list-group"
                    :list="datas[this.name].list"
                    group="peoples"
                    handle=".icon"
            >
                <div class="input-item" v-for="(item,index) in datas[this.name].list" :key="index">
                    <div class="icon"><Icon type="ios-keypad" size="20" /></div>
                    <Input v-model="item.val" placeholder="选填，不超过十个字" :maxlength="item.maxlength" />
                    <div class="close" @click="close(index)"><Icon type="md-close" size="20" style="color:#D8D8D8"/></div>
                </div>
            </draggable>
            <div class="add-btn" @click="addHotTxt">
                <Button type="primary" ghost style="width: 100%; height: 40px;border-color:#1890FF; color: #1890FF;">添加热词</Button>
            </div>
        </div>
    </div>
</template>
<script>
    import vuedraggable from 'vuedraggable'
    export default {
        name: 'c_hot_word',
        props: {
            name: {
                type: String
            },
            configData:{
                type:null
            }
        },
        components: {
            draggable: vuedraggable
        },
        data () {
            return {
                hotWordList: [],
                hotIndex: 1,
                defaults: {},
                datas: {}
            }
        },
        mounted () {
            this.$nextTick(()=>{
                this.datas = this.configData
            })
        },
        watch: {
            configData: {
                handler (nVal, oVal) {
                    this.datas = nVal
                },
                deep: true
            }
        },
        methods: {
            addHotTxt () {
                let obj = {}
                if(this.datas[this.name].list.length>0){
                    obj= JSON.parse(JSON.stringify(this.datas.hotList.list[this.datas.hotList.list.length - 1]))
                }else{
                    obj= {
                        val:''
                    }
                }

                this.datas[this.name].list.push(obj)
            },
            close(index){
                this.datas[this.name].list.splice(index,1)
            }
        }
    }
</script>

<style scoped lang="stylus">
    .line-box
        margin-top 20px
        padding 10px 0 20px
        border-top 1px solid rgba(0,0,0,.05)
        border-bottom 1px solid rgba(0,0,0,.05)
        .title
            p
                font-size 14px
                color #000000
            span
                color #999999
        .input-box
            position: relative;
            margin-top 10px
            .add-btn
                margin-top 18px
            .input-item
                position: relative;
                display flex
                align-items center
                margin-bottom 15px
                .icon
                    display flex
                    align-items center
                    justify-content center
                    width 36px
                    cursor move
                    color #D8D8D8
                /deep/.ivu-input
                    flex 1
                    height 36px
            .close
                position absolute
                right 10px
                top 50%
                transform translateY(-50%)
                cursor pointer

</style>
