

<template>
    <div class="main-content">
        <!-- 条件查询 -->
        <div v-if="showFlag">
            <el-form :inline="true" :model="searchForm" class="form-content">
                <el-row :gutter="20" class="slt" :style="{justifyContent:contents.searchBoxPosition=='1'?'flex-start':contents.searchBoxPosition=='2'?'center':'flex-end'}">


                    <el-form-item :label="contents.inputTitle == 1 ? '试题名称' : ''">
                        <el-input prefix-icon="el-icon-search" v-model="searchForm.examquestionName" placeholder="试题名称" clearable></el-input>
                    </el-form-item>




                    <el-form-item :label="contents.inputTitle == 1 ? '试卷名称' : ''">
                        <el-input prefix-icon="el-icon-search" v-model="searchForm.exampaperName" placeholder="试卷名称" clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="success" @click="search()">{{ contents.searchBtnFont == 1?'查询':'' }}<i class="el-icon-search el-icon--right"/></el-button>
                    </el-form-item>
                </el-row>
                <el-row class="ad" :style="{justifyContent:contents.btnAdAllBoxPosition=='1'?'flex-start':contents.btnAdAllBoxPosition=='2'?'center':'flex-end'}">
                    <el-form-item>
                        <el-button
                                v-if="isAuth('examquestion','新增')"
                                type="success"
                                icon="el-icon-plus"
                                @click="addOrUpdateHandler()"
                        >{{ contents.btnAdAllFont == 1?'新增':'' }}</el-button>
                        <el-button
                                v-if="isAuth('examquestion','删除')"
                                :disabled="dataListSelections.length <= 0"
                                type="danger"
                                icon="el-icon-delete"
                                @click="deleteHandler()"
                        >{{ contents.btnAdAllFont == 1?'删除':'' }}</el-button>
                    </el-form-item>
                </el-row>
            </el-form>
            <div class="table-content">
                <el-table class="tables" :size="contents.tableSize" :show-header="contents.tableShowHeader"
                          :header-row-style="headerRowStyle" :header-cell-style="headerCellStyle"
                          :border="contents.tableBorder"
                          :fit="contents.tableFit"
                          :stripe="contents.tableStripe"
                          :row-style="rowStyle"
                          :cell-style="cellStyle"
                          :style="{width: '100%',fontSize:contents.tableContentFontSize,color:contents.tableContentFontColor}"
                          v-if="isAuth('examquestion','查看')"
                          :data="dataList"
                          v-loading="dataListLoading"
                          @selection-change="selectionChangeHandler">
                    <el-table-column  v-if="contents.tableSelection"
                                      type="selection"
                                      header-align="center"
                                      align="center"
                                      width="50">
                    </el-table-column>
                    <el-table-column label="索引" v-if="contents.tableIndex" type="index" width="50" />



                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="exampaperName"
                                      header-align="center"
                                      label="试卷名称">
                        <template slot-scope="scope">
                            {{scope.row.exampaperName}}
                        </template>
                    </el-table-column>

                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionName"
                                      header-align="center"
                                      label="试题名称">
                        <template slot-scope="scope">
                            <div v-if="scope.row.examquestionName.length>10">
                                {{scope.row.examquestionName.substring(0,10)}}...
                            </div>
                            <div v-else>
                                {{scope.row.examquestionName}}
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionScore"
                                      header-align="center"
                                      label="分值">
                        <template slot-scope="scope">
                            {{scope.row.examquestionScore}}
                        </template>
                    </el-table-column>

                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionAnswer"
                                      header-align="center"
                                      label="正确答案">
                        <template slot-scope="scope">
                            {{scope.row.examquestionAnswer}}
                        </template>
                    </el-table-column>

                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionAnalysis"
                                      header-align="center"
                                      label="答案解析">
                        <template slot-scope="scope">
                            <div v-if="scope.row.examquestionAnalysis.length>10">
                                {{scope.row.examquestionAnalysis.substring(0,10)}}...
                            </div>
                            <div v-else>
                                {{scope.row.examquestionAnalysis}}
                            </div>
                        </template>
                    </el-table-column>

                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionTypes"
                                      header-align="center"
                                      label="试题类型">
                        <template slot-scope="scope">
                            <el-tag v-if="scope.row.examquestionTypes==1">{{scope.row.examquestionValue}}</el-tag>
                            <el-tag v-if="scope.row.examquestionTypes==2" type="info">{{scope.row.examquestionValue}}</el-tag>
                            <el-tag v-if="scope.row.examquestionTypes==3" type="success">{{scope.row.examquestionValue}}</el-tag>
                            <el-tag v-if="scope.row.examquestionTypes==4" type="warning">{{scope.row.examquestionValue}}</el-tag>
                        </template>
                    </el-table-column>
                    <el-table-column  :sortable="contents.tableSortable" :align="contents.tableAlign"
                                      prop="examquestionSequence"
                                      header-align="center"
                                      label="试题排序">
                        <template slot-scope="scope">
                            {{scope.row.examquestionSequence}}
                        </template>
                    </el-table-column>

                    <el-table-column width="300" :align="contents.tableAlign"
                                     header-align="center"
                                     label="操作">
                        <template slot-scope="scope">
                            <el-button v-if="isAuth('examquestion','查看')" type="success" icon="el-icon-tickets" size="mini" @click="addOrUpdateHandler(scope.row.id,'info')">{{ contents.tableBtnFont == 1?'详情':'' }}</el-button>

                            <el-button v-if="isAuth('examquestion','修改')" type="primary" icon="el-icon-edit" size="mini" @click="addOrUpdateHandler(scope.row.id)">{{ contents.tableBtnFont == 1?'修改':'' }}</el-button>

                            <el-button v-if="isAuth('examquestion','删除')" type="danger" icon="el-icon-delete" size="mini" @click="deleteHandler(scope.row.id)">{{ contents.tableBtnFont == 1?'删除':'' }}</el-button>

                        </template>
                    </el-table-column>
                </el-table>
                <el-pagination
                        clsss="pages"
                        :layout="layouts"
                        @size-change="sizeChangeHandle"
                        @current-change="currentChangeHandle"
                        :current-page="pageIndex"
                        :page-sizes="[10, 20, 50, 100]"
                        :page-size="Number(contents.pageEachNum)"
                        :total="totalPage"
                        :small="contents.pageStyle"
                        class="pagination-content"
                        :background="contents.pageBtnBG"
                        :style="{textAlign:contents.pagePosition==1?'left':contents.pagePosition==2?'center':'right'}"
                ></el-pagination>
            </div>
        </div>
        <!-- 添加/修改页面  将父组件的search方法传递给子组件-->
        <add-or-update v-if="addOrUpdateFlag" :parent="this" ref="addOrUpdate"></add-or-update>



    </div>
</template>
<script>
    import AddOrUpdate from "./add-or-update";
    import styleJs from "../../../utils/style.js";
    export default {
        data() {
            return {
                searchForm: {
                    key: ""
                },
                exampaperTypesSelectSearch : [],
                form:{},
                dataList: [],
                pageIndex: 1,
                pageSize: 10,
                totalPage: 0,
                dataListLoading: false,
                dataListSelections: [],
                showFlag: true,
                sfshVisiable: false,
                shForm: {},
                chartVisiable: false,
                addOrUpdateFlag:false,
                contents:null,
                layouts: '',
            };
        },
        created() {
            this.contents = styleJs.listStyle();
            this.init();
            this.getDataList();
            this.contentStyleChange()
        },
        mounted() {

        },
        filters: {
            htmlfilter: function (val) {
                return val.replace(/<[^>]*>/g).replace(/undefined/g,'');
            },
        },
        components: {
            AddOrUpdate,
        },
        methods: {
            contentStyleChange() {
                this.contentSearchStyleChange()
                this.contentBtnAdAllStyleChange()
                this.contentSearchBtnStyleChange()
                this.contentTableBtnStyleChange()
                this.contentPageStyleChange()
            },
            contentSearchStyleChange() {
                this.$nextTick(()=>{
                    document.querySelectorAll('.form-content .slt .el-input__inner').forEach(el=>{
                        let textAlign = 'left'
                        if(this.contents.inputFontPosition == 2) textAlign = 'center'
                        if(this.contents.inputFontPosition == 3) textAlign = 'right'
                        el.style.textAlign = textAlign
                        el.style.height = this.contents.inputHeight
                        el.style.lineHeight = this.contents.inputHeight
                        el.style.color = this.contents.inputFontColor
                        el.style.fontSize = this.contents.inputFontSize
                        el.style.borderWidth = this.contents.inputBorderWidth
                        el.style.borderStyle = this.contents.inputBorderStyle
                        el.style.borderColor = this.contents.inputBorderColor
                        el.style.borderRadius = this.contents.inputBorderRadius
                        el.style.backgroundColor = this.contents.inputBgColor
                    })
                    if(this.contents.inputTitle) {
                        document.querySelectorAll('.form-content .slt .el-form-item__label').forEach(el=>{
                            el.style.color = this.contents.inputTitleColor
                            el.style.fontSize = this.contents.inputTitleSize
                            el.style.lineHeight = this.contents.inputHeight
                        })
                    }
                    setTimeout(()=>{
                        document.querySelectorAll('.form-content .slt .el-input__prefix').forEach(el=>{
                            el.style.color = this.contents.inputIconColor
                            el.style.lineHeight = this.contents.inputHeight
                        })
                        document.querySelectorAll('.form-content .slt .el-input__suffix').forEach(el=>{
                            el.style.color = this.contents.inputIconColor
                            el.style.lineHeight = this.contents.inputHeight
                        })
                        document.querySelectorAll('.form-content .slt .el-input__icon').forEach(el=>{
                            el.style.lineHeight = this.contents.inputHeight
                        })
                    },10)

                })
            },
            // 搜索按钮
            contentSearchBtnStyleChange() {
                this.$nextTick(()=>{
                    document.querySelectorAll('.form-content .slt .el-button--success').forEach(el=>{
                        el.style.height = this.contents.searchBtnHeight
                        el.style.color = this.contents.searchBtnFontColor
                        el.style.fontSize = this.contents.searchBtnFontSize
                        el.style.borderWidth = this.contents.searchBtnBorderWidth
                        el.style.borderStyle = this.contents.searchBtnBorderStyle
                        el.style.borderColor = this.contents.searchBtnBorderColor
                        el.style.borderRadius = this.contents.searchBtnBorderRadius
                        el.style.backgroundColor = this.contents.searchBtnBgColor
                    })
                })
            },
            // 新增、批量删除
            contentBtnAdAllStyleChange() {
                this.$nextTick(()=>{
                    document.querySelectorAll('.form-content .ad .el-button--success').forEach(el=>{
                        el.style.height = this.contents.btnAdAllHeight
                        el.style.color = this.contents.btnAdAllAddFontColor
                        el.style.fontSize = this.contents.btnAdAllFontSize
                        el.style.borderWidth = this.contents.btnAdAllBorderWidth
                        el.style.borderStyle = this.contents.btnAdAllBorderStyle
                        el.style.borderColor = this.contents.btnAdAllBorderColor
                        el.style.borderRadius = this.contents.btnAdAllBorderRadius
                        el.style.backgroundColor = this.contents.btnAdAllAddBgColor
                    })
                    document.querySelectorAll('.form-content .ad .el-button--danger').forEach(el=>{
                        el.style.height = this.contents.btnAdAllHeight
                        el.style.color = this.contents.btnAdAllDelFontColor
                        el.style.fontSize = this.contents.btnAdAllFontSize
                        el.style.borderWidth = this.contents.btnAdAllBorderWidth
                        el.style.borderStyle = this.contents.btnAdAllBorderStyle
                        el.style.borderColor = this.contents.btnAdAllBorderColor
                        el.style.borderRadius = this.contents.btnAdAllBorderRadius
                        el.style.backgroundColor = this.contents.btnAdAllDelBgColor
                    })
                    document.querySelectorAll('.form-content .ad .el-button--warning').forEach(el=>{
                        el.style.height = this.contents.btnAdAllHeight
                        el.style.color = this.contents.btnAdAllWarnFontColor
                        el.style.fontSize = this.contents.btnAdAllFontSize
                        el.style.borderWidth = this.contents.btnAdAllBorderWidth
                        el.style.borderStyle = this.contents.btnAdAllBorderStyle
                        el.style.borderColor = this.contents.btnAdAllBorderColor
                        el.style.borderRadius = this.contents.btnAdAllBorderRadius
                        el.style.backgroundColor = this.contents.btnAdAllWarnBgColor
                    })
                })
            },
            // 表格
            rowStyle({ row, rowIndex}) {
                if (rowIndex % 2 == 1) {
                    if(this.contents.tableStripe) {
                        return {color:this.contents.tableStripeFontColor}
                    }
                } else {
                    return ''
                }
            },
            cellStyle({ row, rowIndex}){
                if (rowIndex % 2 == 1) {
                    if(this.contents.tableStripe) {
                        return {backgroundColor:this.contents.tableStripeBgColor}
                    }
                } else {
                    return ''
                }
            },
            headerRowStyle({ row, rowIndex}){
                return {color: this.contents.tableHeaderFontColor}
            },
            headerCellStyle({ row, rowIndex}){
                return {backgroundColor: this.contents.tableHeaderBgColor}
            },
            // 表格按钮
            contentTableBtnStyleChange(){
                // this.$nextTick(()=>{
                //   setTimeout(()=>{
                //     document.querySelectorAll('.table-content .tables .el-table__body .el-button--success').forEach(el=>{
                //       el.style.height = this.contents.tableBtnHeight
                //       el.style.color = this.contents.tableBtnDetailFontColor
                //       el.style.fontSize = this.contents.tableBtnFontSize
                //       el.style.borderWidth = this.contents.tableBtnBorderWidth
                //       el.style.borderStyle = this.contents.tableBtnBorderStyle
                //       el.style.borderColor = this.contents.tableBtnBorderColor
                //       el.style.borderRadius = this.contents.tableBtnBorderRadius
                //       el.style.backgroundColor = this.contents.tableBtnDetailBgColor
                //     })
                //     document.querySelectorAll('.table-content .tables .el-table__body .el-button--primary').forEach(el=>{
                //       el.style.height = this.contents.tableBtnHeight
                //       el.style.color = this.contents.tableBtnEditFontColor
                //       el.style.fontSize = this.contents.tableBtnFontSize
                //       el.style.borderWidth = this.contents.tableBtnBorderWidth
                //       el.style.borderStyle = this.contents.tableBtnBorderStyle
                //       el.style.borderColor = this.contents.tableBtnBorderColor
                //       el.style.borderRadius = this.contents.tableBtnBorderRadius
                //       el.style.backgroundColor = this.contents.tableBtnEditBgColor
                //     })
                //     document.querySelectorAll('.table-content .tables .el-table__body .el-button--danger').forEach(el=>{
                //       el.style.height = this.contents.tableBtnHeight
                //       el.style.color = this.contents.tableBtnDelFontColor
                //       el.style.fontSize = this.contents.tableBtnFontSize
                //       el.style.borderWidth = this.contents.tableBtnBorderWidth
                //       el.style.borderStyle = this.contents.tableBtnBorderStyle
                //       el.style.borderColor = this.contents.tableBtnBorderColor
                //       el.style.borderRadius = this.contents.tableBtnBorderRadius
                //       el.style.backgroundColor = this.contents.tableBtnDelBgColor
                //     })

                //   }, 50)
                // })
            },
            // 分页
            contentPageStyleChange(){
                let arr = []

                if(this.contents.pageTotal) arr.push('total')
                if(this.contents.pageSizes) arr.push('sizes')
                if(this.contents.pagePrevNext){
                    arr.push('prev')
                    if(this.contents.pagePager) arr.push('pager')
                    arr.push('next')
                }
                if(this.contents.pageJumper) arr.push('jumper')
                this.layouts = arr.join()
                this.contents.pageEachNum = 10
            },

            init () {
            },
            search() {
                this.pageIndex = 1;
                this.getDataList();
            },
            // 获取数据列表
            getDataList() {
                this.dataListLoading = true;
                let params = {
                    page: this.pageIndex,
                    limit: this.pageSize,
                    sort: 'id',
                }


                if(this.searchForm.exampaperName!='' && this.searchForm.exampaperName!=undefined){
                    params['exampaperName'] = '%' + this.searchForm.exampaperName + '%'
                }

                if(this.searchForm.exampaperTypes!='' && this.searchForm.exampaperTypes!=undefined){
                    params['exampaperTypes'] =  this.searchForm.exampaperTypes
                }
                if(this.searchForm.createTimeStartSearch!='' && this.searchForm.createTimeStartSearch!=undefined){
                    params['createTimeStartSearch'] =  this.searchForm.createTimeStartSearch
                }
                if(this.searchForm.createTimeEndSearch!='' && this.searchForm.createTimeEndSearch!=undefined){
                    params['createTimeEndSearch'] =  this.searchForm.createTimeEndSearch
                }

                if(this.searchForm.examquestionName!='' && this.searchForm.examquestionName!=undefined){
                    params['examquestionName'] = '%' + this.searchForm.examquestionName + '%'
                }

                if(this.searchForm.examquestionScoreStartSearch!='' && this.searchForm.examquestionScoreStartSearch!=undefined){
                    params['examquestionScoreStartSearch'] =  this.searchForm.examquestionScoreStartSearch
                }
                if(this.searchForm.examquestionScoreEndSearch!='' && this.searchForm.examquestionScoreEndSearch!=undefined){
                    params['examquestionScoreEndSearch'] =  this.searchForm.examquestionScoreEndSearch
                }



                this.$http({
                    url: "examquestion/page",
                    method: "get",
                    params: params
                }).then(({ data }) => {
                    if (data && data.code === 0) {
                        this.dataList = data.data.list;
                        this.totalPage = data.data.total;
                    } else {
                        this.dataList = [];
                        this.totalPage = 0;
                    }
                    this.dataListLoading = false;
                });

                //查询级联表搜索条件所有列表
                this.$http({
                    url: "dictionary/page?dicCode=exampaper_types&page=1&limit=100",
                    method: "get",
                    page: 1,
                    limit: 100,
                }).then(({ data }) => {
                    if (data && data.code === 0) {
                        this.exampaperTypesSelectSearch = data.data.list;
                    }
                });
                //查询当前表搜索条件所有列表
            },
            //每页数
            sizeChangeHandle(val) {
                this.pageSize = val;
                this.pageIndex = 1;
                this.getDataList();
            },
            // 当前页
            currentChangeHandle(val) {
                this.pageIndex = val;
                this.getDataList();
            },
            // 多选
            selectionChangeHandler(val) {
                this.dataListSelections = val;
            },
            // 添加/修改
            addOrUpdateHandler(id,type) {
                this.showFlag = false;
                this.addOrUpdateFlag = true;
                this.crossAddOrUpdateFlag = false;
                if(type!='info'){
                    type = 'else';
                }
                this.$nextTick(() => {
                    this.$refs.addOrUpdate.init(id,type);
                });
            },
            // 下载
            download(file){
                window.open(" ${file} ")
            },
            // 删除
            deleteHandler(id) {
                var ids = id
                        ? [Number(id)]
                        : this.dataListSelections.map(item => {
                            return Number(item.id);
                        });

                this.$confirm(`确定进行[${id ? "删除" : "批量删除"}]操作?`, "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                }).then(() => {
                    this.$http({
                        url: "examquestion/deleteExamquestion",
                        method: "post",
                        data: ids
                    }).then(({ data }) => {
                        if (data && data.code === 0) {
                            this.$message({
                                message: "操作成功",
                                type: "success",
                                duration: 1500,
                                onClose: () => {
                                    this.search();
                                }
                            });
                        } else {
                            this.$message.error(data.msg);
                        }
                    });
                });
            },
        }

    };
</script>


<style lang="scss" scoped>
.slt {
    margin: 0 !important;
    display: flex;
  }

  .ad {
    margin: 0 !important;
    display: flex;
  }

  .pages {
    & /deep/ el-pagination__sizes{
      & /deep/ el-input__inner {
        height: 22px;
        line-height: 22px;
      }
    }
  }


  .el-button+.el-button {
    margin:0;
  }

  .tables {
	& /deep/ .el-button--success {
		height: 30px;
		color: rgba(0, 0, 0, 1);
		font-size: 10px;
		border-width: 1px;
		border-style: none none solid none;
		border-color: #DCDFE6;
		border-radius: 0px;
		background-color: rgba(255, 255, 255, 1);
	}

	& /deep/ .el-button--primary {
		height: 30px;
		color: rgba(0, 0, 0, 1);
		font-size: 10px;
		border-width: 1px;
		border-style: none none solid none;
		border-color: #DCDFE6;
		border-radius: 0px;
		background-color: rgba(255, 255, 255, 1);
	}

	& /deep/ .el-button--danger {
		height: 30px;
		color: rgba(0, 0, 0, 1);
		font-size: 10px;
		border-width: 1px;
		border-style: none none solid none;
		border-color: #DCDFE6;
		border-radius: 0px;
		background-color: rgba(255, 255, 255, 1);
	}

    & /deep/ .el-button {
      margin: 4px;
    }
  }</style>


