<template>
  <div class="app-container">
    <!-- <div class="filter-container">
      <el-input
        placeholder="请输入搜索内容"
        v-model="searchValue"
        style="width: 200px;"
        class="filter-item"
        @keyup.enter.native="handleFilter"
      />
      <el-button
        v-waves
        class="filter-item"
        type="primary"
        icon="el-icon-search"
        @click="handleFilter"
      >搜索</el-button>
      <el-button
        class="filter-item"
        style="margin-left: 10px;"
        type="primary"
        icon="el-icon-edit"
        @click="handleCreate"
      >新增</el-button>
    </div> -->
    <!-- 列表 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column prop="name" align="center" label="产品名称" width="150"></el-table-column>
      <el-table-column prop="product_id" label="产品编号" width="150" align="center"></el-table-column>
      <el-table-column prop="price" label="产品价格" width="150" align="center"></el-table-column>
      <el-table-column prop="stocks" label="库存" width="110" align="center"></el-table-column>
      <el-table-column prop="status" label="产品状态" width="110" align="center"></el-table-column>
      <el-table-column prop="desc" label="产品描述"></el-table-column>
      <el-table-column label="操作" align="center" width="230" class-name="small-padding fixed-width">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="handleEdit(scope)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 编辑/新增 -->
    <el-dialog :title="title" :visible.sync="dialogEditFormVisible">
      <el-form label-position="left" label-width="70px" style="width: 400px; margin-left:50px;">
        <el-form-item label="产品图片">
          <el-upload
            class="avatar-uploader"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :on-change="handleChange"
            name="imageFile"
          >
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>

        <el-form-item label="产品名称">
          <el-input v-model="editProductName"/>
        </el-form-item>
        <el-form-item v-if="visable" label="产品编号">
          <el-input v-model="editProductId"/>
        </el-form-item>
        <el-form-item label="产品价格">
          <el-input v-model="editProductPrice"/>
        </el-form-item>
        <el-form-item label="产品库存">
          <el-input-number v-model="editProductStocks" :step="10"></el-input-number>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button>取消</el-button>
        <el-button type="primary">确认</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  //   filters: {
  //     statusFilter(status) {
  //       const statusMap = {
  //         published: "success",
  //         draft: "gray",
  //         deleted: "danger"
  //       };
  //       return statusMap[status];
  //     }
  //   },
  data() {
    return {
      list: null,
      listLoading: true,
      dialogEditFormVisible: false,
      editProductName: "",
      editProductId: "",
      editProductPrice: "",
      editProductStocks: "",
      imageUrl: "",
      searchValue:"",
      visable:'',
      title:'',
    };
  },
  created() {
    this.getProduct();
  },
  methods: {
    getProduct() {
      this.listLoading = true;
      this.$http.post("/productList").then(res => {
        console.log(res);
        this.listLoading = false;
        this.list = res.data.pageData;
      });
    },
    handleEdit(scope) {
      console.log(scope);
      this.title = '编辑'
      this.visable = true 
      this.dialogEditFormVisible = true;
      this.editProductName = scope.row.name;
      this.editProductId = scope.row.product_id;
      this.editProductPrice = scope.row.price;
      this.editProductStocks = scope.row.stocks;
    },
    handleAvatarSuccess(res, file) {
      console.log(file);
    },
    handleChange: function(res, file) {
      console.log(
        document.getElementsByClassName("el-upload__input")[0].files[0]
      );
      let fd = new FormData();
      fd.append(
        "fileToUpload",
        document.getElementsByClassName("el-upload__input")[0].files[0]
      );
      let xhr = new XMLHttpRequest();
      xhr.open("POST", "http://localhost:4208/upload");
      xhr.send(fd);
    },
    handleDelete(scope) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          this.$http.post("/deleteProduct", { id: scope.row.id }).then(res => {
            console.log(res);
            this.$message({
              type: "success",
              message: "删除成功"
            });
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    handleFilter(){
        this.$http.post('/productFilter',{search:searchValue}).then(res => {
            let pageData = res.data.pageData
            this.list = pageData
        })
    },
    handleCreate(){
      this.dialogEditFormVisible = true;
      this.title = '新增'
      this.visable = false

    }
  }
};
</script>
<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>

