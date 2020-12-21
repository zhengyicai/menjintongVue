<template> 
 <div class="block">   
	 	<el-col :span="20" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				<el-form-item style="width:300px">
					<el-input v-model="page.criteria" @keyup.enter.native="query"   placeholder="请输入[姓名|手机号]" style="width:300px"></el-input>
				</el-form-item>
				<el-form-item  >
					<el-button type="primary" v-on:click="query">查询</el-button>
				</el-form-item>
               
				
			</el-form>
		</el-col>
        <el-col :span="4" class="toolbar" style="padding-bottom: 0px;">
			<el-form :inline="true">
				
				<el-form-item  style="text-align:right">
					<el-button type="primary" @click="add()">新增</el-button>
				</el-form-item>
			</el-form>
		</el-col>
      
        
		<el-table :data="datalist" highlight-current-row v-loading="listLoading" style="width: 100%;">
		
			
			<el-table-column prop="name" label="姓名" width="120" sortable>
			</el-table-column>
			<el-table-column prop="mobile" label="手机号" width="150" sortable>
			</el-table-column>
			<!-- <el-table-column prop="communityName" label="所属小区" width="150" sortable>
			</el-table-column> -->
            <el-table-column prop="equId" label="楼栋单元" width="200" sortable>
			</el-table-column>
             <el-table-column prop="unitNo" label="房间号" width="170" sortable>
			</el-table-column>

			<el-table-column  label="创建时间" min-width="170">
				<template slot-scope="scope">{{ scope.row.createTime | moment('YYYY-MM-DD HH:mm:ss') }}</template>
			</el-table-column>

          
            <!-- <el-table-column prop="wxId" label="微信名" width="200" sortable>
			</el-table-column> -->
          
			
			<el-table-column  label="状态" min-width="120">
				<template slot-scope="scope">{{ state(scope.row.state)}}</template>
			</el-table-column>
            <el-table-column prop="remark" label="微信昵称" width="170" sortable>
			</el-table-column>
			
			
			<el-table-column label="操作" min-width="200">
				<template scope="scope">
				<!-- <el-button size="small" type="primary"  @click="edit(scope.$index,scope.row)">编辑</el-button>
				<el-button size="small" type="primary"  v-if='scope.row.sysUserId=="" ||  scope.row.sysUserId ==null' @click="addAdmin(scope.$index,scope.row)">新增物业</el-button>
				<el-button size="small" type="warning"  v-if='scope.row.sysUserId!="" ||  scope.row.sysUserId !=null' @click="editAdmin(scope.$index,scope.row)">修改物业</el-button>
                <el-button size="small" type="danger" @click="deleteRow(scope.$index, scope.row)">删除</el-button> -->
                <el-button size="small" type="primary"  @click="updateDetail(scope.$index,scope.row)">修改</el-button>
               
                <el-button size="small" type="warning" v-if="scope.row.state==='10'"   @click="updateState(scope.row,'20')" >禁用</el-button>
                <el-button size="small" type="success" v-if="scope.row.state==='20'" @click="updateState(scope.row,'10')" >启用</el-button>
                <el-button size="small" type="info" v-if="scope.row.state==='30'"  @click="updateState(scope.row,'10')">授权</el-button>
                <el-button size="small" type="danger" @click="deleteRow(scope.$index, scope.row)">解绑</el-button>
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

        <el-dialog   :title="formtitle" :visible.sync="dialogFormVisibleEqu" >
			<el-table :data="residRooms" highlight-current-row style="width: 100%;">
                    <el-table-column prop="buildingName" label="楼栋" width="100" sortable>
                    </el-table-column>
                    <el-table-column prop="unitName" label="单元" width="100" sortable>
                    </el-table-column>
                    <el-table-column prop="roomName" label="房间" width="100" sortable>
                    </el-table-column>
                    <el-table-column label="户主" width="100" sortable>
                            <template slot-scope="scope">{{ scope.row.owner =='10'?'是':'否'}}</template>
                    </el-table-column>
                    <el-table-column  label="操作" min-width="120" sortable>
                         <!-- <template slot-scope="scope"><el-button size="small" type="danger" @click="deleteItem(scope.row)">删除</el-button></template> -->
                        
                    </el-table-column>
                    
            </el-table>
			
        </el-dialog>


         <el-dialog   :title="formtitle" :visible.sync="dialogFormVisibleEqu" width="70%" >
          
            <el-row>
                <el-col :span="24" style="font-size:14px;">
                    <el-card>
                            <table>
                            <!-- <label style="font-weight:bold"><input type="checkbox" :checked="choOne"  @click="isSelectedOne($event)"/>全选</label> -->
                            
                          
                            
                        </table>
                    </el-card>
                </el-col>
            
            </el-row>
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisibleEqu = false">取 消</el-button>
				<el-button type="primary" @click="addCho()">确 定</el-button>
			</div>
        </el-dialog>




         <el-dialog   :title="str11" :visible.sync="dialogFormVisibleRoom" >
			<el-form ref="subData" :model="subData1" label-width="100px" tyle="margin:0px;">
                <el-form-item label="手机号">
                        <el-input   :disabled="disabled1" style="width:60%"   v-model="one" type="number" placeholder="请输入手机号"></el-input>
                </el-form-item>
                <el-form-item label="用户名">
                        <el-input  :disabled="disabled1" style="width:60%" v-model="two"  placeholder="请输入用户名"></el-input>
                </el-form-item>
                 <!-- <el-form-item label="密码">
                        <el-input style="width:60%" v-model="three"  placeholder="请输入密码"></el-input>
                </el-form-item>  -->
                <el-form-item label="单元">   
                        <el-select  v-model="equselect" placeholder="请选择单元" @change="selectProvince(equselect)">
                            <el-option   :label="m.equId" :key="m.id" :value="m.id" v-for="m in parentMenuOneData">{{m.equId}}</el-option> 
                        </el-select>
                </el-form-item>        
                 <el-form-item label="房间号">   
                        <el-input v-model="cardNo" style="width:60%" placeholder="请输入房间号"> </el-input>
                </el-form-item>    
			</el-form>	
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisibleRoom = false">取 消</el-button>
				<el-button  type="primary" @click="open1()">确 定</el-button>
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

      add(){
          this.one="";
          this.two="";
          this.three="";
          this.dialogFormVisibleRoom = true;
          this.equselect="";
          this.cardNo="";
           this.disabled1=false;
          this.str11="新增用户";
          this.communityId = sessionStorage.getItem("communityId");
         this.loadMenus();
           

      },
       updateDetail(index, row){
      
          this.dialogFormVisibleRoom = true;
          this.str11="修改用户";
          this.communityId = sessionStorage.getItem("communityId");
         
           this.disabled1="disabled";
           this.one=row.mobile;
           this.two=row.name;
           this.subData1.password="";
           this.subData1.communityId = sessionStorage.getItem("communityId");
           this.subData1.equipmentId = row.equselect;
          // this.equselect = row.equipmentId;
           //this.cardNo = row.unitNo;
           this.residentId= row.id;
            this.loadMenus();
           
           

      },
      selectProvince(str){
         

         for(var i= 0;i<this.parentMenuOneData.length;i++){
             if(str==this.parentMenuOneData[i].id){
                 if(this.parentMenuOneData[i].equCode.length==8){
                       this.cardNo= this.parentMenuOneData[i].equCode.substr(4,8);      
                 }else{
                     this.cardNo="";
                 }
                
             }
         }
      },
      deleteItem(){

      },
      showRelationPanel(index, rows){
          this.dialogFormVisible= true;
          this.communityId = rows.communityId;
                this.residentId = rows.id;
               
                this.loadRooms();
      },
      /**
        * 加载住户房间信息
        */
      

      loadRooms(){
         
            var room = {
                    residentId: this.residentId,
                    communityId: this.communityId
                };
            RequestGet("/resident/findResidentRooms",room).then(response => {
						if(response.code == '0000'){
								 this.residRooms = response.data;
						 }
					
            }).catch(error => {
                            this.$router.push({ path: '/login' });
                            
            })  
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

                RequestPost("/resident/updateState",opt).then(response => {
                            
                          
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
      open1(){
         
        this.subData1= {}; 
     //   this.subData1.remark = this.one+","+this.two+","+this.three+","+this.four+","+this.free;  
       //this.subData1.residentId = this.residentId;
       this.subData1.mobile=this.one;
       this.subData1.name=this.two;
        this.subData1.password="";
        this.subData1.communityId = sessionStorage.getItem("communityId");
        this.subData1.equipmentId = this.equselect;
        this.subData1.unitNo = this.cardNo;


        if(this.str11=="新增用户"){
                RequestPost("/resident/addUser",this.subData1).then(response => {
                            if(response.code=='0000'){
                                this.$message({
                                    message: response.message,
                                    type: 'success'
                                });  
                                this.dialogFormVisibleRoom = false;
                                this.loadData();
                            }else{
                                this.$message({
                                    message: response.message,
                                    type: 'error'
                                });
                            }
                            //this.loadData();
                            //this.communityId = this.subData.communityId;
                            //this.loadCommunityData();
                }).catch(error => {
                this.$router.push({ path: '/login' });
                })
        }else{
               this.subData1.id = this.residentId;
               RequestPost("/resident/updateUser",this.subData1).then(response => {
                            if(response.code=='0000'){
                                this.$message({
                                    message: response.message,
                                    type: 'success'
                                });  
                                this.dialogFormVisibleRoom = false;
                                this.loadData();
                            }else{
                                this.$message({
                                    message: response.message,
                                    type: 'error'
                                });
                            }
                            //this.loadData();
                            //this.communityId = this.subData.communityId;
                            //this.loadCommunityData();
                }).catch(error => {
                this.$router.push({ path: '/login' });
                })
        }
       // this.loadData();
    
        

    },
      updateRoom(rows){
         
         this.subData1 = {};
         this.subData = rows;

         this.one = "";
         this.two = "";
         this.three = "";
         this.four = "";
         this.free = "";
         this.residentId = rows.id;

       
         RequestGet("/resident/findResidentCount",{residentId:this.residentId}).then(response => {
            if(response.code == '0000'){
                    if(response.data == 0){
                        this.dialogFormVisibleRoom = false;    
                        this.$message({
								message: "请先授权设备",
								type: 'error'
						});  
                    }else{
                        this.dialogFormVisibleRoom = true;    
                    }
            }
					
        }).catch(error => {
                        this.$router.push({ path: '/login' });
                        
        })

     
         //this.subData1.roomId  = rows.id;
     


         RequestGet("/resident/findCard",{residentId:rows.id}).then(response => {
                    if(response.code=='0000'){
                          if(response.data.length>0){
                              for(var i = 0 ;i<response.data.length;i++){
                                    if(i ==0){
                                      this.one = response.data[i].cardNo+"";
                                  }else if(i ==1){
                                      this.two = response.data[i].cardNo+"";
                                  }else if(i ==2){
                                      this.three = response.data[i].cardNo+"";
                                  }else if(i ==3){
                                      this.four = response.data[i].cardNo+"";
                                  }else if(i ==4){
                                      this.free = response.data[i].cardNo+"";
                                  }
                              }
                          }
                          


                    }else{
                        this.$message({
                            message: response.message,
                            type: 'error'
                        });
                    }
                // this.loadCommunityData();
        }).catch(error => {
        this.$router.push({ path: '/login' });
        })
         

    },  
      
      isSelectedOne(e){
          this.selectedOneData = [];
            var cho = e.target.checked;  //判断是否选中
            
            if(cho){
                for(var i = 0 ;i<this.parentMenuOneData.length;i++){
                    this.selectedOneData.push(this.parentMenuOneData[i].id);
                }
                this.choOne = true;
              
            }else{
              
                this.selectedOneData=[];
                this.choOne = false;
            }
      },
      open(){
          RequestPost("/equipment/add",this.subData).then(response => {
						
						//this.logining = false; 
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
          
      },
      getUsers(){},


      
	  deleteRow(index, rows) {
       this.$confirm('确认销毁, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
            // this.$message({
            //     type: 'success',
            //     message: '删除成功!'
            // });

            RequestPost("/resident/delete",rows).then(response => {
						
						//this.logining = false; 
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
       edit(index, rows){
            this.isEdit = true;
            this.dialogFormVisibleEqu = true;
		    this.formtitle ="绑定房间";   
           
            this.residentId = rows.id;
            
            this.updateDate = true; 
            



            this.loadMenus();
        

      },
     /* 
       * 加载菜单
    */
    loadMenus(){
        this.page.pageSize=9999;
          this.communityId = sessionStorage.getItem("communityId");   
        RequestGet("/equipment/findCommunitys",{communityId:this.communityId}).then(response => {
						if(response.code == '0000'){
                              //this.parentMenuOneData = response.data;
                             this.parentMenuOneData = [];
                             this.parentMenuTwoData = [];
                              for(var i= 0;i<response.data.length;i++){
                                 // if(response.data[i].equipmentType=='20'){
                                      this.parentMenuOneData.push(response.data[i]);
                                //   }else{
                                //       this.parentMenuTwoData.push(response.data[i]);
                                   
                                //   }
                              }
							// 	 this.datalist = response.data;
							// 	 this.total = response.page.totalCount; 
                            // 	 this.totalsize  = response.page.pageSize;
                            this.page.pageSize = PageSize;  //一页显示的条数
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  
      
    },  
	 addCho(){
            
            
            this.subData1.choId = this.selectedOneData; 
            this.subData1.communityId = sessionStorage.getItem("communityId");
            this.subData1.userId = sessionStorage.getItem("userId");
            this.subData1.id = this.residentId;
            this.subData1.cardNo = this.cardNo;
            this.subData1.equipmentId = this.equselect;
            if(!this.updateDate){
                RequestPost("/user/updateResidentEquipment",this.subData1).then(response => {
                            if(response.code=='0000'){
                                this.$message({
                                    message: response.message,
                                    type: 'success'
                                });  
                                this.dialogFormVisibleEqu = false;
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
            }else{
                RequestPost("/user/updateResidentEquipment",this.subData1).then(response => {
                            if(response.code=='0000'){
                                this.$message({
                                    message: response.message,
                                    type: 'success'
                                });  
                                 this.dialogFormVisibleEqu = false;
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
            }

             


              

      },
	loadData(){
        if(sessionStorage.getItem("loginName")=="admin"){
            
        }else{
            this.page.communityId = sessionStorage.getItem("communityId");
    
        }
        
		RequestGet("/resident/residentList",this.page).then(response => {
						if(response.code == '0000'){
								 this.datalist = response.data;
								 this.total = response.page.totalCount; 
								 this.totalsize  = response.page.pageSize;
						 }
					
		}).catch(error => {
						 this.$router.push({ path: '/login' });
						
		})  
        
						
	
	},

  
    
  



	},
	//1
	created:function(){
		//3
		this.loadData();
		
	
	},
    data() {
      return {
		total:0,     //数据的总数量
		totalsize:0,  //总的页数 = 总数量/每页显示的条数
        currentPage:1,
        str11:"",
		page:{
			pageSize:PageSize,   //一页显示的条数
            criteria:''
        },
        formtitle:"用户住房信息",
        datalist:[],
        communityId:'',
        residentId:'',
		residRooms:[],
		listLoading: false,
		form:{},
        subData:{},
        isEdit:true, //是否禁用
        dialogFormVisible:false,
        dialogFormVisibleEqu:false,  //显示设备列表
        
        parentMenuOneData:[],
        parentMenuTwoData:[],
        selectedTwoData:[],
        selectedOneData:[],
        selectedTwoDataAll:[],
        selectedOneDataAll:[],
        choTwo:false,
        choOne:false,
        subData1:{},
        userEquipmentList:[],
        residentId:"",
        updateDate:false,
        cardNo:"",
        equselect:"",


        // 房卡
        dialogFormVisibleRoom:false,
        dialogFormVisibleRoomDevice:false,
        one:"",
        two:"",
        three:"",
        four:"",
        free:"",
        
        oneId:"",
        twoId:"",
        threeId:"",
        fourId:"",
        freeId:"",
        
        roomNo:"",
        roomId:"",
        
        oneOld:"",
        twoOld:"",
        threeOld:"",
        fourOld:"",
        freeOld:"",

        oneSum:false,
        twoSum:false,
        threeSum:false,
        fourSum:false,
        freeSum:false,
        disabled1:"",
       
       
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
