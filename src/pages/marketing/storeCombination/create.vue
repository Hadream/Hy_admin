<template>
    <div>
        <div class="i-layout-page-header">
          <div class="i-layout-page-header">
            <router-link :to="{path:'/admin/marketing/store_combination/index'}"><Button icon="ios-arrow-back" size="small"  class="mr20">返回</Button></router-link>
            <span class="ivu-page-header-title mr20" v-text="$route.params.id?'编辑拼团商品':'添加拼团商品'"></span>
          </div>
        </div>
        <Card :bordered="false" dis-hover class="ivu-mt">
            <Row type="flex" class="mt30 acea-row row-middle row-center">
                <Col span="20">
                    <Steps :current="current">
                        <Step title="选择拼团商品"></Step>
                        <Step title="填写基础信息"></Step>
                        <Step title="修改商品详情"></Step>
                    </Steps>
                </Col>
                <Col span="23">
                    <Form class="form mt30" ref="formValidate" :model="formValidate" :rules="ruleValidate" @on-validate="validate"
                          :label-width="labelWidth" :label-position="labelPosition" @submit.native.prevent>
                        <FormItem label="选择商品：" prop="image_input" v-if="current === 0">
                            <div class="picBox" @click="changeGoods">
                                <div class="pictrue" v-if="formValidate.image"><img
                                        v-lazy="formValidate.image"></div>
                                <div class="upLoad acea-row row-center-wrapper" v-else>
                                    <Icon type="ios-camera-outline" size="26"/>
                                </div>
                            </div>
                        </FormItem>
                        <Row v-show="current === 1" type="flex">
                            <Col span="24">
                                <FormItem label="商品主图：" prop="image">
                                    <div class="picBox" @click="modalPicTap('dan','danFrom')">
                                        <div class="pictrue" v-if="formValidate.image"><img v-lazy="formValidate.image"></div>
                                        <div class="upLoad acea-row row-center-wrapper" v-else>
                                            <Icon type="ios-camera-outline" size="26"/>
                                        </div>
                                    </div>
                                </FormItem>
                            </Col>
                            <Col span="24">
                                <FormItem label="商品轮播图：" prop="images">
                                    <div class="acea-row">
                                        <div class="pictrue" v-for="(item,index) in formValidate.images" :key="index"
                                             draggable="true"
                                             @dragstart="handleDragStart($event, item)"
                                             @dragover.prevent="handleDragOver($event, item)"
                                             @dragenter="handleDragEnter($event, item)"
                                             @dragend="handleDragEnd($event, item)">
                                            <img v-lazy="item">
                                            <Button shape="circle" icon="md-close"  @click.native="handleRemove(index)" class="btndel"></Button>
                                        </div>
                                        <div class="upLoad acea-row row-center-wrapper" @click="modalPicTap('duo')">
                                            <Icon type="ios-camera-outline" size="26"/>
                                        </div>
                                    </div>
                                </FormItem>
                            </Col>
                            <Col span="24">
                                <Col v-bind="grid">
                                    <FormItem label="拼团名称：" prop="title" label-for="title">
                                        <Input placeholder="请输入拼团名称" element-id="title" v-model="formValidate.title"/>
                                    </FormItem>
                                </Col>
                            </Col>
                            <Col span="24">
                                <Col v-bind="grid">
                                    <FormItem label="拼团简介：" prop="info" label-for="info">
                                        <Input placeholder="请输入拼团简介" type="textarea" :rows="4" element-id="info" v-model="formValidate.info"/>
                                    </FormItem>
                                </Col>
                            </Col>
                            <Col v-bind="grid2">
                                <FormItem label="拼团时间：" prop="section_time">
                                    <DatePicker :editable="false" type="datetimerange" format="yyyy-MM-dd HH:mm" placeholder="请选择活动时间"
                                                @on-change="onchangeTime" class="perW100" :value="formValidate.section_time" v-model="formValidate.section_time"></DatePicker>
                                </FormItem>
                            </Col>
                            <Col v-bind="grid2">
                                <FormItem label="单位：" prop="unit_name" label-for="unit_name">
                                    <Input placeholder="请输入单位" element-id="unit_name" v-model="formValidate.unit_name"/>
                                </FormItem>
                            </Col>
                            <Col v-bind="grid2">
                                <FormItem label="拼团时效(单位 小时)：" prop="effective_time">
                                    <InputNumber placeholder="请输入拼团时效(单位 小时)" class="perW100" element-id="effective_time"  v-model="formValidate.effective_time"/>
                                </FormItem>
                            </Col>
                            <!--<Col v-bind="grid2">-->
                                <!--<FormItem label="拼团价：" prop="price" label-for="price">-->
                                    <!--<InputNumber placeholder="请输入拼团价" element-id="price"  v-model="formValidate.price"/>-->
                                <!--</FormItem>-->
                            <!--</Col>-->
                            <Col v-bind="grid2">
                                <FormItem label="拼团人数：" prop="people">
                                    <InputNumber placeholder="请输入拼团人数" :precision="0" element-id="people"  v-model="formValidate.people" class="perW100"/>
                                </FormItem>
                            </Col>
                            <!--<Col v-bind="grid2">-->
                                <!--<FormItem label="库存：" prop="stock" label-for="stock">-->
                                    <!--<InputNumber placeholder="请输入库存" :min="1" :precision="0" element-id="stock"  v-model="formValidate.stock"/>-->
                                <!--</FormItem>-->
                            <!--</Col>-->
                            <!--<Col v-bind="grid2">-->
                                <!--<FormItem label="虚拟销量：">-->
                                    <!--<InputNumber placeholder="请输入虚拟销量" :precision="0" element-id="sales"  v-model="formValidate.sales" style="width: 100%"/>-->
                                <!--</FormItem>-->
                            <!--</Col>-->
                            <Col v-bind="grid2">
                                <FormItem label="排序：">
                                    <InputNumber placeholder="请输入排序" element-id="sort" :precision="0"  v-model="formValidate.sort" class="perW100"/>
                                </FormItem>
                            </Col>
                            <Col v-bind="grid2">
                                <FormItem label="运费模板：" prop="temp_id">
                                    <div>
                                        <Select v-model="formValidate.temp_id">
                                            <Option v-for="item in templateList" :value="item.id" :key="item.id">{{ item.name }}</Option>
                                        </Select>
                                    </div>
                                </FormItem>
                            </Col>
                            <!--<Col v-bind="grid2">-->
                                <!--<FormItem label="邮费：">-->
                                    <!--<InputNumber placeholder="请输入邮费" element-id="postage" v-model="formValidate.postage"/>-->
                                <!--</FormItem>-->
                            <!--</Col>-->
                            <!--<Col v-bind="grid2">-->
                                <!--<FormItem label="是否包邮：" props="is_postage" label-for="is_postage">-->
                                    <!--<RadioGroup element-id="is_postage" v-model="formValidate.is_postage">-->
                                        <!--<Radio :label="1" class="radio">是</Radio>-->
                                        <!--<Radio :label="0">否</Radio>-->
                                    <!--</RadioGroup>-->
                                <!--</FormItem>-->
                            <!--</Col>-->
<!--                            <Col v-bind="grid2">-->
<!--                                <FormItem label="热门推荐：" props="is_hot" label-for="is_hot">-->
<!--                                    <RadioGroup element-id="is_hot" v-model="formValidate.is_host">-->
<!--                                        <Radio :label="1" class="radio">开启</Radio>-->
<!--                                        <Radio :label="0">关闭</Radio>-->
<!--                                    </RadioGroup>-->
<!--                                </FormItem>-->
<!--                            </Col>-->
                            <Col v-bind="grid2">
                                <FormItem label="活动状态：" props="is_show" label-for="is_show">
                                    <RadioGroup element-id="status"  v-model="formValidate.is_show">
                                        <Radio :label="1" class="radio">开启</Radio>
                                        <Radio :label="0">关闭</Radio>
                                    </RadioGroup>
                                </FormItem>
                            </Col>
                            <Col span="24">
                                <FormItem label="规格选择：">
                                    <Table :data="specsData" :columns="columns" border @on-selection-change="changeCheckbox">
                                        <template slot-scope="{ row, index }" slot="pic">
                                            <div class="acea-row row-middle row-center-wrapper" @click="modalPicTap('dan','danTable',index)">
                                                <div class="pictrue pictrueTab" v-if="row.pic"><img v-lazy="row.pic"></div>
                                                <div class="upLoad pictrueTab acea-row row-center-wrapper"   v-else>
                                                    <Icon type="ios-camera-outline" size="21"/>
                                                </div>
                                            </div>
                                        </template>
                                    </Table>
                                </FormItem>
                            </Col>
                        </Row>
                        <Row v-show="current === 2">
                            <Col span="24">
                                <FormItem label="内容：">
                                    <vue-ueditor-wrap v-model="formValidate.description"  @beforeInit="addCustomDialog"  :config="myConfig"  style="width: 90%;"></vue-ueditor-wrap>
                                </FormItem>
                            </Col>
                        </Row>
                        <FormItem>
                            <Button class="submission mr15" @click="step" v-show="current!==0" :disabled="$route.params.id&&current===1">上一步</Button>
                            <Button type="primary" :disabled="submitOpen && current === 2" class="submission" @click="next('formValidate')" v-text="current===2?'提交':'下一步'"></Button>
                        </FormItem>
                    </Form>
                    <Spin size="large" fix v-if="spinShow"></Spin>
                </Col>
            </Row>
        </Card>
        <!-- 选择商品-->
        <Modal v-model="modals" title="商品列表" footerHide  class="paymentFooter" scrollable width="900" @on-cancel="cancel">
            <goods-list ref="goodslist" v-if="modals"  @getProductId="getProductId"></goods-list>
        </Modal>
        <!-- 上传图片-->
        <Modal v-model="modalPic" width="60%" scrollable  footer-hide closable title='上传商品图' :mask-closable="false" :z-index="1">
            <uploadPictures :isChoice="isChoice" @getPic="getPic"  @getPicD="getPicD" :gridBtn="gridBtn" :gridPic="gridPic" v-if="modalPic"></uploadPictures>
        </Modal>
    </div>
</template>

<script>
    import { mapState } from 'vuex'
    import goodsList from '@/components/goodsList/index'
    import UeditorWrap from '@/components/ueditorFrom/index'
    import VueUeditorWrap from 'vue-ueditor-wrap'
    import uploadPictures from '@/components/uploadPictures'
    import { combinationInfoApi, combinationCreatApi, productAttrsApi } from '@/api/marketing'
    import { productGetTemplateApi } from '@/api/product'
    export default {
        name: 'storeCombinationCreate',
        components: { UeditorWrap, goodsList, uploadPictures, VueUeditorWrap },
        data () {
            return {
                submitOpen: false,
                spinShow: false,
                isChoice: '',
                current: 0,
                modalPic: false,
                grid: {
                    xl: 12,
                    lg: 20,
                    md: 24,
                    sm: 24,
                    xs: 24
                },
                grid2: {
                    xl: 8,
                    lg: 8,
                    md: 12,
                    sm: 24,
                    xs: 24
                },
                gridPic: {
                    xl: 6,
                    lg: 8,
                    md: 12,
                    sm: 12,
                    xs: 12
                },
                gridBtn: {
                    xl: 4,
                    lg: 8,
                    md: 8,
                    sm: 8,
                    xs: 8
                },
                myConfig: {
                    autoHeightEnabled: false, // 编辑器不自动被内容撑高
                    initialFrameHeight: 500, // 初始容器高度
                    initialFrameWidth: '100%', // 初始容器宽度
                    UEDITOR_HOME_URL: '/admin/UEditor/',
                    serverUrl: ''
                },
                modals: false,
                modal_loading: false,
                images: [],
                templateList: [],
                columns: [],
                specsData: [],
                picTit: '',
                tableIndex: 0,
                formValidate: {
                    images: [],
                    info: '',
                    title: '',
                    image: '',
                    unit_name: '',
                    price: 0,
                    effective_time: 24,
                    stock: 1,
                    sales: 0,
                    sort: 0,
                    postage: 0,
                    is_postage: 0,
                    is_host: 0,
                    is_show: 0,
                    section_time: [],
                    description: '',
                    id: 0,
                    product_id: 0,
                    people: 2,
                    temp_id: '',
                    attrs: [],
                    items: []
                },
                ruleValidate: {
                    image: [
                        { required: true, message: '请选择主图', trigger: 'change' }
                    ],
                    images: [
                        { required: true, type: 'array', message: '请选择主图', trigger: 'change' },
                        { type: 'array', min: 1, message: 'Choose two hobbies at best', trigger: 'change' }
                    ],
                    title: [
                        { required: true, message: '请输入拼团名称', trigger: 'blur' }
                    ],
                    info: [
                        { required: true, message: '请输入拼团简介', trigger: 'blur' }
                    ],
                    section_time: [
                        { required: true, type: 'array', message: '请选择活动时间', trigger: 'change' }
                    ],
                    unit_name: [
                        { required: true, message: '请输入单位', trigger: 'blur' }
                    ],
                    price: [
                        { required: true, type: 'number', message: '请输入拼团价', trigger: 'blur' }
                    ],
                    cost: [
                        { required: true, type: 'number', message: '请输入成本价', trigger: 'blur' }
                    ],
                    stock: [
                        { required: true, type: 'number', message: '请输入库存', trigger: 'blur' }
                    ],
                    give_integral: [
                        { required: true, type: 'number', message: '请输入赠送积分', trigger: 'blur' }
                    ],
                    effective_time: [
                        { required: true, type: 'number', message: '请输入拼团时效(单位 小时)', trigger: 'blur' }
                    ],
                    people: [
                        { required: true, type: 'number', message: '请输入拼团人数', trigger: 'blur' }
                    ],
                    temp_id: [
                        { required: true, message: '请选择运费模板', trigger: 'change', type: 'number' }
                    ]
                }
            }
        },
        computed: {
            ...mapState('media', [
                'isMobile'
            ]),
            labelWidth () {
                return this.isMobile ? undefined : 155
            },
            labelPosition () {
                return this.isMobile ? 'top' : 'right'
            }
        },
        mounted () {
            if (this.$route.params.id) {
                this.current = 1
                this.getInfo()
            }
            this.productGetTemplate()
        },
        methods: {
            // 拼团规格；
            productAttrs (row) {
                let that = this
                productAttrsApi(row.id, 3).then(res => {
                    let data = res.data.info
                    let selection = {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    }
                    that.specsData = data.attrs
                    that.specsData.forEach(function (item, index) {
                        that.$set(that.specsData[index], 'id', index)
                    })
                    that.formValidate.items = data.items
                    that.columns = data.header
                    that.columns.unshift(selection)
                    that.inputChange(data)
                }).catch(res => {
                    that.$Message.error(res.msg)
                })
            },
            inputChange (data) {
                let that = this
                let $index = []
                data.header.forEach(function (item, index) {
                    if (item.type === 1) {
                        $index.push({ index: index, key: item.key, title: item.title })
                    }
                })
                $index.forEach(function (item, index) {
                    let title = item.title
                    let key = item.key
                    let row = {
                        title: title,
                        key: key,
                        align: 'center',
                        minWidth: 100,
                        render: (h, params) => {
                            return h('div', [
                                h('InputNumber', {
                                    props: {
                                        min: 0,
                                        value: key === 'price' ? params.row.price : params.row.quota
                                    },
                                    on: {
                                        'on-change': e => {
                                            key === 'price' ? params.row.price = e : params.row.quota = e
                                            that.specsData[params.index] = params.row
                                            if (!!that.formValidate.attrs && that.formValidate.attrs.length) {
                                                that.formValidate.attrs.forEach((v, index) => {
                                                    if (v.id === params.row.id) {
                                                        that.formValidate.attrs.splice(index, 1, params.row)
                                                    }
                                                })
                                            }
                                        }
                                    }
                                })
                            ])
                        }
                    }
                    that.columns.splice(item.index, 1, row)
                })
            },
            // 多选
            changeCheckbox (selection) {
                this.formValidate.attrs = selection
            },
            // 获取运费模板；
            productGetTemplate () {
                productGetTemplateApi().then(res => {
                    this.templateList = res.data
                })
            },
            // 表单验证
            validate (prop, status, error) {
                if (status === false) {
                    this.$Message.error(error)
                }
            },
            // 商品id
            getProductId (row) {
                this.modal_loading = false
                this.modals = false
                setTimeout(() => {
                    this.formValidate = {
                        images: row.slider_image,
                        info: row.store_info,
                        title: row.store_name,
                        image: row.image,
                        unit_name: row.unit_name,
                        price: 0, // 不取商品中的原价
                        effective_time: 24,
                        stock: row.stock,
                        sales: row.sales,
                        sort: row.sort,
                        postage: row.postage,
                        is_postage: row.is_postage,
                        is_host: row.is_hot,
                        is_show: 0,
                        section_time: [],
                        description: row.description, // 不取商品中的
                        id: 0,
                        people: 2,
                        product_id: row.id,
                        temp_id: row.temp_id
                    }
                    this.productAttrs(row)
                }, 500)
            },
            cancel () {
                this.modals = false
            },
            // 具体日期
            onchangeTime (e) {
                this.formValidate.section_time = e
            },
            // 详情
            getInfo () {
                this.spinShow = true
                combinationInfoApi(this.$route.params.id).then(async res => {
                    let that = this
                    let info = res.data.info
                    let selection = {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    }
                    this.formValidate = info
                    this.$set(this.formValidate, 'items', info.attrs.items)
                    this.columns = info.attrs.header
                    this.columns.unshift(selection)
                    this.specsData = info.attrs.value
                    that.specsData.forEach(function (item, index) {
                        that.$set(that.specsData[index], 'id', index)
                    })
                    let data = info.attrs
                    let attr = []
                    for (let index in info.attrs.value) {
                        if (info.attrs.value[index]._checked) {
                            attr.push(info.attrs.value[index])
                        }
                    }
                    that.formValidate.attrs = attr
                    that.inputChange(data)
                    this.spinShow = false
                }).catch(res => {
                    this.spinShow = false
                    this.$Message.error(res.msg)
                })
            },
            // 下一步
            next (name) {
                let that = this
                if (this.current === 2) {
                    this.$refs[name].validate((valid) => {
                        if (!that.formValidate.section_time[0]) {
                            return that.$Message.error('请选择活动时间')
                        }
                        if(!that.formValidate.unit_name){
                            return that.$Message.error('请输入单位')
                        }
                        if (valid) {
                            if (that.formValidate.people < 2) {
                                return that.$Message.error('拼团人数必须大于2')
                            }
                            if (!that.formValidate.attrs) {
                                return that.$Message.error('请选择属性规格')
                            } else {
                                for (let index in that.formValidate.attrs) {
                                    if (that.formValidate.attrs[index].quota <= 0) {
                                        return that.$Message.error('拼团限量必须大于0')
                                    }
                                }
                            }
                            this.formValidate.id = Number(this.$route.params.id) || 0
                            this.submitOpen = true;
                            combinationCreatApi(this.formValidate).then(async res => {
                                this.submitOpen = false;
                                this.$Message.success(res.msg)
                                setTimeout(() => {
                                    this.$router.push({ path: '/admin/marketing/store_combination/index' })
                                }, 500)
                            }).catch(res => {
                                this.submitOpen = false;
                                this.$Message.error(res.msg)
                            })
                        } else {
                            return false
                        }
                    })
                } else {
                    if (this.formValidate.image) {
                        this.current += 1
                    } else {
                        this.$Message.warning('请选择商品')
                    }
                }
            },
            // 上一步
            step () {
                this.current--
            },
            // 内容
            getContent (val) {
                this.formValidate.description = val
            },
            // 点击商品图
            modalPicTap (tit, picTit, index) {
                this.modalPic = true
                this.isChoice = tit === 'dan' ? '单选' : '多选'
                this.picTit = picTit
                this.tableIndex = index
            },
            // 获取单张图片信息
            getPic (pc) {
                switch (this.picTit) {
                case 'danFrom':
                    this.formValidate.image = pc.att_dir
                    break
                default:
                    if (!!this.formValidate.attrs && this.formValidate.attrs.length) {
                        this.$set(this.specsData[this.tableIndex], '_checked', true)
                    }
                    this.specsData[this.tableIndex].pic = pc.att_dir
                }
                this.modalPic = false
            },
            // 获取多张图信息
            getPicD (pc) {
                this.images = pc
                this.images.map((item) => {
                    this.formValidate.images.push(item.att_dir)
                })
                this.modalPic = false
            },
            handleRemove (i) {
                this.images.splice(i, 1)
                this.formValidate.images.splice(i, 1)
            },
            // 选择商品
            changeGoods () {
                this.modals = true
            },
            // 移动
            handleDragStart (e, item) {
                this.dragging = item
            },
            handleDragEnd (e, item) {
                this.dragging = null
            },
            // 首先把div变成可以放置的元素，即重写dragenter/dragover
            handleDragOver (e) {
                e.dataTransfer.dropEffect = 'move'
            },
            handleDragEnter (e, item) {
                e.dataTransfer.effectAllowed = 'move'
                if (item === this.dragging) {
                    return
                }
                const newItems = [...this.formValidate.images]
                const src = newItems.indexOf(this.dragging)
                const dst = newItems.indexOf(item)
                newItems.splice(dst, 0, ...newItems.splice(src, 1))
                this.formValidate.images = newItems
            },
            // 添加自定义弹窗
            addCustomDialog (editorId) {
                window.UE.registerUI('test-dialog', function (editor, uiName) {
                    // 创建 dialog
                    let dialog = new window.UE.ui.Dialog({
                        // 指定弹出层中页面的路径，这里只能支持页面，路径参考常见问题 2
                        iframeUrl: '/admin/widget.images/index.html?fodder=dialog',
                        // 需要指定当前的编辑器实例
                        editor: editor,
                        // 指定 dialog 的名字
                        name: uiName,
                        // dialog 的标题
                        title: '上传图片',
                        // 指定 dialog 的外围样式
                        cssRules: 'width:1200px;height:500px;padding:20px;'
                    })
                    this.dialog = dialog
                    // 参考上面的自定义按钮
                    var btn = new window.UE.ui.Button({
                        name: 'dialog-button',
                        title: '上传图片',
                        cssRules: `background-image: url(../../../assets/images/icons.png);background-position: -726px -77px;`,
                        onclick: function () {
                            // 渲染dialog
                            dialog.render()
                            dialog.open()
                        }
                    })
                    return btn
                }, 37)
            }
        }
    }
</script>

<style scoped lang="stylus">
    .ivu-table-wrapper
      border-left: 1px solid #dcdee2;
      border-top: 1px solid #dcdee2;
    .tabBox_img
        width 50px;
        height 50px;
    .tabBox_img img
        width 100%;
        height 100%;
    .priceBox
        width 100%
    .form
        .picBox
            display: inline-block;
            cursor: pointer;
        .pictrue
            width:60px;
            height:60px;
            border:1px dotted rgba(0,0,0,0.1);
            margin-right:15px;
            display: inline-block;
            position: relative;
            cursor: pointer;
            img
                width 100%
                height 100%
            .btndel
                position: absolute;
                z-index: 9;
                width :20px !important;
                height: 20px !important;
                left: 46px;
                top: -4px;
        .upLoad {
            width: 58px;
            height: 58px;
            line-height: 58px;
            border: 1px dotted rgba(0, 0, 0, 0.1);
            border-radius: 4px;
            background: rgba(0, 0, 0, 0.02);
            cursor: pointer;
        }

</style>
