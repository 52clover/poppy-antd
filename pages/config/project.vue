<template>
  <div>
    <a-table :columns="columns" :data-source="data" bordered>
      <template slot="title" slot-scope="">
        <a-row>
          <a-col :span="6">
            <TeamSelector
              ref="teamSelector"
              v-on:selectedTeam="selectedTeam"
            />
          </a-col>
          <a-col :span="6">
            <OwnerSelector
              ref="ownerSelector"
              v-on:selectedOwner="selectedOwner"
            />
          </a-col>
          <a-col :span="6">
            col-6
          </a-col>
          <a-col :span="6">
            col-6
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
      <span slot="action" slot-scope="record">
        <a-button @click="add(record)" type="primary" size="small" icon="plus">添加</a-button>
        <a-divider type="vertical" />
        <a-button style="background-color: #faad14;" size="small" icon="edit">编辑</a-button>
        <a-divider type="vertical" />
        <a-button type="danger" size="small" icon="delete">删除</a-button>
      </span>
    </a-table>
    <a-pagination
      v-model="pageNumber"
      :total="total"
      show-less-items
      show-size-changer
      @showSizeChange="onShowSizeChange"
    />
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
    }
  }
}
</script>
