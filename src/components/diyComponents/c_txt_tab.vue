<template>
    <div class="txt_tab" v-if="datas[name]">
        <div class="c_row-item">
            <Col class="c_label">
                {{datas[name].title}}
                <span>{{datas[name].list[datas[name].type].val}}</span>
            </Col>
            <Col class="color-box">
                <RadioGroup v-model="datas[name].type" type="button" @on-change="radioChange($event)">
                    <Radio :label="key" v-for="(radio,key) in datas[name].list" :key="key">
                        <span class="iconfont-diy" :class="radio.icon" v-if="radio.icon"></span>
                        <span v-else>{{radio.val}}</span>
                    </Radio>
                </RadioGroup>
            </Col>
        </div>
    </div>

</template>

<script>
    export default {
        name: 'c_txt_tab',
        props: {
            name: {
                type: String
            },
            configData:{
                type:null
            }
        },
        data () {
            return {
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
                immediate: true,
                deep: true
            }
        },
        methods: {
            radioChange (e) {
                this.$emit('getConfig', { name: 'radio', values: e })
            }
        }
    }
</script>

<style scoped lang="stylus">
    .txt_tab
        margin-top 20px
    .c_row-item
        margin-bottom 20px
    .row-item
        display flex
        justify-content space-between
        align-items center
    .iconfont
        font-size 18px
</style>
