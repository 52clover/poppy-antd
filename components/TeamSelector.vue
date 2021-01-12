<template>
  <div>
    <a-select
      v-model="team"
      @change="selectedTeam"
      style="width: 120px"
      size="small"
    >
      <a-select-option
        v-for="item in teams"
        :key="item.value"
        :label="item.label"
        :value="item.value"
      >
        {{ item.label }}
      </a-select-option>
    </a-select>
  </div>
</template>

<script>
export default {
  data () {
    return {
      team: '',
      teams: []
    }
  },
  mounted () {
    this.getTeam()
  },
  methods: {
    getTeam () {
      this.teams = []
      this.$axios({
        url: '/api/v1/team/aggregate',
        method: 'post',
        data: JSON.stringify({
          key: 'team'
        }),
        headers: {
          'Content-Type': 'application/json;'
        }
      }).then((res) => {
        for (const index in res.data.data) {
          this.teams.push({
            label: res.data.data[index],
            value: res.data.data[index]
          })
        }
      }).catch((error) => {
        this.$message.error(error.response.data.message)
      })
    },
    selectedTeam (value) {
      this.team = value
      this.$emit('selectedTeam', this.team)
    }
  }
}
</script>
