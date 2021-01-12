<template>
  <div>
    <a-table :columns="columns" :data-source="data" size="small" bordered>
      <template slot="title" slot-scope="">
        <a-row>
          <a-col :span="3">
            <TeamSelector
              ref="teamSelector"
              v-on:selectedTeam="selectedTeam"
            />
          </a-col>
          <a-col :span="3">
            <OwnerSelector
              ref="ownerSelector"
              v-on:selectedOwner="selectedOwner"
            />
          </a-col>
          <a-col :span="16">
          </a-col>
          <a-col :span="2">
            <a-button @click="addDialogVisible=true" type="primary" size="small" icon="plus">添加</a-button>
          </a-col>
        </a-row>
      </template>
      <a slot="name" slot-scope="text">{{ text }}</a>
      <span slot="customTitle"><a-icon type="smile-o" /> Name</span>
      <span slot="tags" slot-scope="tags">
        <a-tag
          v-for="tag in tags"
          :key="tag"
          :color="
            tag === 'loser' ? 'volcano' : tag.length > 5 ? 'geekblue' : 'green'
          "
        >
          {{ tag.toUpperCase() }}
        </a-tag>
      </span>
      <span slot="action">
        <a-button @click="editDialogVisible=true" style="background-color: #faad14;" size="small" icon="edit">编辑</a-button>
        <a-divider type="vertical" />
        <a-button @click="deleteProjectHandle" type="danger" size="small" icon="delete">删除</a-button>
      </span>
    </a-table>
    <a-pagination
      v-model="pageNumber"
      :total="total"
      show-less-items
      show-size-changer
      @showSizeChange="onShowSizeChange"
    />
    <a-modal
      title="添加项目"
      width="400px"
      :visible="addDialogVisible"
      @ok="addProjectHandleOk"
      @cancel="addProjectHandleCancel"
    >
      <a-form :model="add" :label-col="{ span: 5 }" :wrapper-col="{ span: 17 }">
        <a-form-item label="团队名称">
          <a-input v-model="add.team" placeholder="请输入团队名称！" />
        </a-form-item>
        <a-form-item label="项目名称" size="small">
          <a-input v-model="add.project" placeholder="请输入项目名称！" />
        </a-form-item>
        <a-form-item label="负责人">
          <a-input v-model="add.owner" placeholder="请输入负责人名字！" />
        </a-form-item>
      </a-form>
    </a-modal>
    <a-modal
      title="编辑项目"
      width="400px"
      :visible="editDialogVisible"
      @ok="editProjectHandleOk"
      @cancel="editProjectHandleCancel"
    >
      <a-form :model="edit" :label-col="{ span: 5 }" :wrapper-col="{ span: 17 }">
        <a-form-item label="团队名称">
          <a-input v-model="edit.team" placeholder="请输入团队名称！" />
        </a-form-item>
        <a-form-item label="项目名称" size="small">
          <a-input v-model="edit.project" placeholder="请输入项目名称！" />
        </a-form-item>
        <a-form-item label="负责人">
          <a-input v-model="edit.owner" placeholder="请输入负责人名字！" />
        </a-form-item>
      </a-form>
    </a-modal>
  </div>
</template>

<script>
import TeamSelector from '~/components/TeamSelector.vue'
import OwnerSelector from '~/components/OwnerSelector.vue'

const columns = [
  {
    dataIndex: 'id',
    key: 'id',
    title: 'ID'
  },
  {
    title: '团队',
    dataIndex: 'team',
    key: 'team'
  },
  {
    title: '项目',
    dataIndex: 'project',
    key: 'project'
  },
  {
    title: '负责人',
    key: 'owner',
    dataIndex: 'owner'
  },
  {
    title: '创建日期',
    key: 'created',
    dataIndex: 'created'
  },
  {
    title: '更新日期',
    key: 'updated',
    dataIndex: 'updated'
  },
  {
    title: '操作',
    key: 'action',
    scopedSlots: { customRender: 'action' }
  }
]

export default {
  components: {
    TeamSelector,
    OwnerSelector
  },
  data () {
    return {
      data: [],
      team: '',
      owner: '',
      total: 0,
      pageNumber: 0,
      pageSize: 20,
      addDialogVisible: false,
      editDialogVisible: false,
      add: {
        team: '',
        project: '',
        owner: ''
      },
      edit: {
        team: '',
        project: '',
        owner: ''
      },
      columns
    }
  },
  mounted () {
    this.refresh()
  },
  methods: {
    onShowSizeChange (pageNumber, pageSize) {
      console.log(pageNumber, pageSize)
    },
    refresh () {
      const params = {
        limit: this.pageSize,
        offset: this.pageNumber * this.pageSize
      }
      if (this.team !== '') {
        params.team = this.team
      }
      if (this.owner !== '') {
        params.owner = this.owner
      }
      this.$axios
        .get('/api/v1/team/search', {
          params
        })
        .then((res) => {
          this.total = res.data.total
          this.data = res.data.data
          this.data = this.data.map((item) => {
            return Object.assign(item, { key: item.id })
          })
          console.log(this.data)
        })
        .catch((error) => {
          this.$message.error(error.response.data.message)
        })
    },
    selectedTeam (value) {
      this.team = value
      this.refresh()
    },
    selectedOwner (value) {
      this.owner = value
      this.refresh()
    },
    addProjectHandleCancel (e) {
      this.addDialogVisible = false
    },
    addProjectHandleOk () {
      this.$axios({
        url: '/api/v1/team/create',
        method: 'post',
        data: JSON.stringify(this.add),
        headers: {
          'Content-Type': 'application/json;'
        }
      }).then((res) => {
        this.add.team = {}
        if (res.data.status === 0) {
          this.refresh()
          this.$message.success(res.data.message)
          // 创建团队后下拉框不更新 #8 https://github.com/taoyanli0808/clover/issues/8
          this.$refs.teamSelector.getTeam()
          this.$refs.ownerSelector.getOwner()
        } else {
          this.$message.warning(res.data.message)
        }
      }).catch((error) => {
        this.$message.error(error.response.data.message)
      })
      this.addDialogVisible = false
    },
    editProjectHandleCancel (e) {
      this.editDialogVisible = false
    },
    editProjectHandleOk (e) {
      console.log(e)
      // this.editDialogVisible = true
      // this.edit.team = row.team
      // this.edit.project = row.project
      // this.edit.owner = row.owner
      // this.edit.id = row.id
    },
    deleteProjectHandle () {
      this.$confirm({
        title: '删除项目',
        content: '此操作将永久删除该项目, 是否继续？',
        onCancel () {},
        onOk () {}
      })
    }
  }
}
</script>
