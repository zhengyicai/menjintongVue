<template> 
 <div class="block">   
	 	<el-col :span="17" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				<el-form-item style="width:300px">
					<el-input v-model="page.criteria"  @keyup.enter.native="query"  placeholder="请输入设备[编号|名称]" style="width:300px"></el-input>
				</el-form-item>
				<el-form-item  >
					<el-button type="primary" v-on:click="query">查询</el-button>
				</el-form-item>
                <el-form-item  >
					<el-button type="success" v-on:click="query">刷新</el-button>
				</el-form-item>
				
				
			</el-form>
		</el-col>
        <el-col :span="7" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				
				<el-form-item  style="text-align:right">
					<!-- <el-button type="primary" @click="add()">新增</el-button> -->
				</el-form-item>
                 <el-form-item  style="text-align:right">
					<el-button type="danger" @click="delete1()">一键删除</el-button>
				</el-form-item>
                <el-form-item  style="text-align:right">
					 <!--  action="https://wx.szrunlifang.com/cms/equipment/addUpload" -->
                     <el-upload
                      
                        class="upload-demo"
                        action="https://wx.szrunlifang.com/cms/equipment/addUpload"
                        :data="subData"
                        :limit="1"
                        :on-preview="handlePreview"
                        :on-success="uploadok"
                        :on-error="uploaderror"
                        :on-remove="handleRemove"
                        :file-list="fileList"
                        list-type="picture">
                        <el-button  type="primary">一键导入</el-button>
                       
                    </el-upload>        
                </el-form-item>
               

			</el-form>
		</el-col>
      
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			
			<el-table-column prop="equipmentId" label="设备编号" width="200" sortable>
			</el-table-column>
			<el-table-column prop="equipmentName" label="设备名称" width="200" sortable>
			</el-table-column>
            <!-- <el-table-column prop="ename" label="设备英文名称" width="200" sortable>
			</el-table-column> -->
			<el-table-column prop="communityName" label="所属小区" width="150" sortable>
			</el-table-column>

            <!-- <el-table-column prop="communityName" label="所属小区" width="150" sortable>
			</el-table-column> -->
			<!-- <el-table-column prop="buildingName" label="楼栋" width="150" sortable>
			</el-table-column> -->
            <el-table-column prop="ip" label="IP" width="200" sortable>
			</el-table-column>
            <el-table-column  label="创建时间" min-width="120">
				<template slot-scope="scope">{{ scope.row.createTime | moment('YYYY-MM-DD') }}</template>
			</el-table-column>
            <el-table-column  label="最新设备状态" min-width="150">
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowDateStatus=='10'" ><i class="tip" style="background:green;"></i>在线</span> <span class="span" v-if="scope.row.nowDateStatus=='20'"><i class="tip" ></i>离线</span></template>
			</el-table-column>
             <!-- <el-table-column  label="更新状态时间" min-width="120">
				<template slot-scope="scope">{{ scope.row.onlineCreateTime | moment('YYYY-MM-DD HH:mm:ss') }}</template>
			</el-table-column> -->

			<!-- <el-table-column  label="单元" min-width="150" sortable>
                            <template slot-scope="scope">{{scope.row.unitName}} <span v-if="scope.row.unitName!='' && scope.row.unitName!=null ">单元</span></template>    
			</el-table-column> -->
			<el-table-column  label="状态" min-width="120">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
            <!-- <el-table-column  label="最新设备状态" min-width="150">
                
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowDateStatus=='离线'"><i class="tip"></i></span> <span class="span" v-if="scope.row.nowDateStatus=='在线'"><i class="tip" style="background:green;"></i></span>{{scope.row.nowDateStatus}}</template>
			</el-table-column>
            <el-table-column  label="最新门磁状态" min-width="150">
				<template slot-scope="scope"><span class="span" v-if="scope.row.nowState=='异常'"><i class="tip"></i></span> <span class="span" v-if="scope.row.nowState=='正常'"><i class="tip" style="background:green;"></i></span>{{scope.row.nowState}}</template>
			</el-table-column> -->
			
			
			<el-table-column label="操作" min-width="120">
				<template scope="scope">
				<!-- <el-button size="small" type="primary"  @click="edit(scope.$index,scope.row)">编辑</el-button>
				<el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增物业</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" ||  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改物业</el-button> -->
               <el-button size="small" type="warning" v-if="scope.row.state==='10'"   @click="updateState(scope.row,'20')" >禁用</el-button>
                <el-button size="small" type="success" v-if="scope.row.state==='20'" @click="updateState(scope.row,'10')" >启用</el-button>
                <!-- <el-button size="small" type="warning" @click="updateRow(scope.$index, scope.row)">替换</el-button> -->
				</template>
			</el-table-column>
		</el-table>

		<el-pagination
		 	class="pull-right clearfix"
			@current-change="handleCurrentChange"
			:current-page="currentPage"
			:page-size="totalsize" 
			layout="total, prev, pager, next, jumper"
			:total="total" 
		>
		</el-pagination>

        <el-dialog   :title="formtitle" :visible.sync="dialogFormVisible" >
			<el-form ref="subData" :model="subData" label-width="100px" @submit.prevent="onSubmit" style="margin:20px;">
                    
                    <!-- <el-form-item label="设备类型">
                       <el-select  v-model="subData.equipmentType" placeholder="请选择设备类型" @change="selectEquType(subData.equipmentType)">
                           <el-option :label="item.label" :key="item.value" :value="item.value" v-for="item in options">{{item.label}}</el-option>
					    </el-select>
                    </el-form-item>  -->

                       

                      <el-form-item label="*设备编号">
                        <el-input v-model="subData.equNo" type="number"  placeholder="请输入6位设备编号"></el-input>
                    </el-form-item>
                    <el-form-item label="*设备名称">
                        <el-input v-model="subData.equipmentName"  placeholder="请输入设备名称"></el-input>
                    </el-form-item>
                    
                    <el-form-item label="所属小区">
                        <el-select  v-model="subData.communityId" placeholder="请选择设备类型"    @change="selectCommunity(subData.communityId)">
                           <el-option :label="item.name" :key="item.value" :value="item.value" v-for="item in communitys">{{item.name}}</el-option>
					    </el-select>
                    </el-form-item>
                   
                    <!-- <el-form-item label="*设备序列号">
                        <el-input  v-model="subData.equId"  placeholder="请输入设备序列号后8位"></el-input>
                    </el-form-item> -->
                    <el-form-item label="状态">
					<el-radio-group v-model="subData.state">
						<el-radio label="10">正常</el-radio>
						<el-radio label="20">禁用</el-radio>
					</el-radio-group>
				</el-form-item>
			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="open()">确 定</el-button>
			</div>
        </el-dialog>



        <el-dialog   title="替换设备" :visible.sync="updateDialogFormVisible" >
			<el-form ref="subData1" :model="subData1" label-width="120px" @submit.prevent="onSubmit" style="margin:20px;">
                    <el-form-item label="*设备名称">
                        <el-input v-model="subData1.equipmentName"  placeholder="请输入设备名称"></el-input>
                    </el-form-item>                   
                    <el-form-item label="原设备序列号">
                        <el-input  v-model="subData1.oldequId" disabled="disabled"  placeholder="请输入设备序列号后8位"></el-input>
                    </el-form-item>

                    <el-form-item label="*新设备序列号">
                        <el-input  v-model="subData1.newequId"  placeholder="请输入设备序列号后8位"></el-input>
                    </el-form-item>
			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="updateDialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="open1()">确 定</el-button>
			</div>
        </el-dialog>

		 

  </div>
</template>
<script>
	//2
	import { RequestPost } from '../../api/api';
    import { RequestGet } from '../../api/api';
	import { url } from '../../api/api';
	import {timeFormat} from '../../api/format';
	import {dateFormat} from '../../api/format';
	import {state} from '../../api/format';
	import { PageSize } from '../../api/api';
	import moment from 'moment';
  	export default {
	  
    methods: {
	  dateFormat,	
	  timeFormat,
	  state,	
      handleSizeChange(val) {
		console.log(`每页 ${val} 条`);
		

      },
      handleCurrentChange(val) {
		console.log(`当前页: ${val}`);
		this.pageNumber = val;

		
		this.page.pageNumber = val;
		/**
		 * 1.get 请求传参数，必须是json前面加一个params
		 * 2.post传参数不需要在json对象传值前面带params
		 */
		this.loadData();


	  },
       /**
        * 查询
        */
        query(){
            this.loadData();
        },
        submitUpload:function () {
            this.$refs.upload.submit();
        },
        uploadok(res,file){

            
            
           this.$message({
                message:'设备导入成功' ,
                type: 'success'
            });  
            this.fileList = [];

          this.loadData();
        },

        uploaderror(){
            alert("上传异常");
        },
        handleRemove(file, fileList) {
            console.log(file, fileList);
        },
        handlePreview(file) {
            alert(file);
            console.log(file);
        },
        uploadSuccess:function(response, file, fileList) {
            console.log("上传文件成功response" +response);
            console.log("上传文件成功file" +file);
            console.log("上传文件成功fileList" +fileList);
            // response：即为后端传来的数据，这里我返回的是图片的路径
            app.user.userImage=response;
        },
        uploadError:function (response, file, fileList) {

            console.log("上传文件失败response" +response);
            console.log("上传文件失败file" +file);
            console.log("上传文件失败fileList" +fileList);
        },
        delete1(){

             this.$confirm('该操作会删除现有数据,谨慎操作, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
               

                var opt ={
                    communityId:sessionStorage.getItem("communityId")
                    
                }

                RequestPost("/equipment/deleteAll",opt).then(response => {
                            
                          
                    if(response.code=='0000'){
                        this.$message({
                            message: response.message,
                            type: 'success'
                        });  
                        //this.dialogFormVisible = false;
                    }else{
                        this.$message({
                            message: response.message,
                            type: 'error'
                        });
                    }
                    this.loadData();
                }).catch(error => {
                this.$router.push({ path: '/login' });
                })


                
               // this.dialogFormVisible = false;
                
                }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除'
                });          
            });



        },
        updateState(rows,state){

           this.$confirm('确认该操作, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
               

                var opt ={
                    communityId:rows.communityId,
                    id:rows.id,
                    state:state
                }

                RequestPost("/equipment/updateState",opt).then(response => {
                            
                          
                    if(response.code=='0000'){
                        this.$message({
                            message: response.message,
                            type: 'success'
                        });  
                        this.dialogFormVisible = false;
                    }else{
                        this.$message({
                            message: response.message,
                            type: 'error'
                        });
                    }
                    this.loadData();
                }).catch(error => {
                this.$router.push({ path: '/login' });
                })


                
                this.dialogFormVisible = false;
                
                }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除'
                });          
            });



      },
       add(){
           this.dialogFormVisible = true;
		   this.formtitle ="新增设备";   
		   this.subData = {}; 
           this.communityId = ''; 
           this.buildingId = '';
           this.unitNo = '';
            this.loadCommunitys();
            this.isEdit = true;
            this.subData = {
                equipmentName: '',
                equipmentType: '10',
                state: '10',
                communityId:this.communityId,
                buildingId:this.buildingId,
                unitName:this.unitNo,
                equCode:"",
                equId:"",
                equNo:""
                
            };


      },
      open(){


          if(this.validate() == false){
              //this.subData.equId=="";
              return;
          }
          this.subData.equCode = this.subData.equipmentType =="10"?"0000000000":this.subData.equipmentType =="20"?"0000000000":"FFFF000000";
          this.subData.equId = this.communityNo+this.subData.equId;
          
          RequestPost("/equipment/add",this.subData).then(response => {
						
						//this.logining = false; 
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
                            this.dialogFormVisible = false;
                            //this.subData = {};
						}else{
							this.$message({
								message: response.message,
								type: 'error'
							});
						}
						this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })
          
      },


      open1(){

          if(this.subData1.equipmentName == ""){
            this.$message({
                type: 'error',
                message: '请输入设备名称'
            });
              return;
          }
          if(this.subData1.newequId == "" || this.subData1.newequId.length>8){
            this.$message({
                type: 'error',
                message: '请输入设备序列号后8位'
            });
              return;
          }
         
          this.communityNo = (Array(4).join(0) +sessionStorage.getItem("code")).slice(-4);
         // alert(this.communityNo+this.subData1.equCode);  
          this.subData1.equId =this.communityNo+this.subData1.newequId;
          this.subData1.equCode = this.subData.equipmentType =="10"?"0000000000":this.subData.equipmentType =="20"?"0000000000":"FFFF000000";
          //this.subData1.equId = this.communityNo+this.subData1.equId;
          this.subData1.equCardState = "20"; //修改设备房卡关联数据状态
          RequestPost("/equipment/update",this.subData1).then(response => {
						
						
						if(response.code=='0000'){
							this.$message({
								message: response.message,
								type: 'success'
							});  
                            this.updateDialogFormVisible = false;
                        
						}else{
							this.$message({
								message: response.message,
								type: 'error'
							});
						}
						this.loadData();
            }).catch(error => {
            this.$router.push({ path: '/login' });
            })
          
      },
      getUsers(){},

      updateRow(index,rows){
            this.subData1 = rows;
            this.updateDialogFormVisible = true;
            this.subData1.equipmentName = rows.equipmentName;
            this.subData1.oldequId = rows.equId.substr(4,12);
            this.subData1.newequId = "";
            

      },
 
	  deleteRow(index, rows) {
       this.$confirm('确认删除, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
            this.$message({
                type: 'success',
                message: '删除功能暂时未开放!'
            });


            //删除功能暂时不开放
            // RequestPost("/equipment/delete",rows).then(response => {
						
			// 			//this.logining = false; 
            //     if(response.code=='0000'){
            //         this.$message({
            //             message: response.message,
            //             type: 'success'
            //         });  
            //         this.dialogFormVisible = false;
            //     }else{
            //         this.$message({
            //             message: response.message,
            //             type: 'error'
            //         });
            //     }
            //     this.loadData();
            // }).catch(error => {
            // this.$router.push({ path: '/login' });
            // })


            
            this.dialogFormVisible = false;
            
            }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
      },
     

	
	loadData(){

		RequestGet("/equipment/findAll",this.page).then(response => {
						if(response.code == '0000'){
								 this.datalist = response.data;
								 this.total = response.page.totalCount; 
								 this.totalsize  = response.page.pageSize;
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  
        
						
	
	},

  
    
    /**
    * 选择设备类型
    */
    selectEquType(){
        if(this.subData.equipmentType === '30'){
            this.isEdit = false;
        }else{
            this.isEdit = true;
        }
    },

    // /**
    // * 选择小区
    // */
    selectCommunity(selectIdx){
        
        this.communityId = selectIdx;
        this.subData.communityId = selectIdx;
        this.buildingId = '';
        this.subData.buildingId = "";
        this.buildings = [];
        this.unitNo = '';
        this.loadBuildings();

        this.findOne(selectIdx);
    },

    //
    findOne(one){
        RequestGet("/equipment/findOne",{
                id:one
            }).then(response => {
          
						if(response.code == '0000'){
                              
                                this.communityNo = (Array(4).join(0) + response.data.code).slice(-4);
                              
    
						 }
					
        }).catch(error => {
               // this.$router.push({ path: '/login' });
						
		})  
    },

    // /**
    // * 选择楼栋
    // */
    selectBuild(selectIdx){
        //this.buildingId = this.buildings[selectIdx].value;
        this.buildingId = selectIdx;
        this.subData.buildingId = this.buildingId;
        this.subData.unitName = '';
        this.units = [];
        this.unitNo = '';
        this.loadUnits();
    },

    // /**
    // * 选择单元
    // */
    selectUnit(selectIdx){
        this.unitNo = selectIdx;
        this.subData.unitName = this.unitNo;
    },

   
    /**
    * 加载小区
    */
    loadCommunitys(){

        RequestGet("/equipment/findCommunitys").then(response => {
          
						if(response.code == '0000'){
								this.communitys = response.data;
                                
                                
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
      
    },

    /**
    * 加载楼栋
    */
    loadBuildings(){

        RequestGet("/equipment/findBuildings",{
                communityId:this.communityId
            }).then(response => {
          
						if(response.code == '0000'){
								 this.buildings = response.data;
                                 if(this.buildings.length>0){
                                     this.subData.buildingId = this.buildings[0].value;
                                 }
                                 
                                // for(let obj in this.buildings){
                                //     this.selectBuild(0);
                                //     break;
                                // }
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
       
    },

    // /**
    // * 加载单元
    // */
    loadUnits(){

         RequestGet("/equipment/findUnits",{
                buildingId:this.buildingId
            }).then(response => {
          
						if(response.code == '0000'){
								  this.units = response.data;
                                   if(this.units.length>0){
                                     this.subData.unitName = this.units[0].value;
                                 }
                                // for(let obj in this.buildings){
                                //     this.selectBuild(0);
                                //     break;
                                // }
						 }
					
        }).catch(error => {
                this.$router.push({ path: '/login' });
						
		})  
        
    },
    validate(){
        if(this.subData.equNo.trim()=="" || this.subData.equNo == null){
            this.$message({
                type: 'error',
                message: '设备编号不能为空'
            });         
            return false;
        }
        
        if(this.subData.equipmentName.trim()=="" || this.subData.equipmentName == null){
            this.$message({
                type: 'error',
                message: '设备名称不能为空'
            });         
            return false;
        }

        if(this.subData.communityId.trim()=="" || this.subData.communityId == null){
            this.$message({
                type: 'error',
                message: '所属小区不能为空'
            });         
            return false;
        }

        if(this.subData.equNo.trim().length>6){
            this.$message({
                type: 'error',
                message: '请输入6位设备序列号'
            });         
            return false;
        }

        


    },




	},
	//1
	created:function(){
        //3

        
        setInterval(() => {
          this.page.pageNumber =1;  
         this.datalist=[];
         this.currentPage=1;
         this.total = 1;
          this.loadData();
        }, 60000);
        
        


        this.loadData();
       // alert(sessionStorage.getItem("communityId"));
        this.subData.communityId = sessionStorage.getItem("communityId"); 
		
	
	},
    data() {
      return {
       
		total:0,     //数据的总数量
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
		currentPage:1,
		page:{
			pageSize:PageSize,   //一页显示的条数
            criteria:''
		},
        datalist:[],
        fileList: [],
		
		listLoading: false,
        form:{},
        communityNo:"",
        subData:{},
        isEdit:true, //是否禁用
        dialogFormVisible:false,
        communitys:[],
        buildings:[],
        units:[],
        communityId:"",
        buildingId:"",
        unitNo:"",
        formtitle:"",
         options: [{
          value: '10',
          label: '其它'
        }, {
          value: '20',
          label: '围墙机'
        }, {
          value: '30',
          label: '单元门口机'
        }],
        updateDialogFormVisible:false,
        subData1:{newequId:""},

      };
    }
  }
</script>
<style>
	.el-dialog--small {
		 width: 30%; 
	}
    .span {
        position:relative;
        padding:8px;
    }
    .tip {
        display:block;
        background:#f00;
        border-radius:50%;
        width:8px;
        height:8px;
        top:12px;
        right:0px;
        position:absolute;
    }
</style>
