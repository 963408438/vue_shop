<template>
    <div>
        <!--面包屑导航区域-->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>权限管理</el-breadcrumb-item>
            <el-breadcrumb-item>角色列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!--卡片视图-->
        <el-card>
            <!--            添加角色按钮区域-->
            <el-row>
                <el-col>
                    <el-button type="primary">添加角色</el-button>
                </el-col>
            </el-row>
            <!--角色列表区域-->
            <el-table :data="roleList" border stripe>
                <el-table-column type="expand"></el-table-column>
                <!--索引列-->
                <el-table-column type="index"></el-table-column>
                <el-table-column label="角色名称" prop="roleName"></el-table-column>
                <el-table-column label="角色描述" prop="roleDesc"></el-table-column>
                <el-table-column label="操作" width="300px">
                    <template slot-scope="scope">
                        <el-button size="mini" type="primary" icon="el-icon-edit" @click="showEditDialog(scope.row.id)">
                            编辑
                        </el-button>
                        <el-button size="mini" type="danger" icon="el-icon-delete" @click="removeRoleById(scope.row.id)">删除</el-button>
                        <el-button size="mini" type="warning" icon="el-icon-setting">分配权限</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </el-card>
        <!--修改用户对话框-->
        <el-dialog title="修改角色信息" :visible.sync="editDialogVisible"
                   width="30%" @close="editDialogClosed">
            <el-form :model="editForm" :rules="editFromRules"
                     ref="editFormRef" label-width="90px">
                <el-form-item label="角色名称" prop="roleName">
                    <el-input v-model="editForm.roleName"></el-input>
                </el-form-item>
                <el-form-item label="角色描述" prop="roleDesc">
                    <el-input v-model="editForm.roleDesc"></el-input>
                </el-form-item>
            </el-form>
            <!--底部区域-->
            <span slot="footer" class="dialog-footer">
                <el-button @click="editDialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="editRolesInfo">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "Roles",
        data() {
            return {
                //所有角色列表数据
                roleList: [],
                editDialogVisible : false,
                editForm : {},
                editFromRules : {
                    roleName:[
                        {required: true, message: '请输入角色名称', trigger: 'blur'},
                        {min: 2, max: 10, message: '角色名称的长度在2-10个字符之间', trigger: 'blur'}
                    ],
                    roleDesc:[
                        {required: true, message: '请输入角色描述', trigger: 'blur'},
                        {min: 3, max: 15, message: '角色描述的长度在3-15个字符之间', trigger: 'blur'}
                    ],
                }
            }
        },
        created() {
            this.getRoleList()
        },
        methods: {
            // 获取所有角色列表
            async getRoleList() {
                const {data: res} = await this.$http.get('roles');
                if (res.meta.status !== 200) return this.$message.error("获取角色列表失败");
                this.roleList = res.data;
                console.log(this.roleList)
            },
            async showEditDialog(id) {
                const {data: res} = await this.$http.get('/roles/' + id);
                if (res.meta.status !== 200) return this.$message.error("查询角色列表失败");
                this.editForm = res.data;
                this.editDialogVisible =  true
            },
            //监听修改角色列表对话框的关闭事件
            editDialogClosed() {
                this.$refs.editFormRef.resetFields()
            },
            editRolesInfo(){
                this.$refs.editFormRef.validate(async valid =>{
                    if (!valid) return;
                    console.log(this.editForm.id);
                    console.log(this.editForm.roleId);
                    const {data : res} = await  this.$http.put('roles/'+this.editForm.roleId,
                        {roleName:this.editForm.roleName,roleDesc:this.editForm.roleDesc});
                    if (res.meta.status!==200) return this.$message.error("修改角色信息失败");
                    this.editDialogVisible = false;
                    this.getRoleList();
                    this.$message.success("修改信息成功")
                })
            },
            async removeRoleById(id){
                const confirmResult = await  this.$confirm('此操作将永久删除该角色, 是否继续?', '提示',{
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).catch(err => err);
                if (confirmResult !== 'confirm'){
                    return this.$message.info('已取消删除')
                }
                console.log(id)
                // const {data : res} = await  this.$http.delete('roles/' + id);
                // if (res.meta.status !==200) return this.$message.error('删除失败');
                // this.getRoleList()
            }
        }
    }
</script>

<style lang="less" scoped>

</style>