<template>
  <div class="container">
    <div class="optionTool">
      <el-row><el-col :span="12">
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  :disabled="deleteCommentBtn? true : false"
                  @click="deleteComment"
                >删除({{ multipleSelection.length }})</el-button>
              </el-col>
        <el-col :span="12">
          <el-input v-model="search" placeholder="搜索评论" />
        </el-col>
      </el-row></div>
    <el-table
      ref="multipleTable"
      :data="tableData"
      tooltip-effect="dark"
      style="width: 100%"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column label="评论" width="400">
        <template slot-scope="scope">{{ scope.row.comment>20? scope.row.comment.substring(0, 20) + '...' : scope.row.comment }}</template>
      </el-table-column>
      <el-table-column label="状态" width="80">
        <template slot-scope="scope">
          <el-tag
            :type="scope.row.status? 'success': 'info'"
          >{{ scope.row.status? '已公开' : '未公开' }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="username" label="用户" width="80" />
      <el-table-column label="所属文章" width="100">
        <template slot-scope="scope">{{ scope.row.article>10? scope.row.article.substring(0, 10) + '...' : scope.row.article }}</template>
      </el-table-column>
      <el-table-column prop="time" label="时间" width="200" />
      <el-table-column fixed="right" label="操作" width="300">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button size="mini" type="primary" @click="handleReply(scope.$index, scope.row)">回复</el-button>
          <el-button
            size="mini"
            type="success"
            :disabled="scope.row.status? true : false"
            @click="handlePublic(scope.$index, scope.row)"
          >公开</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div v-show="!search" class="footer">
      <el-pagination
        background
        layout="prev, pager, next"
        :page-size="pagesize"
        :total="lists.length"
        @current-change="changeCurrent"
      />
    </div>

    <!-- 回复评论 -->
    <el-dialog title="回复评论" :visible.sync="replyInfo.dialogVisible">
      <div class="comment">
        <h3>{{ replyInfo.username }} :</h3>
        <p>{{ replyInfo.comment }}</p>
      </div>
      <div class="reply">
        <el-input
          v-model="replyInfo.reply"
          type="textarea"
          :autosize="{ minRows: 2, maxRows: 4}"
          placeholder="回复"
        />
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelReply">取 消</el-button>
        <el-button type="primary" @click="submitReply">回 复</el-button>
      </div>
    </el-dialog>
    <!-- 回复评论 结束 -->

    <!-- 编辑评论 -->
    <el-dialog title="编辑评论" :visible.sync="editInfo.dialogVisible">
      <div class="comment">
        <h3>{{ editInfo.username }} :</h3>
      </div>
      <div class="reply">
        <el-input
          v-model="editInfo.comment"
          type="textarea"
          :autosize="{ minRows: 2, maxRows: 4}"
          placeholder="回复"
        />
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelEdit">取 消</el-button>
        <el-button type="primary" @click="submitEdit">保 存</el-button>
      </div>
    </el-dialog>
    <!-- 编辑评论 结束 -->
  </div>
</template>

<script>
// import commentreply from "./components/commentReply";

export default {
  components: {
    // commentreply
  },
  data() {
    return {
      lists: [
        {
          comment: '1点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '2点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Jerry',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '3点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 1,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        },
        {
          comment: '点个赞，写得很棒，解决了困扰我很久的问题。',
          status: 0,
          username: 'Tom',
          article: '水调歌头',
          time: '2019-12-6 10:00'
        }
      ],
      multipleSelection: [],
      pagesize: 10,
      currentPage: 1,
      replyInfo: {
        index: 0,
        dialogVisible: false,
        username: '',
        comment: '',
        reply: ''
      },
      editInfo: {
        index: 0,
        dialogVisible: false,
        username: '',
        comment: ''
      },
      search: ''
    }
  },
  computed: {
    deleteCommentBtn() {
      // 删除按钮的禁用状态，未选中时为禁止状态
      if (this.multipleSelection.length !== 0) {
        return false
      }
      return true
    },
    tableData() {
      const search = this.search
      if (search) {
        return this.lists.filter(data => {
          return Object.keys(data).some(key => {
            return String(data[key]).toLowerCase().indexOf(search) > -1
          })
        })
      }
      return this.lists.slice(
        (this.currentPage - 1) * this.pagesize,
        this.currentPage * this.pagesize
      )
    }
  },
  created() {
    this.total = this.lists.length
  },

  methods: {
    toggleSelection(rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row)
        })
      } else {
        this.$refs.multipleTable.clearSelection()
      }
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    handleEdit(val) {
      // 编辑评论，跳转到编辑页面
      this.editInfo.username = this.lists[val].username
      this.editInfo.comment = this.lists[val].comment
      this.editInfo.index = val
      this.editInfo.dialogVisible = true
    },
    handleReply(val) {
      // 回复评论，调用组件
      this.replyInfo.username = this.lists[val].username
      this.replyInfo.comment = this.lists[val].comment
      this.replyInfo.index = val
      this.replyInfo.dialogVisible = true
    },
    handlePublic(val) {
      // 公开评论
      this.lists[val].status = 1
      this.$message({
        message:
          '成功公开评论: ' + this.lists[val].comment.slice(0, 10) + '...',
        type: 'success'
      })
    },
    handleDelete(val) {
      // 单个删除
      this.lists.splice(val, 1)
      this.$message({
        message: '已删除评论',
        type: 'success'
      })
    },
    deleteComment() {
      this.$confirm('此操作将删除选中评论, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          // 删除文件
          for (let i = 0; i < this.lists.length; i++) {
            if (this.multipleSelection.indexOf(this.lists[i]) > -1) {
              this.lists.splice(i, 1)
              i -= 1
            }
          }
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },
    changeCurrent(current) {
      this.currentPage = current
    },
    cancelReply() {
      this.replyInfo.reply = ''
      this.replyInfo.dialogVisible = false
    },
    submitReply() {
      this.replyInfo.dialogVisible = false
      this.replyInfo.reply = ''
      this.$message({
        message: '已回复用户' + this.replyInfo.username,
        type: 'success'
      })
    },
    cancelEdit() {
      this.editInfo.comment = ''
      this.editInfo.username = ''
      this.editInfo.dialogVisible = false
    },
    submitEdit() {
      this.editInfo.dialogVisible = false
      // 修改lists里面的数据
      this.lists[this.editInfo.index].comment = this.editInfo.comment
      this.editInfo.comment = ''
      this.editInfo.username = ''
      this.$message({
        message: '编辑成功',
        type: 'success'
      })
    }
  }
}
</script>

<style scoped>
.tag {
  margin-right: 2px;
}
div.optionTool {
  margin: 20px 0;
}
.footer {
  text-align: right;
  margin-top: 20px;
}
</style>
