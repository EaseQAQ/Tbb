<template>
    <div class="sonRoute">
        <div class="headline">
            <h2>已上架的商品</h2>
        </div>
        <!--导航功能-->
        <div class="filtrate">
            <el-row>
                <el-col :span="12">
                    <el-autocomplete
                    class="inline-input"
                    v-model="state1"
                    :fetch-suggestions="querySearch"
                    placeholder="请输入商品名"
                    @select="handleSelect"
                    ></el-autocomplete>
                </el-col>
                <el-button type="primary">搜索</el-button>
            </el-row>
        </div>
        <div class="information">
            <div id="shopphear">
                <el-button type="primary" round>发布商品</el-button>
                <el-button type="primary" round>批量删除</el-button>
            </div>
            <div id="shoppnum">共<em v-if="sum">{{sum[0].osum}}</em>件商品</div>
        </div>
        <!--表格-->
        <div class="tab">
            <el-table :data="userlist" border style="width: 100%">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column fixed prop="goods_id" label="商品ID" width="80"></el-table-column>
                <el-table-column prop="goods_name" label="商品名称" width="400"></el-table-column>
                <el-table-column prop="goods_price" label="价格" width="100"></el-table-column>
                <el-table-column prop="SUM(goods_stock)" label="库存" width="100"></el-table-column>
                <el-table-column prop="goods_gender" label="分类" width="160"></el-table-column>
                <el-table-column fixed="right" label="操作" width="100">
                    <template #default="scope">
                        <el-button @click="del(scope.row.goods_id)" type="text" size="small">下架</el-button>
                        <el-button type="text" size="small" @click="centerDialogVisible = true">编辑</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <el-pagination background layout="prev, pager, next" v-if="sum" :total="sum[0].osum" :page-size="size" @current-change="changePage"></el-pagination>
            <el-dialog title="提示"  :visible.sync="centerDialogVisible" width="30%" center>
                <el-form ref="form"  label-width="80px">
                    <el-form-item label="价格">
                        <el-input></el-input>
                    </el-form-item>
                    <el-form-item label="数量">
                        <el-input type='password'></el-input>
                    </el-form-item>
                    <el-form-item label="s">
                        <el-input ></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary">保存</el-button>
                    </el-form-item>
                </el-form>
                <span slot="footer" class="dialog-footer">
                    <el-button @click="centerDialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="centerDialogVisible = false">确 定</el-button>
                </span>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    export default {
        inject:['reload'],
        name: "commodity",
        data() {
            return {
                userlist:[],
                sum:'',
                size: 50,
                currentPage: 1,
                restaurants: [],
                state1: '',
                state2: '',
                centerDialogVisible: false,
            }
        },
        methods: {
            del(row){
                let id = row
                console.log(id)
                this.axios.get(`http://localhost:8080/merchant//seller/sellerCenter/delcommodity?sel_id=${id}`).
                then(res=>{
                    console.log("成功");
                    this.reload();
                })
                .catch(err=>{
                    console.log("操作失败" + err);
                })
            },
            changePage(currentPage) {
                const sid = parseInt(sessionStorage.getItem('sellerid'));
                console.log(sid);
                let size=parseInt(this.size)
                console.log(size);
                let current=parseInt((currentPage-1)*this.size)
                console.log(current);
                this.axios.get(`http://localhost:8080/merchant/seller/sellerCenter/paging?sel_id=${sid}&size=${size}&current=${current}`).then((response)=> {
                    this.userlist =response.data
                    console.log(this.userlist);
                }).catch(err=>{
                    console.log("获取数据失败" + err);
                })
            },
            // 
            querySearch(queryString, cb) {
                var restaurants = this.restaurants;
                var results = queryString ? restaurants.filter(this.createFilter(queryString)) : restaurants;
                // 调用 callback 返回建议列表的数据
                cb(results);
            },
            createFilter(queryString) {
                return (restaurant) => {
                return (restaurant.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0);
                };
            },
            loadAll() {
                return [
                { "value": "三全鲜食（北新泾店）", "address": "长宁区新渔路144号" },
                { "value": "Hot honey 首尔炸鸡（仙霞路）", "address": "上海市长宁区淞虹路661号" },
                { "value": "新旺角茶餐厅", "address": "上海市普陀区真北路988号创邑金沙谷6号楼113" },
                { "value": "泷千家(天山西路店)", "address": "天山西路438号" },
                { "value": "胖仙女纸杯蛋糕（上海凌空店）", "address": "上海市长宁区金钟路968号1幢18号楼一层商铺18-101" },
                { "value": "贡茶", "address": "上海市长宁区金钟路633号" },
                { "value": "豪大大香鸡排超级奶爸", "address": "上海市嘉定区曹安公路曹安路1685号" },
                { "value": "茶芝兰（奶茶，手抓饼）", "address": "上海市普陀区同普路1435号" },
                { "value": "十二泷町", "address": "上海市北翟路1444弄81号B幢-107" },
                { "value": "星移浓缩咖啡", "address": "上海市嘉定区新郁路817号" },
                { "value": "阿姨奶茶/豪大大", "address": "嘉定区曹安路1611号" },
                { "value": "新麦甜四季甜品炸鸡", "address": "嘉定区曹安公路2383弄55号" },
                { "value": "Monica摩托主题咖啡店", "address": "嘉定区江桥镇曹安公路2409号1F，2383弄62号1F" },
                { "value": "浮生若茶（凌空soho店）", "address": "上海长宁区金钟路968号9号楼地下一层" },
                { "value": "NONO JUICE  鲜榨果汁", "address": "上海市长宁区天山西路119号" },
                { "value": "CoCo都可(北新泾店）", "address": "上海市长宁区仙霞西路" },
                { "value": "快乐柠檬（神州智慧店）", "address": "上海市长宁区天山西路567号1层R117号店铺" },
                { "value": "Merci Paul cafe", "address": "上海市普陀区光复西路丹巴路28弄6号楼819" },
                { "value": "猫山王（西郊百联店）", "address": "上海市长宁区仙霞西路88号第一层G05-F01-1-306" },
                { "value": "枪会山", "address": "上海市普陀区棕榈路" },
                { "value": "纵食", "address": "元丰天山花园(东门) 双流路267号" },
                { "value": "钱记", "address": "上海市长宁区天山西路" },
                { "value": "壹杯加", "address": "上海市长宁区通协路" },
                { "value": "唦哇嘀咖", "address": "上海市长宁区新泾镇金钟路999号2幢（B幢）第01层第1-02A单元" },
                { "value": "爱茜茜里(西郊百联)", "address": "长宁区仙霞西路88号1305室" },
                { "value": "爱茜茜里(近铁广场)", "address": "上海市普陀区真北路818号近铁城市广场北区地下二楼N-B2-O2-C商铺" },
                { "value": "鲜果榨汁（金沙江路和美广店）", "address": "普陀区金沙江路2239号金沙和美广场B1-10-6" },
                { "value": "开心丽果（缤谷店）", "address": "上海市长宁区威宁路天山路341号" },
                { "value": "超级鸡车（丰庄路店）", "address": "上海市嘉定区丰庄路240号" },
                { "value": "妙生活果园（北新泾店）", "address": "长宁区新渔路144号" },
                { "value": "香宜度麻辣香锅", "address": "长宁区淞虹路148号" },
                { "value": "凡仔汉堡（老真北路店）", "address": "上海市普陀区老真北路160号" },
                { "value": "港式小铺", "address": "上海市长宁区金钟路968号15楼15-105室" },
                { "value": "蜀香源麻辣香锅（剑河路店）", "address": "剑河路443-1" },
                { "value": "北京饺子馆", "address": "长宁区北新泾街道天山西路490-1号" },
                { "value": "饭典*新简餐（凌空SOHO店）", "address": "上海市长宁区金钟路968号9号楼地下一层9-83室" },
                { "value": "焦耳·川式快餐（金钟路店）", "address": "上海市金钟路633号地下一层甲部" },
                { "value": "动力鸡车", "address": "长宁区仙霞西路299弄3号101B" },
                { "value": "浏阳蒸菜", "address": "天山西路430号" },
                { "value": "四海游龙（天山西路店）", "address": "上海市长宁区天山西路" },
                { "value": "樱花食堂（凌空店）", "address": "上海市长宁区金钟路968号15楼15-105室" },
                { "value": "壹分米客家传统调制米粉(天山店)", "address": "天山西路428号" },
                { "value": "福荣祥烧腊（平溪路店）", "address": "上海市长宁区协和路福泉路255弄57-73号" },
                { "value": "速记黄焖鸡米饭", "address": "上海市长宁区北新泾街道金钟路180号1层01号摊位" },
                { "value": "红辣椒麻辣烫", "address": "上海市长宁区天山西路492号" },
                { "value": "(小杨生煎)西郊百联餐厅", "address": "长宁区仙霞西路88号百联2楼" },
                { "value": "阳阳麻辣烫", "address": "天山西路389号" },
                { "value": "南拳妈妈龙虾盖浇饭", "address": "普陀区金沙江路1699号鑫乐惠美食广场A13" }
                ];
            },
            handleSelect(item) {
                // console.log(item);
            }
        },
        mounted() {
            this.restaurants = this.loadAll();
            const id = parseInt(sessionStorage.getItem('sellerid'));
            console.log(id);
            this.axios.get(`http://localhost:8080/merchant/seller/sellerCenter/commodity?sel_id=${id}`).then((response) => {
                this.userlist =response.data
                console.log(this.userlist);
            }).catch(err=>{
                console.log("获取数据失败" + err);
            }),
            this.axios.get(`http://localhost:8080/merchant/seller/sellerCenter/commoditysum?sel_id=${id}/1/50`).then((response) => {
                this.sum =response.data
                console.log(this.sum[0]);
            }).catch(err=>{
                console.log("获取数据失败" + err);
            })
        },
    }
</script>

<style scoped>
    
</style>